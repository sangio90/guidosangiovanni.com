---
date: 2019-06-22T11:21:08+02:00
title: "JWT - JSON Web Token"
summary: "Cos è e come si usa il JWT - JSON Web Token"
image: "/images/2/header.jpg"
tags: ["jwt", "javascript", "php", "security"]

---

## JWT

HTTP è un protocollo stateless, ovvero ogni richiesta è indipendente dalle altre. Per questo motivo ci serve uno strumento che indichi al Server che, dopo che ho fatto la Login, ad ogni richiesta non dovrò più autenticarmi.

Un approccio innovativo alla soluzione di questo problema è l'uso di JWT (JSON Web Token).

Un JWT è un token composto da un set di informazioni scritte in formato JSON che viene (per questioni di compattezza e praticità) codificato in "base64url" per funzionare sul Web.



### Struttura di un Token

Un token è diviso in tre parti:

- Header
- Payload
- Signature



###### Header

Un header contiene tipicamente le informazioni necessarie ad interpretare il Token, ovvero l'algoritmo con cui viene applicata la Signature e il tipo di Token (JWT).


###### Payload

Il Payload è un oggetto JSON nel quale possiamo inserire tutte le informazioni che vogliamo.

###### Signature

La Signature è la parte che ci consente di essere sicuri che il Token sia autentico.



### Come funziona

Dopo l'autenticazione il Server deve generare un Token, utilizzando delle apposite funzioni (ne vedremo una più avanti), questo Token viene inviato al Client, che deve salvarlo.

Ad ogni richiesta successiva il Client dovrà allegare questo Token in modo da comunicare al Server di essere lo stesso Client che precedentemente si è autenticato.

Il Server sfrutterà la Signature del Token per assicurarsi che sia stato generato da sè stesso e che non sia stato alterato, quindi deciderà se consentire o bloccare la richiesta.



### Un Esempio

![Esempio di un Token JWT](/images/2/0-esempio.png)

### Come si usa?


###### Client

Dopo aver eseguito la Login, lato client riceveremo il Token, dovremo quindi memorizzarlo ed allegarlo ad ogni successiva richiesta, tipicamente aggiungendo alle richieste HTTP un nuovo Header con questa forma:

``Authorization: Bearer <token>`` dove `<token>` sarà il nostro JWT.

###### Server

Dopo aver ricevuto la richiesta di Login ed aver verificato le credenziali, se tutto è corretto, dovremo generare un Token ed inviarlo al Client.
Per generare i Token JWT esistono diverse librerie per ogni linguaggio di programmazione che si occupa di Web.

Ad esempio in **PHP** possiamo utilizzare la libreria firebase/php-jwt e in **Node.js** auth0/node-jsonwebtoken.

Per generare un Token sarà necessario un *secret* che sia conosciuto soltanto dal Server.

Ogni qualvolta riceviamo una richiesta, se questa contiene l'header di autorizzazione JWT, dovremo verificare il Token, ovvero assicurarci fondamentalmente di 3 cose:

1. Il token è in un formato valido
2. Il token è stato firmato da noi
3. Il token non è scaduto

Tutti questi controlli (e anche altri) sono già implementati nelle librerie menzionate sopra.

Una volta assicurati che il Token sia valido, possiamo proseguire con la gestione della richiesta, sulla base delle informazioni contenute nel Token (e.g. ruoli, autorizzazioni ecc…).
Per fare questo solitamente si sfrutta il pattern dei Middleware.

### Aspetta un momento...

Una delle regole d'oro dello sviluppo Web è "non fidarsi mai di ciò che arriva dal Client", come faccio ad essere sicuro che il Token non sia stato generato o modificato dal Client, magari per garantirsi dei permessi extra?

Il trucco è piuttosto semplice, la terza parte del Token ci permette di assicurarci che il Token sia stato generato dal nostro Server, come?

La signature si compone utilizzando l'algoritmo di Hash specificato nello Header su:

(base64UrlEncode(header) . + base64UrlEncode(payload)) utilizzando un secret.

In questo modo, quando riceviamo una risposta, sarà sufficiente eseguire la stessa funzione su header + payload con il nostro Secret e verificare che la signature corrisponda.

Se corrisponde significa che la chiave utilizzata è la stessa, quindi il Token è stato generato da noi.

