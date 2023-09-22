# DOCKER NETWORK
## Mise en réseau dans docker-compose

### Spécifier des réseaux personnalisés

Au lieu d'utiliser simplement le réseau d'applications par défaut, vous pouvez spécifier vos propres réseaux avec la clé des réseaux de niveau supérieur. Cela vous permet de créer des topologies plus complexes et de spécifier des pilotes et des options réseau personnalisés. Vous pouvez également l'utiliser pour connecter des services à des réseaux créés en externe qui ne sont pas gérés par Compose.

#### Réseau générique - driver=bridge
```
networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    driver_opts:
      foo: "1"
      bar: "2"
```

#### Réseau personnalisé - driver=bridge
```
networks:
  frontend:
    name: frontend
    driver: bridge
    ipam:
      config:
        - subnet: "172.19.0.0/16"
          gateway: "172.19.0.1"
    driver_opts:
      com.docker.network.bridge.default_bridge: "false"
      com.docker.network.bridge.enable_icc: "true"	  
      com.docker.network.bridge.enable_ip_masquerade: "true"
      com.docker.network.bridge.host_binding_ipv4: "0.0.0.0"
      com.docker.network.bridge.name: "frontend"
      com.docker.network.driver.mtu: "1500"
```

### Utiliser un réseau préexistant

Si on souhaite que nos conteneurs rejoignent un réseau préexistant, utilisez l'option externe :

Prérequis :

Il faut au préalable créer le réseau via docker-cli (ligne de commande) :
```
docker network create --driver=bridge --subnet=172.19.0.0/16 --gateway=172.19.0.1 frontend
```

Dans le docker-compose.yaml :

```
networks: 
  frontend: 
    external: true
```

ou :

```
networks:
  frontend:
    name: frontend
    external: true
```

Au lieu d'essayer de créer un réseau appelé [projectname]_default, docker-compose recherche un réseau appelé my-pre-existing-network et y connecte les conteneurs de notre application.