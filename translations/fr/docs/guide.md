La cohérence linéarisable est facilement confondue avec la « sérialisation », car les deux noms semblent tous deux dire « qu'on peut arranger les choses dans un certain ordre ». Mais ce sont deux garanties complètement différentes, qu'il faut distinguer :

Sérialisation
La sérialisation (serializability) est une propriété d'isolation des transactions ; chaque transaction peut lire et écrire plusieurs objets (lignes, documents ou enregistrements). Elle garantit que le comportement des transactions équivaut à une exécution dans un certain ordre sériel : on exécute entièrement une transaction, puis entièrement la suivante, sans qu'elles s'entrelacent. Cet ordre sériel peut être différent de l'ordre dans lequel les transactions se sont réellement déroulées 9.

Cohérence linéarisable
La cohérence linéarisable est une garantie sur les lectures et écritures d'un registre (c'est-à-dire d'un objet unique). Elle ne regroupe pas les opérations en transactions, et ne peut donc pas empêcher des problèmes impliquant plusieurs objets tels que les « écarts d'écriture et les lectures fantômes ». Mais la cohérence linéarisable est une garantie de fraîcheur : si une opération s'est terminée avant qu'une autre ne commence, alors cette dernière doit observer un état au moins aussi récent que le précédent. La sérialisation n'a pas cette exigence ; elle autorise par exemple les lectures périmées 10.

```
upstream upserver_weighted {
    server 192.168.1.10:8080 weight=3;  # serveur haute performance
    server 192.168.1.11:8080 weight=2;  # serveur de performance moyenne
    server 192.168.1.12:8080 weight=1;  # serveur de performance standard
}

server {
    listen 80;
    server_name ms.com;
    
    location /api/questions/ {
        proxy_pass http://upserver_weighted;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Verrouillage et élection du leader
Un système utilisant la réplication à leader unique doit s'assurer qu'il n'y a réellement qu'un seul leader, et non plusieurs leaders simultanés (split brain). Une méthode d'élection du leader (leader election) consiste à utiliser un bail (lease) : chaque nœud qui démarre tente d'obtenir le bail, et celui qui y parvient devient le leader 17. Quelle que soit la façon dont le mécanisme sous-jacent est mis en œuvre, il doit satisfaire la cohérence linéarisable, et jamais deux nœuds différents ne doivent pouvoir obtenir le même bail simultanément.

Des services de coordination tels qu'Apache ZooKeeper 18, etcd, sont souvent utilisés pour implémenter des baux distribués et l'élection du leader. Ils s'appuient sur des algorithmes de consensus pour fournir des opérations linéarisables de manière tolérante aux pannes (ces algorithmes seront abordés plus loin dans ce chapitre). Pour implémenter correctement les baux et l'élection du leader, de nombreux détails subtils subsistent, comme le problème de la barrière (fencing) décrit dans la section « Verrous distribués et baux ». Des bibliothèques telles qu'Apache Curator encapsulent au-dessus de ZooKeeper des solutions idiomatiques de plus haut niveau, ce qui peut alléger ce travail ; mais au fond de toutes ces tâches de coordination se trouve toujours un service de stockage à cohérence linéarisable.