Inoltre, qualsiasi modifica al payload (ad esempio l'aggiunta di un ruolo) fa sì che l'Hash cambi, quindi non solo ci assicuriamo che il Token sia stato firmato da noi, ma anche che il Payload sia rimasto inalterato.

### Le informazioni contenute nel Token sono al sicuro?

No.
Il Token non viene cifrato (almeno non di default), quindi non dovreste MAI inserire nel JWT cose come password o dati sensibili.

L'unica funzione che viene utilizzata (per compattare il JWT e per assicurarci che tutti i caratteri siano compatibili col Web) è base64UrlEncode, una funzione reversibile.

Provate da questo sito a codificare e decodificare un testo per capire meglio il concetto.

https://www.base64encode.org/

https://www.base64decode.org/

### Claims

Ogni "chiave" del JSON che inviamo come Payload si chiama Claim, ne esistono alcuni riservati che *dovrebbero* essere utilizzati per dei motivi precisi:

- iss: chi ha generato il Token
- sub: oggetto del Token
- aud: destinatario del Token
- exp: scadenza del Token
- nbf: data di inizio validità del Token
- iat: Timestamp di generazione del Token
- jti: identificativo univoco del Token

Le funzioni di verifica del Token spesso includono anche dei controlli sulle date, se compilate.

Ad esempio, se nel campo exp è indicato il Timestamp di ieri il Token verrà considerato scaduto, quindi l'autorizzazione sarà negata.


### Perchè dovrei usare JWT

È compatto: un Token molto piccolo può contenere tante informazioni utili, che posso salvare lato client in modo da evitare di dovergliele inviare ad ogni richiesta (e.g. il nome utente).

È scalabile: Non occupa spazio su disco (a differenza dei file di Sessione) nè memoria (a differenza di sistemi come Redis/Memcached), l'unico Overhead è l'esecuzione di una funzione di "verify" sul Token, sicuramente più veloce di un accesso a Filesystem.

È sicuro: non si presta a contraffazione se il secret con cui generiamo i Token è al sicuro.


### Let's do it

Vediamo un esempio semplice di implementazione sviluppata in Node.js.

(il codice è volutamente inserito in screenshot, scrivendolo e non potendo fare copia-incolla si è costretti a leggerlo almeno una volta e a capire davvero come funziona)

###### Step 1

Implementazione del Server tramite express.js

![Server](/images/2/1-node.png)

###### Step 2

Implementazione dell' API di Login (la funzione verifyCredentials non è stata riportata, la verifica di credenziali può essere fatta in diversi modi)

![Api-Login](/images/2/2-node.png)

###### Step 3

Creazione di un Token JWT

![Creazione JWT](/images/2/3-node.png)

###### Step 4 

Verifica di un Token

![Verifica Token](/images/2/4-node.png)


### Dove salvare il JWT lato Client?

Dipende. 
Se stai sviluppando un'applicazione Mobile che utilizza le REST API puoi salvarlo direttamente nella memoria del dispositivo.

Se invece stai sviluppando applicazioni Web puoi fare diverse cose:

- Local Storage: è uno Storage del Browser che non ha scadenza, molto utile per salvare informazioni che vogliamo evitare di chiedere al Server ogni volta, tutto il Javascript che viene eseguito su un dominio può accedere al LocalStorage per quel dominio, stiamo quindi attenti agli attacchi XSS (Cross-Site Scripting).
- Cookie: Li conosciamo tutti, vengono gestiti dal Browser ed inviati automaticamente ad ogni richiesta fatta al dominio da cui sono stati creati. Hanno la vulnerabilità del CSRF (Cross-Site Request Forgery).


### Problemi

Come tutte le tecnologie, anche il JWT ha qualche problema che dobbiamo conoscere prima di fiondarci ad utilizzarlo in ogni contesto.

###### Early Revoke

Supponiamo che un utente si autentichi col suo Smartphone e che i nostri Token durino una settimana. L'utente smarrisce il dispositivo dopo un giorno quindi per i restanti 6 chi trova il suo Smartphone ha l'accesso garantito ai nostri servizi. Il Token è stato generato da noi con una data di scadenza, quindi quando riceveremo il Token lo considereremo valido.
Una volta che il Token è generato, finchè non scade per noi è valido.

L'unica soluzione, più onerosa dal punto di vista delle risorse, è quella di gestire una Blacklist di Token invalidati oppure una Whitelist di Token validi, quindi per ogni richiesta validare il Token per verificare se è presente nella nostra Black/Whitelist ed agire di conseguenza.

###### Data Overhead

Al crescere dei dati contenuti cresce anche la dimensione del Token, stiamo attenti a non inserirci troppe informazioni.


### Conclusione

Abbiamo iniziato a conoscere i JWT, ora non vi resta che provarlo.

https://jwt.io è il sito ufficiale del JWT dove potete trovare la spiegazione di come funziona e un tool fantastico per testare la validità dei vostri Token da browser.
