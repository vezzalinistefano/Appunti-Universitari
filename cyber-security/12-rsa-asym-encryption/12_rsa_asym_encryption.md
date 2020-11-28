# RSA

- Si basa su moduli che sono prodotto di numeri primi

## RSA "da libro di testo"

- Fattorizzazione dei numeri
  - __n = p * q__
    - _n_ è il modulo pubblico
    - _p_ e _q_ sono i numeri primi segreti
  - Calcoliamo ___k, d___ in modo tale che
    - __c = m$^k$ mod n__
    - __m = c$^d$ mod n__
- RSA è un problema invertibile
  - ma può essere reso unidirezionale in base a cosa rendiamo pubblico e cosa rendiamo privato
- Idea
  - __Criptazione__: Alice usa la chiave pubblica k
  - __Decriptazione__: Bob usa la chiave segreta d
    - `sign(d, data)` $\rightarrow$ m = H(data)$^d$ mod n
      - La produzione della firma è fattibile solo da chi ha d
    - `verify(k, data)` $\rightarrow$ H(data) == m$^k$ mod n
      - n è pubblico
      - H() è una funzione di hashing
- Gli standard però aggiungono dei dettagli
  - In particolare su come funziona la funzione H
  - È una funzione per rispondere ad attacchi di varia natura

## RSA encryption and signature

- Dire "produco una firma/cifratura" con RSA non basta
  - __RSA è il problema matematico__
  - Gli standard che usano il problema RSA codificano anche la funzione di hash e altri dettagli fondamentali
- __Cifratura__
  - [...]
- __Firma__
  - PKCS1-PSS
    - È più nuovo
    - Implementa la funzione H
  - PKCS-v1.5
    - Protocollo storico
    - Dettagli su come è implementata la funzione di hash
- Le funzioni H sono chiamate schemi di padding
  - Nel mondo simmetrico lo schema di padding serve in certe modalità di cifratura per raggiungere la dimensione giusta
  - In questo caso servono per adattare il dato in modo che possa essere usato dal protocollo nel modo giusto

<br>
- RSA esiste soltanto definito su numeri interi

## ECDSA vs RSA signatures

- Agli attuali livelli di sicurezza
  - __ECDSA__
    - Le firmae sono molto piu piccole
      - Un'ordine di grandezza in meno
    - La __generazione__ della firma è più veloce
  - __RSA__
    - La __verifica__ è più veloce
  - Più aumenta il livello di sicurezza più questo tradeoff si riduce

## Cifratura asimmetrica

`encrypt(pk, message) -> ciphertext`
`decrypt(sk, message) -> message`

- Possiamo fare uno scambio di chiavi senza Diffie-Hellman?
<br>
- Alice usa la chiave pubblica di Bob (Sa che la chiave è di Bob) per cifrare il messaggio da mandare a Bob
  - Solo Bob potrà decifrarlo con la sua chiave segreta
- L'uso comune è usare sempre la cifratura RSA
  - Lo troviamo implementato ovunque

### Cifratura asimmetrica ibrida

- Nel mondo reale viene usata una cifratura asimmetrica ibrida
- Usiamo la cifratura simmetrica insieme ad una cifratura asimmetrica
  - In particolare:
    - Generiamo prima una chiave simmetrica
    - L'operazione asimmetrica viene eseguita sulla chiave 
    - Cifratura dei dati usando la chiave simmetrica
- Chiave pubblica cifra k e k cifra tutti i dati
- Perchè?
  - Performance
    - Fare operazioni di elevamento a potenza su numeri molto grandi sono molto piu costose rispetto all'uso di un cifrario simmetrico
    - Cifratura asimmetrica viene eseguita su campi molto più complessi
  - Cifratura asimmetrica in alcuni contesti non è sicuro
    - Vulnerabilità nativa (in comune con le funzioni hash)