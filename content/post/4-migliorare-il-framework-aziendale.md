---
title: "Migliorare il Framework aziendale"
date: 2019-08-08T13:31:17+02:00
summary: "Il percorso che ho seguito per migliorare la piattaforma di sviluppo che uso al lavoro"
image: "/images/4/header.jpg"
tags: ["php", "symfony", "silex", "framework"]
---

In questi giorni, sfruttando la relativa calma di Agosto, mi sto concentrando sull'aspetto *ricerca* del mio lavoro ed ho riflettuto sul framework proprietario NEALIS che al lavoro utilizziamo per sviluppare le nostre applicazioni Web.

Abbiamo sostanzialmente due problemi che sono sicuramente diffusi anche in altre aziende:

1. Il nostro Framework si basa su una libreria ormai deprecata (Silex 1.0).
2. Aggiornare i progetti dei clienti all'ultima release del Framework è sempre problematico.

Entro nel merito di ciascuno dei problemi:

#### Problema 1
Silex 1.0 è stato rilasciato nel 2010 e nel 2016 è uscita la versione 2.0. La sfortuna è che avevamo appena finito di costruirci sopra un framework e migrarlo era impensabile).

Nel 2018 è stato abbandonato in favore di Symfony 4, finalmente più leggero e grazie a Flex molto più facile da utilizzare.
Flex è uno strumento che semplifica l'installazione di applicazioni Symfony, semplifica la ricerca di pacchetti da composer, esegue script al termine di installazioni e automatizza alcune parti di configurazione.

La nostra architettura è quindi basata su un Framework che a breve compirà 10 anni e che non è più aggiornato in nessun modo dal 2016, questo ci impedisce di utilizzare le migliori tecnologie disponibili che vengono spesso sviluppate con un occhio di riguardo a framework come Laravel o Symfony.


#### Problema 2
Ogni volta che aggiorniamo un progetto da un cliente ci si pone il problema "ma aggiorniamo anche la libreria?".
Ogni nostro progetto si basa su un Framework interno che contiene **TUTTO** quello che ci serve per sviluppare i nostri progetti: dall'autenticazione AS400 a quella Postgres, la gestione di utenti/ruoli, le connessioni a Database, la gestione delle Route, il Logging ecc...

Abbastanza spesso ci capita di correggere o modificare questa libreria.
Quando poi torniamo a lavorare da un cliente ci poniamo il problema se sia il caso o meno di portargli in casa tutte le migliorie fatte nella libreria, alcune potrebbero servirgli (soprattutto i Bugfix) ma spesso non tutte sono davvero utili.

A volte queste modifiche rompono la retro-compatibilità e spesso solo per una piccola modifica alla libreria che ci torna utile ne dobbiamo importare 10 una delle quali ci costringe ad adeguare il progetto.
La seccatura è che fare questi adeguamenti richiede tempo e il cliente fatica a percepire il vantaggio di adeguare il framework: per lui il progetto funzionava anche prima.

Ultimo problema: abbiamo detto il nostro Framework contiene TUTTA la logica che ci può servire per i progetti, ma nel 99% dei casi molta di questa logica non ci serve proprio per **tutti** i progetti (e.g. tutta la gestione di AS400 non serve a niente in progetti dove lavoriamo solo con Postgres.
Questo fa sì che tutti i nostri progetti siano piuttosto pesanti e pieni di codice inutilizzato.

### Qual è il nucleo del problema?
Secondo me il problema risiede nell'aver tenuto tutta la logica in una singola libreria, che ci costringe quindi ad aggiornare in blocco e a tenere la libreria compatibile con ogni ambiente possibile complicandoci poi sviluppo di ciascun progetto.


### Come fare per risolvere questo problema?
Penso che la soluzione migliore sia spacchettare il Framework in tante piccole librerie che siano minimamente interconnesse e che ci permettano di utilizzarle nei nostri progetti solo quando serve.


### Esempio pratico

- Rilasciamo un progetto dal cliente il 1 Gennaio.
- Al 1 Febbraio modifichiamo la libreria del Logger introducendo una Breaking Change (i progetti che vogliono utilizzare il nuovo Logger devono adeguarsi modificando ad esempio tutti i Repository di lettura da Database).
- Al 1 Marzo introduciamo una modifica che velocizza le connessioni a Postgres.

Con il framework unico le due modifiche sarebbero nello stesso progetto, quindi per avere i benefici di performance su Postgres saremo costretti ad aggiornare anche la parte relativa al Logger, quindi a modificare il progetto.

Con la nuova gestione a librerie potremo scegliere arbitrariamente cosa aggiornare e quando, in questo modo potremo aggiornare solo la parte di accesso a Db, senza essere costretti ad aggiornare il Logger con il relativo costo di adeguamento dei progetti dove non è strettamente necessario farlo.

e.g.
```
composer update nealis/database
```
sostitusisce il vecchio
```
composer update nealis/library
```
che avrebbe aggiornato sia la parte database sia quella logger.

### Testing
Questo risultato è figlio del famoso SRP - Single Responsibility Principle, valido per la programmazione ad oggetti ma anche in questo caso trovo sia vero: "*ogni libreria deve avere una sola responsabilità, ovvero fare una cosa sola*".

Questa condizione ci semplificherà la vita in fase di scrittura dei test, dovremo infatti preoccuparci di testare una cosa sola alla volta.


### Open Source
Queste nuove librerie che **ho appena cominciato a sviluppare** saranno completamente Open Source. Questo perchè sono fermamente convinto che pubblicarle non rappresenti un problema. 

Trovo estremamente improbabile che un potenziale cliente si rivolga ad un altro fornitore che utilizza le nostre librerie piuttosto che a noi; allo stesso tempo potremmo avere qualche Contributor esterno che ci aiuta ad avere del codice migliore, a beneficio nostro e dei nostri clienti.


#### Conclusione
Se anche tu hai questo genere di problemi il mio consiglio è quello di trovare il tempo di riflettere sulla causa prinicpale e poi di **agire**, il problema non si risolverà da solo, anzi peggiorerà continuamente col tempo. Trova un modo di farlo percepire al tuo responsabile (o ai tuoi clienti se sei un Freelancer) e buttati, sarà sicuramente un percorso formativo per te e vantaggioso per i tuoi colleghi e clienti.
