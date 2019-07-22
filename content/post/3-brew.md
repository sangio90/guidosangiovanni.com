---
date: 2019-07-22T10:18:25+02:00
title: "Gestire pacchetti sul Mac usando Brew"
summary: "Un semplice tool per gestire l'installazione di pacchetti per Mac"
image: "/images/3/header.png"
tags: ["brew", "mac", "packagemanager", "software"]

---

## Come gestire i pacchetti sul Mac

Come sviluppatore spesso ho la necessità di installare e tenere aggiornati diversi software, e.g. PHP, python, git ecc…
Ad esempio, nel mio caso il Mac aveva una versione di PHP ed apache già disponibili, se però volessimo essere in linea con il server di produzione (che solitamente è un server Linux) sarebbe bello avere a disposizione gli stessi pacchetti che vengono forniti su Linux.

Ecco che entra in gioco Brew, un package manager per Mac (e Linux) che ci permette di gestire pacchetti da linea di comando in modo semplice.

Ecco l'url del sito dove potete trovare le istruzioni per scaricarlo, e la documentazione.

[Brew](http://brew.sh/)

Una volta installato possiamo cercare pacchetti, installarli, verificare le versioni e disinstallarli.

### Alcuni comandi

Vediamo alcuni comandi di base per capire quanto sia immediato e semplice da utilizzare:



Supponiamo di voler installare il PHP sul nostro Mac:

La prima cosa da fare è cercare il pacchetto, possiamo farlo googlando "brew php" oppure (meglio) direttamente da terminale `brew search php`.

![Brew search](/images/3/0-search.png)


Come facilmente intuibile il comando brew search cerca sulle repository l'esistenza del pacchetto PHP e ci restituisce una lista di possibilità.

Al momento della scrittura di questo post fra le varie alternative trovo php, php@7.1 e php@7.2.

Solitamente la regola è che il pacchetto senza "@" è l'ultima release stabile (ad oggi la 7.3), gli altri servono per scaricare una versione particolare.

Una volta identificato il pacchetto da installare è sufficiente lanciare ad esempio`brew install php@7.1` ed aspettare.

![Brew install](/images/3/1-install.png)


Tutti i file installati da brew finiscono nella cartella di installazione (di default /usr/local/Cellar), in questo modo sarà più facile rimuovere tutto manualmente se decidessimo di farlo.

Per disinstallare un pacchetto è sufficiente utilizzare l'intuitivo comando `brew uninstall php@7.1`.

![Brew uninstall](/images/3/2-uninstall.png)



Se decidessimo di aggiornare un pacchetto (ad esempio il php), possiamo utilizzare `brew upgrade php`.

**Attenzione a non confondere update ed upgrade, update.**

`brew update` serve per aggiornare la lista dei nuovi pacchetti disponibili, dovreste lanciarlo ogni giorno per vedere se fra i vostri pacchetti ce n'è qalcuno aggiornabile.



### Cask

Fin ora abbiamo utilizzato brew per gestire pacchetti eseguibili da terminale, la chicca di brew è "cask", una repository di software con interfaccia grafica installabili direttamente da terminale tramite brew.



Come funziona? Semplicissimo, i comandi sono praticamente gli stessi di brew.

Come esempio utilizzeremo **dbeaver** un software open-source per la gestione di connessioni a database.

``brew search dbeaver`` ci indicherà che sono presenti dei software, ma ci indicherà che sono dei "Casks":

![Brew cask search](/images/3/3-cask-search.png)

dbeaver-community e dbeaver-enterprise, quindi per installarli dovremo lanciare `brew cask install dbeaver-community`.

Come scopriamo quali pacchetti dobbiamo aggiornare?

`brew cask outdated` elencherà tutti i software che possiamo aggiornare, dandoci i riferimenti della versione installata e dell'ultima disponibile.

Come si aggiorna un pacchetto con Cask? 

Anche in questo caso il comando è semplicissimo:
`brew cask upgrade dbeaver-community` aggiornerà all'ultima versione il nostro dbeaver.



Sono disponibili anche alcune chicche, come il comando `brew cask home dbeaver-community` che apre il browser predefinito sulla homepage del software che vogliamo installare.



### Backup-Restore

Una delle cose che capita di tanto in tanto è di dover fare una pulizia del Mac o un format-reinstall, oppure di dover passare ad un collega la lista di software da installare.

Come farlo con brew/cask?

Anche in questo caso gli sviluppatori sono stati bravissimi, hanno tenuto tutto semplice e i comandi da lanciare sono 2.

`brew bundle dump` genera un file chiamato Brewfile, che contiene una lista di istruzioni, questo file va condiviso col collega o backuppato in un posto sicuro ed accessibile durante la prossima installazione, per poi essere riutilizzato semplicemente tramite il comando `brew bundle` (dopo esserci posizionati nella cartella del Brewfile).



### Conclusione

Con questo direi che è più o meno tutto, ci sono alcune altre cose interessanti che potete fare con Brew e con Cask. Come sempre vi invito a leggere la documentazione ufficiale per scoprire tutto il potenziale di Brew.

Inoltre vi segnalo che potete ad esempio collaborare pubblicando aggiornamenti delle applicazioni che utilizzate sulle repository git (ricordo che è tutto open source ed hostato su Github).

Buon divertimento!
