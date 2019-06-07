---
date: 2019-06-05T21:20:10+02:00
title: "Introduzione a Docker"
summary: "Una breve introduzione all'utilizzo di Docker per la Containerizzazione dei servizi"
image: "/images/1/header.jpg"
tags: ["docker", "linux", "devops"]

---

## Virtualizzazione tramite Container con Docker

Fino a qualche anno fa l'infrastruttura dedicata allo sviluppo Web era piuttosto standard: 

Un **server** (solitamente Linux) su cui veniva installato un **WebServer** (Apache), **l'interprete** (ad esempio PHP) e un **Database** (e.g. MySql), tutto piuttosto semplice e standard.

Col passare degli anni e con l'arrivo di nuove tecnologie, linguaggi, framework e tool di sviluppo ogni applicazione Web può avere requisiti decisamente diversi da tutte le altre.

Spesso si utilizza un'architettura a micro-servizi ciascuno dei quali ha i suoi requisiti, ogni sviluppatore ha un ambiente di lavoro che può essere diverso dai propri colleghi e dal server di produzione. 
È infine possibile che uno sviluppatore lavori su due o più microservizi, uno dei quali richiede PHP 5.6 e  l'altro basato su PHP 7.3, e magari questo sviluppatore lavora su un Mac mentre il server di produzione è Ubuntu 18.04.

Bene, penso si sia capito che il contesto in cui lavoriamo oggi è molto diverso da prima e molto più frammentato.

Ci serve qualcosa per garantirci che stiamo lavorando in un ambiente equivalente alla produzione e a quello dei nostri colleghi.

## Come si può risolvere questo problema?

Inizialmente abbiamo provato a risolverlo con le VM (Virtual Machines) e con degli script custom di installazione. Un esempio è il classico Script che esegue qualche **apt-add-repository** ed una decina di **apt install** da lanciare subito dopo aver installato la VM.
In questo modo possiamo garantire che la macchina virtuale avrà tutti i pacchetti necessari correttamente installati.
### Come funziona una Virtual Machine

### ![Virtual Machines](/images/1/vm.png)

Lo schema qua sopra utilizza un Hypervisor (e.g VMWare o VirtualBox), un software che riesce a gestire delle "macchine virtuali" con le quali condivide risorse hardware, e sulle quali consente di installare un sistema operativo.

Gli Hypervisor possono essere Native (installati direttamente sulla macchina) oppure Hosted (installati su un normale sistema operativo).

Questa soluzione funziona ma ha tre limiti:

1. Consumo di risorse elevato, ogni macchina virtuale deve gestire un intero sistema operativo.
2. Difficoltà di modifica e di replica, ogni volta che vogliamo aggiungere un pacchetto dovremo comunicarlo a tutti i colleghi che dovranno scaricare la stessa versione di quel pacchetto.
3. L'installazione/configurazione di un sistema operativo su una macchina virtuale è piuttosto lenta.
4. Sandboxing, magari vorrei che ogni servizio potesse accedere solo ad alcuni altri servizi e solo ai suoi files.

In casi come il mio, lavorando per diverse aziende e su diversi progetti dovrei avere una o più VM per ogni cliente, una soluzione antipatica, occuperei troppo spazio e farei fatica a gestire memoria/CPU.


Un passo successivo è stato quello di utilizzare software come Vagrant e Ansible, dei tool abbastanza semplici da utilizzare che permettono di scrivere un file di configurazione che indichi il sistema operativo e tutte le dipendenze del nostro software, in modo molto dettagliato (arrivando perfino alla definizione tramite configurazione di utenti e permessi).

Con questa soluzione, a fronte di un cambiamento, sarà sufficiente aggiornare il file di configurazione di Vagrant/Ansible e ricreare la macchina.

Questo risolve alcune delle criticità evidenziate prima, resta il problema della "pesantezza" dell'intero sistema operativo e quello del sandboxing.


## Docker

![Docker Container](/images/1/container.png)

### Cos'è un Container

Un Container è un "Unità di software standardizzata" ovvero un software e tutte le sue relative configurazioni, indipendente dal contesto esterno.

