# HAProxy with Consul service discvoery

Example project that demonstrates using Consul service discovery with HAProxy.

## Setup

1. Call `vagrant up` from this directory to create the virtual machines:

  * Consul server
  * HAProxy
  * Web server running Consul agent

2. Log into the HAProxy VM:

   ```
   $ vagrant ssh haproxy
   ```

3. Use the HAProxy Data Plane API to register HAProxy as a Consul agent.
   Set :code:`address` to the IP address or DNS name of your Consul server.

   ```
   $ curl -u dataplaneapi:mypassword \
          -H 'Content-Type: application/json' \
          -d '{
                "address": "192.168.50.21",
                "port": 8500,
                "enabled": true,
                "retry_timeout": 10
              }' http://192.168.50.20:5555/v2/service_discovery/consul
   ```

   which returns:

   ```
   {"address":"192.168.50.21","enabled":true,"id":"1de47b93-0165-44c6-b575-818b786db51d","name":"consul","port":8500,"retry_timeout":10,"server_slots_base":10,"server_slots_growth_increment":10,"server_slots_growth_type":"linear","service-blacklist":null,"service-whitelist":null}
   ```

## How to register service

Call the `consul services register` command on your webserver:

```
$ consul services register /vagrant/web.json
```

The **web.json** file look like this:

```
{
  "service": {

    "name": "web",
    "port": 80
  }
}
```

You can register more services that have the name *web* and they will be put into the same
`backend` in HAProxy.

The HAProxy configuration is updated with the server:

```
backend consul-backend-192.168.50.21-8500-web
  server SRV_rfBd5 192.168.50.22:80 check weight 128
  server SRV_6ti2S 192.168.50.23:80 check weight 128
  server SRV_MtYvS 127.0.0.1:80 disabled weight 128
  server SRV_gD5xA 127.0.0.1:80 disabled weight 128
  server SRV_V0YU9 127.0.0.1:80 disabled weight 128
  server SRV_9zamp 127.0.0.1:80 disabled weight 128
  server SRV_ta7Z7 127.0.0.1:80 disabled weight 128
  server SRV_S575K 127.0.0.1:80 disabled weight 128
  server SRV_LkIZ9 127.0.0.1:80 disabled weight 128
  server SRV_PYkL1 127.0.0.1:80 disabled weight 128
```

You will need to add a `frontend` that routes to this `backend`.
