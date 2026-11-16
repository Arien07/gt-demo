# gt-demo

En fait, de nombreuses données sont non bornées (unbounded), car elles arrivent progressivement au fil du temps : les utilisateurs ont généré des données hier et aujourd’hui, et ils en généreront encore demain. Tant que votre entreprise reste en activité, ce processus ne prendra pas fin ; par conséquent, de tout point de vue significatif, le jeu de données ne sera jamais « complet »1. Ainsi, les programmes de traitement par lots doivent artificiellement découper les données en tranches de durée fixe, par exemple en traitant les données du jour à la fin de chaque journée, ou les données de l’heure à la fin de chaque heure. [guide.md](../../docs/guide.md)

## 🌐 Versions multilingues / Traductions

[Anglais](../../translations/en/README.md) | [Japonais](../../translations/ja/README.md)

***

Le problème du traitement par lots quotidien est que les changements dans les données d’entrée ne se répercutent sur les données de sortie qu’un jour plus tard, ce qui est bien trop lent pour de nombreux utilisateurs impatients. Pour réduire la latence, on peut exécuter le traitement plus fréquemment — par exemple, traiter les données de la seconde à la fin de chaque seconde — ou abandonner complètement les tranches de temps fixes et passer à un traitement continu, où chaque événement est traité immédiatement dès qu’il se produit. C’est là l’idée fondamentale du traitement des flux (stream processing).

En général, un « flux » désigne des données qui deviennent disponibles progressivement au fil du temps. Ce concept apparaît dans de nombreux endroits : stdin et stdout d’Unix, les listes paresseuses2 dans les langages de programmation, les API de système de fichiers (par exemple, FileInputStream de Java), les connexions TCP, l’audio et la vidéo transmis via Internet, etc.