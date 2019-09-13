---
title: "Gestire più versioni di Node.js sullo stesso sistema"
date: 2019-09-13T09:01:40+02:00
summary: "Come gestire più versioni di Node.js contemporaneamente"
image: "/images/5/header.png"
tags: ["js", "javascript", "nodejs", "node", "nvm"]
---


#### Il Problema

Negli ultimi mesi mi è capitato di mantenere progetti la cui "struttura" è stata definita qualche anno fa, periodo in cui l'ultima versione di Node.js disponibile era la 8.
In quel progetto avevamo realizzato un Gulpfile (Gulp è uno strumento di task automation in Javascript di cui magari parlerò in futuro), per unire tutti i file .js, minifizzarli e produrre una sorta di "bundle.js" il più piccolo possibile contenente tutte le librerie necessarie.

Di recente ho aggiornato la versione di Node.js alla 10 e quando mi sono ritrovato a installare il progetto nuovamente, lanciando Gulp ho ricevuto parecchi errori, questo perchè alcune istruzioni si basavano su concetti che sono cambiati da Node 8 a Node 10, come fare allora? Disinstallare Node 10 (che utilizzo attualmente in altri progetti) era fuori discussione, serviva un modo per:

- mantenere due versioni di Node installate contemporanemante
- poter cambiare velocemente da una all'altra


Ho scoperto di un pacchetto Open Source disponibile su Github chiamato **nvm** (Node Version Manager) tramite il quale è possibile installare tutte le versioni di Node, e impostare quella di default in modo super rapido e super veloce.

Vediamo dunque come fare e come utilizzarlo:

#### Installazione

Per installarlo ho cercato il progetto su Github ed ho trovato questo link nella README:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
```

può essere lanciato su qualsiasi sistema operativo disponga di *curl*.

A questo punto è sufficiente lanciare lo script di install che abbiamo appena scaricato e il gioco è fatto.

`chmod +x install.sh` --aggiunge i permessi d'esecuzione allo script scariato

`./install.sh` --esegue lo script di installazione

In alternativa, se come me disponete di un Mac potete utilizzare il comodissimo brew lanciando 

`brew update ` --aggiorna lista pacchetti

`brew install nvm` -- scarica ed installa nvm

Al terrmine dell'installazione dovrete modificare il vostro .bashrc o .zshrc (il file di configurazione del terminale che utilizzate) che si trova nella home del vostro utente (/home/guidosangiovanni/.zshrc) aggiungendo un paio di istruzioni:

Questo è un esempio che ha funzionato per me (dovrete adattare la seconda parte se non avete brew).

`export NVM_DIR="$HOME/.nvm"`

`source $(brew --prefix nvm)/nvm.sh`

#### Installiamo Node tramite NVM

Fantastico, il software è già pronto, verifichiamolo tramite il comando:

`nvm --version` -- fornisce la versione installata di nvm (ad oggi risponde 0.34.0)

Se tutto è andato liscio siamo pronti a "giocare" con le versioni di node.

`nvm install 8` -- installa la versione 8 di Node.js

È anche possibile scegliere una versione specifica come ad esempio:

`nvm install 8.2` oppure `nvm install 8.1.2`

*N.B. Se specifichiamo solo 8 verrà installata l'ultima relase stabile compatibile con 8, ad esempio 8.16.1 quindi se abbiamo delle necessità specifiche dovremo essere altrettanto specifici nell'installazione della giusta versione di Node.*



#### Utilizziamo Node

Ora dobbiamo solo indicare a nvm di impostare come Node.js di default la versione desiderata, come farlo?

`nvm use 8` --imposta la versione 8 

Questo comando fa in modo che tutti i comandi che utilizzano Node facciano riferimento ad una versione specifica, in questo caso la 8.



#### Curiosità: Cosa succede "under the hood"

Questo software non è magico, sostanzialmente il comando "use" modifica il contenuto della variabile d'ambiente $PATH ed aggiorna il terminale, potremmo tranquillamente scrivere noi uno script che faccia la stessa cosa ma, perchè reinventarci la ruota?



Spero che questo breve articolo sia stato chiaro ed utile, alla prossima!