Un container può essere avviato su un laptop Windows e su un Server Ubuntu ed essere esattamente identico, quindi funzionare allo stesso modo.
 e differenze tra le varie piattaforme saranno tutte gestite in modo trasparente da Docker.

Questo ci consente di pensare alla nostra architettura tralasciando il sistema operativo.
Possiamo definire tutti i componenti che ci servono per far funzionare la nostra applicazione quindi, tramite un solo comando, tutta l'infrastruttura verrà preparata ed avviata (in pochissimo tempo).
Questa infrastruttura è replicabile su qualsiasi computer sepmlicemente condividendo il file di configurazione.

Ti sembra interessante? Continua a leggere perchè adesso iniziamo ad usare Docker.

#### Installiamo Docker

Per prima cosa è necessario installare docker come applicazione sul proprio computer e (almeno in ambienti Linux e Mac) aggiungere il proprio utente al gruppo "docker".

Per l'installazione vi rimando alla documentazione ufficiale che è molto chiara e soprattutto sempre aggiornata: [Documentazione Ufficiale](https://docs.docker.com/install/).

#### Come si ottiene un Container?

Per ottenere un container bisogna prima definire un Immagine, ovvero la lista di cose che servono perchè il nostro Container funzioni.

Ad esempio, ci servirà un sistema operativo e il software che vogliamo venga eseguito all'interno di quel Container.

Ma aspetta, se devo installare un sistema operativo nel Container allora perdo tutti i benefici di utilizzare Docker, avrò ancora GB di spazio utilizzati e memoria occupata, giusto?
Sbagliato! Il sistema operativo di partenza può essere ad esempio una distribuzione chiamata Alpine, ovvero un OS basato sul kernel Linux che pesa 5MB!
Inoltre alcuni provider di servizi realizzano ad-hoc delle immagini super leggere che espongono soltanto una bash e la loro applicazione.

#### Come si crea un immagine?

Per creare un immagine ci sono due modi:

