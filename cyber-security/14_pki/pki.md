# Protezione da attacchi MITM

## Infrastuttura PKI

- I key pairs necessitano di metadati
  - Quali sono i protocolli usati e i parametri?
- PKI è basato su certificati
  - standard x509

### Scenario 1: protezione MITM in una comunicazione web

- "lucchetto verde"
- So che sto comunicando con il server con cui avevo intenzione di comunicare
- E inoltre sono sicuro di chi ha autenticato il materiale crittografico che viene scambiato

### Scenario 2: protezione MITM tra due servizi

- comunicazioni autenticate in entrambe le direzioni
  - Il server autentica anche il client
- Servizi business to business

### Scenario 3: S/MIME per email

- Certificati non sono relativi a domini ma a indirizzi email
- Obiettivo non è eseguire uno scambio di chiavi sicure ma bensì usare correttamente uno schema di cifratura e firma

## Protezione da attacco MITM nelle comunicazioni Web

- Voglio raggiungere un server (tipo google.com)
1. Lo user agent fa una richiesta verso il suo nameserver DNS locale
2. Che andrà a fare una richiesta al architettura DNS per o**ttenere l'indirizzo ip** 
   - Tutte queste richieste sono in chiaro
   - No autenticità o confidenzialità
3. Se...
   - ...la comunicazione non fosse sicura:
     - handshake TCP verso l'indirizzo ip ricevuto dal dns
   - ...in quello cifrato:
     - handshake TLS alla porta 443 dell ip
       - Estensione di TCP
       - Fa handshake TCP e dopodichè fa uno scmabio di chiavi Diffie-Hellman
     - Come verifichiamo la chiave pubblica che riceviamo da google?
       - google ci manda un certificato
         - Verifichiamo che sia un certificato valido per google.com
           - "google.com" appare nel campo _common name_ del certificato o nel campo _dns name_ 
           - Se nel certificato non c'è il dominio che io ho scritto nel browser allora la comunicazione non è sicura!
           - **Ma chi mi dice che google non ha falsificato il certificato?**
- Approccio terza parte fidata
  - Ci si affida ad una terza parte intermedia che certifica che quel certificato che abbiamo ricevuto è autentico

## Certification authorities

- Queste entità terze sono le certification authorities
- Aziende che si assicurano di dare i certificati alle persone giuste
- Perchè fidarsi di loro?
  - Sono preinstallate nei nostri browser (nei nostri user agent)!
  - ci sono consorzi che decidono quale CA sono affidabili