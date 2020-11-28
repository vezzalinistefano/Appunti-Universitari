# Mac

## Garanzia autenticità

* Si usano messaggi di autenticazione
* Sono detti MAC: message authentication code

## Message authentication codes

* input chiave e messaggio di dimensione arbitratia
  * `MAC(key, msg)` -> tag
    * il tag ha dimensioni costanti a prescindere dal msg
* Accettano una chiave
  * chi non la conosce non può calcolare il tag
* Usati per garantire l'autenticità
* Il destinatario ricalcola il tag e lo confronta con quello ricevuto
* Posso mandare il tag nello stesso canale di comunicazione

### Parametri di sicurezza

* Dimensione chiave
  * allo stesso livello di quelli dei cifrari simmetrici
* Dimensione del tag
  * Difficile calcolare il livello di sicurezza in base al livello del tag
  * Dipende dallo schema del tipo di MAC
  * L'applicazione può scegliere di troncare il tag
    * meglio non farlo

### Replay attacks

* no tag differente per messaggi differenti
* l'attaccante può fare un replay attack
  * l'attaccante ripete, reinvia messaggi già inviati in precedenza
    * l'autenticità della comunicazione viene violata
      * non quella del messaggio però!
  * Possiamo inserire metadati che ci proteggano da questi tipi di attacchi
    * oltre al messaggio associo un valore id che deve essere univoco
      * ad esempio un contatore
      * sembra semplice ma è molto efficace!
      * Destinatario deve scartare tutti i messaggi con id già visti
    * Perchè non usare nonce sia per cifrare sia per unificare il msg?
      * si può ma bisogna stare attenti
      * lo faceva WPA, che è stato bucato!

### Reflection attacks

* In comunicazioni full-duplex si rischia di avere un attaccante che rimanda indietro il messaggio del mittente
* Per difenderci due approcci
  1. Aggiungiamo un altro metadato detto `direzione`
      * Da A a B dir = 0
      * Da B ad A dir = 1
  2. (Più popolare) Gestiamo la comunicazione full-duplex come due comunicazioni half duplec sicure
      * Due chiavi
        * Una per autenticare, una per verificare
      * Rende a livello di sicurezza i due flussi di comunicazione completamente indipendenti
        * E permette di non usare un metadato aggiuntivo

## Standard in vigore

1. Funzione MAC sulla base di una funzione hash
     * Funzione hash garantisce integrità ma la usiamo come building block di una funzione MAC
     * **Approccio sbagliato**: generare un hash con la coppia chiave-messaggio
    * Vulnerabile ad attacchi a lunga estensione
    * HMAC

* Esistono anche MAC basati su cifrari a blocchi (*CMAC*).