1. Creare un Dockerfile, contenente una configurazione definita da noi, che indichi cosa deve "contenere" il Container.
2. Utilizzare un Dockerfile già fatto da qualcuno, per questo esiste una Repository ufficiale chiamata [Docker Hub](https://hub.docker.com/)


Nel 90% dei casi esisterà già su Docker Hub un'immagein che fa al caso nostro, in caso contrario potremo creare noi la nostra definizione.


### Creiamo un Dockerfile


Tutta la documentazione sui comandi da utilizzare per comporre il nostro Dockerfile è disponibile al sito https://docs.docker.com/engine/reference/builder/ dove ogni direttiva è spiegata nel dettaglio.

In questo caso creiamo l'immagine di un Container che esegue un comando di *ping* verso sè stesso.

Per farlo dobbiamo creare un file chiamato "Dockerfile", senza estensione, quindi compilarlo con alcune direttive, la prima delle quali è FROM, e indica un immagine di partenza.

Nel nostro caso utilizziamo proprio **alpine**, quindi specifichiamo tramite CMD il comando che vogliamo che venga eseguito quando il container verrà avviato.

Una volta salvato il Dockerfile dobbiamo "compilarlo", tramite il comando

```
docker build -l ping-test .
```

-l serve per indicare un nome che vogliamo dare alla nostra immagine, per poterla riconoscere in seguito.

Abbiamo definito la nostra prima immagine Docker!

A questo punto per lanciarla ed avviare finalmente il container dobbiamo utilizzare il comando

```
docker run --name my_ping_container -d ping-test
```


![docker build](/images/1/docker-build.png)

**-d** indica che vogliamo lanciare il Container in modalità detached, ovvero, vogliamo riprendere il controllo del terminale dopo aver avviato il Container.

**--name** invece serve per dare un nome al nostro Container, come abbiamo fatto col -l nelle immagini. Altrimenti nei comandi successivi dovremo riferirci a questo container tramite il suo ID autogenerato (non molto comodo da ricordare).

Ora il nostro Container è attivo e sta già pingando, per verificarlo possiamo lanciare 

```
docker logs my_ping_container
```
 
un utile comando per vedere i log di esecuzione di un container, dovremmo vedere l'output del ping.

Per ottenere una lista di tutti i container attivi utilizziamo:

```
docker ps
```

![docker ps](/images/1/docker-ps.png)

Per ciascun Container vediamo un ID autogenerato, il nome dell'immagine di riferimento, il comando che viene lanciato all'avvio, l'uptime e lo status, le porte aperte verso la macchina HOST e infine il nome del Container.

Fino a qui tutto fantastico, ma un'applicazione non è così semplice: spesso ci servirà un database, un webserver ed un runtime, inoltre tutti e tre questi servizi dovranno poter comunicare fra di loro.

### Docker Compose 

Per connettere fra di loro più Container di Docker ci viene in soccorso `docker-compose` grazie al quale possiamo scrivere un file di configurazione che descriva la "rete" di Container che vogliamo realizzare, lui si preoccuperà di costruire l'infrastruttura necessaria per mettere in comunicazione tutti i Container.

#### Come si realizza un applicazione composta da più Servizi

Creiamo un file chiamato `docker-compose.yml` e compiliamolo come segue:

```
version: '3'
services:
  webapp:
    image: nginx:latest
    ports:
      - 10082:80
    volumes:
     - ./site.conf:/etc/nginx/conf.d/mysite.conf
    volumes_from:
      - phpfpm
    links:
      - phpfpm:phpfpm
    environment:
      FASTCGI_APP_ENV: app.dev
  phpfpm:
    image: php:7.3-fpm-stretch
    volumes:
      - ./:/code
  postgres:
    image: postgres:9.6.3
    ports:
      - '15432:5432'
    volumes:
      - ./_pgdata:/var/lib/postgresql/data:rw
  memcached:
    image: memcached:alpine
```

Questo esempio contiene una buona parte delle istruzioni rilevanti che ci serviranno normalmente.

Analizziamolo!
**version** indica la versione di docker-compose, è importante assicurarsi di utilizzare la versione corretta per evitare problemi con la sintassi.

**services** indica l'inizio della lista dei servizi, segue una lista di servizi, a cui potremo dare un nome a scelta, in questo caso **web**, **phpfpm**, **postgres** e **memcached**.

**web** sarà un servizio basato su un Dockerfile che viene ricercato e "buildato" a partire dalla directory corrente (`build: .`), questo servizio esporrà verso l'esterno la porta 8080, rimappandola sulla porta interna 80, tutti i file presenti nel percorso corrente saranno montati nella cartella /code del container, inoltre il container avrà una rotta DNS verso il container "redis" tramite l'hostname "redis".

**phpfpm** contiene il runtime php-fpm effettivo, che dovrà interpretare gli script.

**postgres** è il nostro database, accessibile dagli altri container sulla porta 5432, mentre da noi esternamente
tramite la porta 15432.

**memcached** è il servizio di cache in memoria che utilizziamo per migliorare le performance.

Vediamo qui sotto un'esempio di come avviare questa infrastruttura.

```
docker-compose up -d
```

È utile ricordare che i quattro servizi saranno deployati su 4 host virtuali diversi, che però si conoscono, e possono comunicare, per ognuno gli altri nodi saranno identificati dal loro nome, ad esempio se dal codice PHP (che sarà interpretato dal container "phpfpm") volessi accedere al database dovrei farlo cercando l'host chiamato "postgres".

Dall'esterno potremo parlare con l'applicazione tramite la porta 10082, che si rimapperà sulla locale 80, quindi il nostro "site.conf" che avremo nella cartella locale ma che sarà mappato tramite la direttiva **volumes** nel container webapp al percorso /etc/nginx/conf.d dovrà ascoltare sulla porta 80, ci penserà docker-compose a ridirezionare le richieste da una porta all'altra.

Inoltre dovremo configurare il nostro virtual-host nginx per chiedere al PHP di interpretare le richieste, per farlo accederà al PHP tramite l'hostname "phpfpm" sulla porta 9000 (la porta l'ho trovata nella documentazione).
Ecco un esempio di configurazione da aggiungere al nostro site.conf.

```
    fastcgi_pass   phpfpm:9000;
    fastcgi_split_path_info ^(.+\.php)(/.*)$;
    include fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param  APP_ENV FASTCGI_APP_ENV;
```


