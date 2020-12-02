# Server di rete

- [Server di rete](#server-di-rete)
  - [Paradigma _client/server_](#paradigma-clientserver)
  - [Vari problemi](#vari-problemi)
  - [Interazione prot trasoprto con applicazioni](#interazione-prot-trasoprto-con-applicazioni)
  - [Socket](#socket)

## Paradigma _client/server_

- Client
  - Processo applicativo che fa richieste ad un server
- Server 
  - Processo applicativo in esecuzione su qualche macchina
  - Viene contattato da un client per fonrire un servizio
- Questo paradigma è rilevabile a diversi livelli

<no fino a slide 8>

## Vari problemi

- Come viene realizzato questo paradigma a livello trasporto
- Processo server rimane in ascolto su una porta TCP o UDP
 
1. Come fa un processo server ad identificare il corrispondente processo client con il quale vuole comunicare?
  - _multitasking_: server riesce a gestire su una sola porta le comunicazioni di **tanti** client
    - Soluzione: il server si ricorda con chi sta parlando utilizzando la quadrupla:
      - ip server
      - numero porta del protocollo lato server
      - ip del client
      - numero porta protocollo lato client

## Interazione prot trasoprto con applicazioni

- API

## Socket

- Le API nei sistemi moderni sono i socket
  - Interfacce messe a disposizione per creare delle comunicazioni in modo trasparente alle applicazioni
  - Consentono ad un programmatore di effettuare comunicazioni TCP e UDP senza curarsi di dettagli di più basso livello
  - Come comunicazioni tra processi ma dal punto di vista remoto
   