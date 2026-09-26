Odoo 19
=========

Version dockerisée d'Odoo 19 Community. Rien à installer sur votre machine :
deux commandes, et vous avez Odoo.

A dockerized Odoo 19 Community. Nothing to install on your machine: two
commands and Odoo is running.

[Français](#français) · [English](#english)

---

# Français

## Prérequis

Docker, et Docker Compose v2 — la commande `docker compose`, sans tiret.

**Windows et macOS** : installez [Docker Desktop](https://www.docker.com/products/docker-desktop/)
et lancez-le. L'icône baleine doit être présente dans la barre des tâches
avant d'aller plus loin.

**Linux (Ubuntu, Debian)** :

```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

Déconnectez-vous et reconnectez-vous, sinon chaque commande réclamera `sudo`.

**Vérifiez** :

```bash
docker --version
docker compose version
```

Deux numéros s'affichent : vous êtes prêt. Si la seconde commande échoue,
votre Docker est trop ancien — mettez-le à jour.

## Installation

```bash
git clone https://github.com/esacodeorg/docker-odoo-19.git
cd docker-odoo-19
```

Une seule fois.

## Démarrer

```bash
docker compose up -d --build
```

La première fois, comptez quelques minutes : Docker télécharge l'image
Odoo 19 et PostgreSQL 17. Ensuite, c'est immédiat.

```bash
docker compose ps
```

Les deux conteneurs doivent tourner.

## Ouvrir Odoo

Rendez-vous sur **http://localhost:8069**.

Au premier lancement, Odoo demande de créer une base de données.

**Important : nommez-la `odoo_19`.** Le fichier `odoo/config/odoo.conf`
contient `dbfilter = odoo_19` ; une base portant un autre nom existe bien,
mais reste invisible. Pour travailler avec plusieurs bases, commentez cette
ligne puis `docker compose restart web`.

## Ajouter vos modules

Déposez-les dans `odoo/addons/` — le dossier est monté dans le conteneur sur
`/mnt/extra-addons`.

```bash
docker compose restart web
```

Puis, dans Odoo : activez le mode développeur, allez dans **Applications**,
cliquez sur **Mettre à jour la liste des applications**, et installez le vôtre.

## Au quotidien

```bash
docker compose stop          # arrêter
docker compose start         # redémarrer
docker compose logs -f web   # suivre les journaux
```

Tout supprimer, bases et images comprises :

```bash
docker compose down -v --rmi all
```

## Où vivent vos données

Le service `db` n'a pas de volume nommé : PostgreSQL écrit dans un volume
anonyme créé par Docker.

`docker compose stop` puis `start` conserve tout. En revanche
`docker compose down` supprime les conteneurs et **détache** ce volume : au
démarrage suivant Docker en crée un neuf, et votre base semble avoir disparu.
Elle est toujours sur le disque, mais orpheline.

Retenez : pour une pause, `stop`. `down` seulement quand vous voulez
réellement repartir de zéro.

## Si ça coince

**Le port 8069 est déjà pris.** Un autre Odoo tourne sur votre machine.
Changez le port publié dans `docker-compose.yml` : `"8169:8069"`, puis
`docker compose up -d`.

**Nom de conteneur déjà utilisé.** Plusieurs dépôts de cette famille peuvent
partager un `container_name` (les versions 14, 15 et 16 utilisent toutes
`postgres_12_server`). N'en faites tourner qu'un à la fois, ou renommez le
conteneur dans `docker-compose.yml`.

**La liste des bases est vide, ou Odoo répond "Database not found".**
Votre base ne s'appelle pas `odoo_19` — voir plus haut.

**Odoo ne répond pas alors que le conteneur tourne.**

```bash
docker compose logs web | tail -30
```

**`docker compose` n'existe pas.** Votre Docker est antérieur à la version 2.
Mettez-le à jour ; l'ancien `docker-compose` avec un tiret n'est plus
maintenu.

## Ce qu'il y a dans le dépôt

| Chemin | Contenu |
|---|---|
| `docker-compose.yml` | Odoo (8069) et PostgreSQL 17 |
| `odoo/Dockerfile` | notre couche, `FROM odoo:19` |
| `odoo/config/odoo.conf` | la configuration utilisée par le conteneur |
| `odoo/odoo.conf.example` | la même, vierge, pour repartir proprement |
| `odoo/addons/` | **vos modules** |

Les modules de base d'Odoo — `base`, `sale`, `stock` et les autres — sont déjà
dans l'image. Vous n'avez jamais à les copier.

---

# English

## Requirements

Docker, and Docker Compose v2 — the `docker compose` command, no hyphen.

**Windows and macOS**: install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
and start it. The whale icon must be in the taskbar before you go further.

**Linux (Ubuntu, Debian)**:

```bash
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

Log out and back in, otherwise every command will ask for `sudo`.

**Check**:

```bash
docker --version
docker compose version
```

Two version numbers: you are ready. If the second command fails, your Docker
is too old — update it.

## Install

```bash
git clone https://github.com/esacodeorg/docker-odoo-19.git
cd docker-odoo-19
```

Once only.

## Start

```bash
docker compose up -d --build
```

The first run takes a few minutes: Docker downloads the Odoo 19 image and
PostgreSQL 17. After that it is instant.

```bash
docker compose ps
```

Both containers should be up.

## Open Odoo

Go to **http://localhost:8069**.

On first launch Odoo asks you to create a database.

**Important: name it `odoo_19`.** `odoo/config/odoo.conf` sets
`dbfilter = odoo_19`; a database with any other name does exist, but stays
invisible. To work with several databases, comment that line out, then
`docker compose restart web`.

## Add your modules

Drop them in `odoo/addons/` — the folder is mounted in the container at
`/mnt/extra-addons`.

```bash
docker compose restart web
```

Then, in Odoo: turn on developer mode, go to **Apps**, click **Update Apps
List**, and install yours.

## Day to day

```bash
docker compose stop          # stop
docker compose start         # start again
docker compose logs -f web   # follow the logs
```

Remove everything, databases and images included:

```bash
docker compose down -v --rmi all
```

## Where your data lives

The `db` service has no named volume: PostgreSQL writes into an anonymous
volume created by Docker.

`docker compose stop` then `start` keeps everything. However
`docker compose down` removes the containers and **detaches** that volume: on
the next start Docker creates a fresh one and your database seems to be gone.
It is still on disk, but orphaned.

Remember: to pause, use `stop`. Use `down` only when you really want a clean
slate.

## Troubleshooting

**Port 8069 is already in use.** Another Odoo is running on your machine.
Change the published port in `docker-compose.yml` to `"8169:8069"`, then
`docker compose up -d`.

**Container name already in use.** Several repositories in this family may
share a `container_name` (versions 14, 15 and 16 all use
`postgres_12_server`). Run only one at a time, or rename the container in
`docker-compose.yml`.

**The database list is empty, or Odoo says "Database not found".** Your
database is not named `odoo_19` — see above.

**Odoo does not answer although the container is running.**

```bash
docker compose logs web | tail -30
```

**`docker compose` does not exist.** Your Docker predates version 2. Update
it; the old hyphenated `docker-compose` is no longer maintained.

## What is in the repository

| Path | Contents |
|---|---|
| `docker-compose.yml` | Odoo (8069) and PostgreSQL 17 |
| `odoo/Dockerfile` | our layer, `FROM odoo:19` |
| `odoo/config/odoo.conf` | the configuration used by the container |
| `odoo/odoo.conf.example` | the same file, untouched, to start over |
| `odoo/addons/` | **your modules** |

Odoo's own modules — `base`, `sale`, `stock` and the rest — are already in the
image. You never have to copy them.

---

## La famille / The family

| | | |
|---|---|---|
| [docker-odoo-14](https://github.com/esacodeorg/docker-odoo-14) | [docker-odoo-15](https://github.com/esacodeorg/docker-odoo-15) | [docker-odoo-16](https://github.com/esacodeorg/docker-odoo-16) |
| [docker-odoo-17](https://github.com/esacodeorg/docker-odoo-17) | [docker-odoo-18](https://github.com/esacodeorg/docker-odoo-18) | [docker-odoo-19](https://github.com/esacodeorg/docker-odoo-19) |
| [docker-odoo-20](https://github.com/esacodeorg/docker-odoo-20) | | |

## Crédits / Credits

Maintenu par **esacode — solutions numériques**
<https://www.esacode.org/erp-services/>

Contributions : <https://github.com/esaCodeBJ>