Ultima nota: siccome i Container non hanno memoria ogni volta che avviamo un nuovo Container tutti i file presenti nel filesystem sono inizializzati a quelli definiti nella sua immagine.

Per questo motivo dobbiamo utilizzare la direttiva **volumes** per mappare verso l'esterno la cartella /var/lib/postgresql/data del nostro Container postgres, in questo modo potremo rimuovere e ricreare il Container ma i dati, essendo salvati sul filesystem della macchina host, verranno ripristinati ad ogni avvio.

### Networks

Se nello stesso server host abbiamo più applicazioni basate su servizi e vogliamo che queste condividano un servizio, come possiamo fare?
Semplice, è possibile creare una "rete" virtuale di Docker, e indicare al Container in questione di utilizzare quella rete, quindi diremo ai nostri docker-compose.yml delle due applicazioni di utilizzare anche quella rete.

Vediamo un piccolo esmpio pratico della sintassi:

```
version: '3'
services:
  postgres:
    image: postgres:9.6.3
    ports:
      - '15432:5432'
    volumes:
      - ./_pgdata:/var/lib/postgresql/data:rw
    networks:
      - default
      - my_custom_network
networks:
  my_custom_network:
    external:
      name: my_custom_network
```

In questo caso stiamo dicendo al Container di Postgres di utilizzare la rete default (quella che viene creata automaticamente per ogni docker-compose file e che ciascun container conosce) e di utilizzare una rete chiamata "my_custom_network" che (come dichiaro in fondo) è esterna, quindi deve pre-esistere al momento dell'avvio di questo docker-compose.


### Deploy in Produzione

L'ambiente di produzione spesso girerà su porte diverse o avrà alcuni servizi non esposti, cosa che magari in sviluppo vogliamo modificare.
Per questo motivo possiamo utilizzare due diversi file docker-compose.yml.

Solitamente io utilizzo in sviluppo un file chiamato docker-compose-dev.yml, e lascio quello standard per le configurazioni di produzione.

Per poter utilizzare un docker-compose diverso da quello standard è necessario che nella nostra cartella sia presente un file chiamato .env che contiene alcune variabili d'ambiente per l'esecuzione degli script.
Dovremo quindi aggiungere la costante COMPOSE_FILE al nostro file .env. 

```
COMPOSE_FILE=docker-compose-dev.yml 
```


Da questo momento, solo in questa cartella, il comando `docker-compose up -d` utilizzerà il compose-file di sviluppo, mentre in produzione il file .env non sarà presente quindi il compose file sarà quello "ufficiale".

Ricordiamoci di aggiungere al **.gitignore** il nostro file .env (che potrebbe essere diverso per ciascuno sviluppatore).

Una cosa fondamentale da fare in produzione sarà utilizzare la direttiva **restart** del docker-compose.
Questa direttiva indica se e quando il container deve essere riavviato, i valori ammessi sono "always", "never", "unless-stopped".
Always indica che all'avvio di docker il container deve essere attivato sempre, intuitivamente il valore "never" indica che non deve essere attivato.
È interessante "unless-stopped", indica di riavviare sempre il container, a meno chè questo non sia stato fermato a mano (tramite un compado `docker stop container_name`).

In produzione probabilmente vorrete utilizzare restart: unless-stopped per i vostri container, così da assicurarvi che anche in caso di riavvio del server il container sarà subito attivo.

### Conclusione

Con questo abbiamo concluso questa breve panoramica di come si può utilizzare Docker per migliorare la gestione della nostra infrastruttura basata sui servizi.

Come al solito vi invito a leggere la documentazione ufficiale, molti comandi e molte direttive non sono spiegate qui per brevità, però con queste conoscenze potrete certamente iniziare a muovere i primi passi nel mondo dei Container di Docker per sperimentare da subito le sue potenzialità.

Questa introduzione è stata utilizzata anche per prepararmi al talk di CremaWeb che terrò il prossimo 20 Giugno a Crema.

Per ulteriori dettagli in merito vi segnalo la pagina di CremaWeb e la nostra pagina di Meetup.

[Meetup - Iscriviti all'evento](https://www.meetup.com/CremaWeb/events/262018721/)

[CremaWeb - La Community](https://www.cremaweb.org)

