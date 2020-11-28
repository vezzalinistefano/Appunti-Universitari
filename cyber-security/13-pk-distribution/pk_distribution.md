# Public key infrastructure

## Come distribuire le chiavi pubbliche?

- Dobbiamo autenticare la chiave pubblica 
- Con diversi sistemi l'identità è quell'informazione che l'utente inserisce per dire con chi vuole comunicare
  - Whatsapp: numero di telefono
- Diversi tipi di approcci
  - **Trust-on-first-use(TOFU)**
    - Il primo scambio non è autenticato, il destinatario assume che il primo scambio non sia attaccato attivamente
      - È un assunzione forte
  - **Out-of-bounds communications**
    - Usiamo uno o più canali di comunicazione oltre a quello su cui vogliamo comunicare
      - Scambio di chiavi su canale insicuro, assumo di poter scambiare informazioni anche attraverso un altro canale sicuro
        - Su questo canale scambio solamente la chiave pubblica
  - **Approcci delegati**
    - Comunichiamo usando un solo canale ma usiamo firme digitali per autenticare le chiavi pubbliche autenticate da altre entità
    - Approccio più scalabile
    - Mi devo fidare di altri
  - **Approcci di verifica automatica**
    - Protocolli da usare quando vogtliamo verificare l'identità di entità particolari
      - Identità che ha un ruolo nella comunicazione che voglio realizzare

## Trust-on-first-use

- Esempio: **SSH**
- Assumiamo che la prima connessione non sia attaccata attivamente
  - Se anche qualcuno sta dumpando le nostre informazioni siamo tranquilli perche il protocollo di key-exchange protegge la confidenzialità

## Out-of-bound communications

- Scambio la chiave pubblica attraverso altri canali che assumiamo sia in grado di scambiare informazioni autenticate
  - Ad esempio il codice QR su whatsapp
- Posso permettere all'utente di comunicare anche se le chiavi non sono state verificate oppure no
  - tradeoff tra usabilità e sicurezza da cui non si scappa

## Approcci delegati

- Lasciamo che altre entità di cui ci fidiamo autentichino le chiavi pubbliche
- Di chi possiamo fidarci?
  - Approcci centralizzati
    - **PKI**(terza parte fidata)
      - Entità che per natura legale o altre caratteristiche è la "persona" di fiducia
  - Approcci distribuiti   
    - OpenPGP Web of Trust  
      - Una persona garantisce per un altra e cosi via

## Approcci di verifica automatico

- **ACME/LetsEncrypt**
  - Protocolli automatici per verificare automaticamente la corrispondenza tra dominio internet e una chiave pubblica
- **Keybase**
  - "chat" per verificare il controllo di certi servizi virtuali