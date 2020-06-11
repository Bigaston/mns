# MNS Protocol
Définition du protocole d'échange sécurisé MNS

## Format d'adresse
{nom-d'utilisateur}@{domaine}

## DNS
L'adresse du serveur de reception des messages doit être spécifiée dans un champs TXT de votre nom de domaine.
Il doit avoir la forme "mns='{adresse_du_serveur}'"

## Chemin et réponses
**GET /keys** : Renvoit la liste des algorithmes de chiffrement supportés par le serveur, par ordre de prioritée. Chaque serveur doit au moins supporter le chiffrement RSA.  
Réponse : `200`
```
{
    "algo": ["rsa"]
}
```

**GET /keys/@[Utilisateur]/[algo]** : Renvoit la clé publique de *Utilisateur* pour l'algorythme *algo*  
Réponse : `400` L'algorythme utilisé n'est pas supporté par le serveur  
Réponse : `404` L'utilisateur n'est pas trouvé  
Réponse : `200`
```
{
    "user": "un_utilisateur",
    "algo": "nom_de_l'algo",
    "public_key": "LaCléPubliqueDeL'Utilisateur"
}
```

**POST /msg/** : Envoit d'un message au serveur  
Body :
```
{
    "user": "un_utilisateur",
    "from": "adresse_expediteur@undomaine.com",
    "to": ["liste@example.com", "de@example.com", "destinataires@example.com"]
    "algo": "nom_de_l'algo",
    "msg": "LeContenutDuMessageChiffre",
    "hash": "LeHashDuMessage"
}
```
Hash : Contient le hash du message chiffré, chiffré à son tour par la clé privée de l'expéditeur. Utilisation de l'algorythme HS256  
msg : Contient le message chiffré par la clé publique du destinataire

Réponse : `400` L'algorythme utilisé n'est pas supporté par le serveur  
Réponse : `404` L'utilisateur n'est pas trouvé  
Réponse : `200` Message reçu