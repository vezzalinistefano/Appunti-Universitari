- [Domain Name System](#domain-name-system)
  - [Hostname](#hostname)
  - [Meccanismi di _naming_](#meccanismi-di-naming)
  - [Traduzione da ...](#traduzione-da-)
  - [DNS](#dns)
  - [Obiettivi DNS](#obiettivi-dns)
  - [Importanza DNS](#importanza-dns)
  - [Organizzazione nomi](#organizzazione-nomi)
    - [Domini di massimo livello](#domini-di-massimo-livello)
      - [I nuovi TLD](#i-nuovi-tld)
      - [Dal 2012: liberizzazione](#dal-2012-liberizzazione)
    - [Second level domain](#second-level-domain)
  - [Name Server](#name-server)
    - [I root name server](#i-root-name-server)
      - [Funzionamento](#funzionamento)
      - [Indirizzi anycast](#indirizzi-anycast)
    - [TLD name server](#tld-name-server)
    - [Name server più visisbili](#name-server-più-visisbili)
  - [Gerarchia](#gerarchia)
  - [Definizioni](#definizioni)
  - [Server autoritativi](#server-autoritativi)
  - [Soluzioni per...](#soluzioni-per)
  - [Attività di aggiornamento](#attività-di-aggiornamento)
  - [Name server locali](#name-server-locali)
    - [Alcune possibilità](#alcune-possibilità)
  - [tipo 2](#tipo-2)
  - [Tipo 3](#tipo-3)
  - [Dati DNS](#dati-dns)
    - [Resource records](#resource-records)

# Domain Name System

- Sistema che viene in aiuto all'utente per creare comunicazioni sul protocollo di rete
- Serve al naming di internet
  - Tradurre nomi con indirizzi IP
  - Assegnazione di nomi più comprensibili a degli indirizzi IP
- Sistema distribuito geograficamente
- Applicazione internet al servizio delle applicazioni internet

## Hostname

- organizzazione lascamente gerarchica
- si possono usare direttamente gli indirizzi ip
  - un po' scomodo
- hostname favoriscono l'usabilità
  - permettono a utenti di fare riferimento a nomi mnemonici e gerarchici
- Altri vantaggi
  - Se indirizzo ip cambia il nome può rimanere sempre lo stesso!
  - Bilanciamento del carico  
    - Se tante persone risolvono uno stesso nome (tipo google.com) possiamo farlo risolvere in tanti ip diversi
      - (i siti principali hanno diversi server)
- Maggior flessibilità a discapito di una minima perdita di efficenza

## Meccanismi di _naming_

- lookup e reverse lookup

## Traduzione da ...

- Se la rete ha pochi nodi: soluzione centralizzata con uno spazio piatto dei nomi
  - Facciamo tutto in un file di testo con nomi canonici e indirizzi ip assegnati
    - Ovviamente soluzione assolutamente non scalabile
- Se la rete ha milioni di nodi: soluzione distribuita con uno spazio gerarchico dei nomi
  - Prendiamo uno spazio piatto e lo "spezziamo" in tante sottoparti
    - Unità centrale che delega ad altre unità di livello inferiore e così via
      - Ogni unità riesce a gestire un numero di nodi ristretto
  - **Domain Name System**

## DNS

1. Realizzare uno spazio dei nomi gerarchico
2. Implementa un meccanismo efficente che consente di effettuare in modo rapido la risoluzione dei nomi
   - sistema _estremamente_ distribuito
   - deleghe gerarchiche
   - uso di caching ad ogni livello per velocizzare le operazioni 
   - uso di protocollo "veloce" (UDP) per le query DNS
   - può anche richiedere trasferimenti più corposi
     - usano un protocollo di trasporto affidabile (TCP)
     - aggiornamenti dei record dei name server

## Obiettivi DNS

- Spazio dei nomi consistenti
  - Eventuali modifiche non devono causare inconsistenze e problemi
- Elevata tolleranza ai guasti
  - no single point of failure
- Sistema scalabile
- Sistema deve funzionare in reti eterogenee

## Importanza DNS

- Funzionale
- Commerciale
  - I nomi hanno un valore economico!
- Funzionale
- Sicurezza

## Organizzazione nomi

### Domini di massimo livello

- www.unimore.**it**
- gTLD: generic TLD
  - .com
  - .edu
  - .org
  - ...
    - Non sono legate ad un area geografica specifica
    - Spesso legate ad organizzazioni situate negli USA
- ccTLD: country code TLD
  - .uk
  - .fr
  - .it
  - ...
- iTLD: infrastructure TLD

#### I nuovi TLD

- Ne sono stati creati di nuovi
  - troppi .com, peso crescente della UE...
- Unsponsored TLD
- Sponsored TLD (sTLD)
  - TLD dedicati ad organizzazioni specifiche

#### Dal 2012: liberizzazione

- È diventato possibile richiedere l'apertura di nuovi tld a patto che non esista già
- Ovviamente richiede un finanziamento

### Second level domain

- Vari brand di un organizzazione
- www.**unimore**.it
- Sono quelli che hanno un vero valore!

<!-- immagine gerarchia dei domini -->

- SLD è il brand, tutto quello che c'è sotto è gestito da chi ha acquistao il SLD

## Name Server

- Tante classi di name server
- Root Name Server
  - Gestiscono la radice dell'albero dei nomi
- TLD name server
- SLD name server

<br>

- Local Name Server
  - Non associati ad una gerarchia
  - Erogano direttamente il loro servizio ai client
  - Quello che usiamo noi utenti
  - Se non lo inseriamo manualmente viene preso dal DHCP o ci viene dato dal nostro ISP

### I root name server

- Erano 13
- Ed erano in posizioni geografiche note
  - Sopratutto negli USA
- Deve conoscere tutti i name server responsabili per tutti i TLD
- Viene eletto un name server primario
  - Lettera A
  - Quello che riceve le notifiche ufficiali e perfettamente aggiornati
  - Gli altri ricevono aggiornamenti in modo periodico dal name server primario
  - Di conseguenza non tutte le modifiche vengono viste immediatamente da chiunque
- Ora i root server sono molti di più
  - Esistono delle copie

#### Funzionamento

- File della root zone è trasmesso ai root name server
  - modalità in band
    - mediante protocollo DNS (però usando TCP!)
  - modalità out of band

#### Indirizzi anycast

- È un indirizzo IP associato a indirizzi ip di più host
  - Voglio comunicare con un host qualunque appartenente a questo gruppo
  - La risposta arriva dal primo che viene raggiunto
- Ad esempio diversi host di uno stesso name server
- si riduce tempo di risposta
- più tollerante ai guasti
- permette di distributire il carico

### TLD name server

- gestiscono i dati e le richieste relative a gTLD e ai ccTLD
- devono conoscere tutte le informazioni relative ad uno specifico TLD
  - se conosco .it devo sapere che esiste unimore.it
- Devono registrarsi presso i root name server

### Name server più visisbili

- SLD name server
- Local name server

## Gerarchia

- I name server devono conoscere quali altri server sono repsonsabili di altre zone
  - Tutti i server devono conoscere i root name server
  - name server deve conoscere il name server di una zona sottostante
- la gerarchia dei name server non sempre corrisponde con la gerarchia dei nomi

## Definizioni

- Nome di dominio: il nome canonico
  - sequenza che va dall'hostname fino alla fine
- Dominio: è il sottoalbero
- Zona

## Server autoritativi

- Ogni zona ha il suo database
- Master File
  - Fonte autoritativa di dati per una zona
  - Il ns che lo contiene viene detto "autoritativo"
- Server Autoritativi
  - Primary
    - Vado a scriverci le modifiche al master file
  - Secondario
    - Periodicamente scarica copie dal master file a partire dal master file
- Per sicurezza ogni zona deve avere almeno due name server

## Soluzioni per...

- un name server può avere dati autorativi per più zone

## Attività di aggiornamento

- master file editato da chi ha l'autorità
  - consultato da ns primario
    - resolver esterni lo consultano periodicamente tramite richieste udp
  - periodicamente il ns secondario ne scarica una copia

## Name server locali

- "le foglie"
- Contattati direttamente dal client

### Alcune possibilità

1. Client di organizzazioni senza dominio proprio
2. Client in un dominio che non gestiscono direttamente il loro dominio
3. Dominio gestito
4. Uso di local dns pubblici

## tipo 2

- Hanno un SLD ma hanno deciso di delegare la funzione di name server ad un name serve del proprio ip

## Tipo 3

## Dati DNS

### Resource records

- ciascun tipo di resource roecord include dati particolari