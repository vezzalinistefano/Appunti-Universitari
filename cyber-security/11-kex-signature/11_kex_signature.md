## Reminder: Settaggio asimmetrico

- A manda un messaggio a B
- **A e B USANO DUE CHIAVI DIVERSE**
- La crittografia asimmetrica nel mondo reale non si usa per scambiare
  messaggi

# Key exchange

- key exchange → scambio di chiavi
- L'obbiettivo di A e B è possano usare correttamente la crittografia
  **simmetrica**

## Idea di base

- A e B hanno un canale di comunicazione bidirezionale, C è un attaccante
- A e B non hanno nessuna chiave e scambiano dati su un canale non sicuro
- Un protocollo di key exchange permette ad A e B di ottenere una chiave
  condivisa su un canale insicuro

## Merkle puzzle

- Basato su schema di cifratura simmetrico
- Consideriamo:
  - Schema forte E
  - Schema debole W che può essere rotto in tempo N
- Funzionamento
  1. A genera N chiavi per E e le numera associandole tutte ad un id
     - (1,k1),(2,k2), ..., (N,kN)
  2. Cifra ogni tupla con lo schema debole
  3. Fa uno shuffle e li manda
  4. B riceve gli N messaggi e ne sceglie uno a caso
     - lo prende e lo "rompe" in un tempo normale ottenendo una chiave kX
  5. B risponde dicendo l'id della chiave scelta, A si era mantenuta un db
     delle chiavi generate
     - quindi può risalire alla chiave kX
  6. A e B hanno la stessa chiave
  - Il protocollo funziona

### Costo

- A costruisce N messaggi
  - costo proporzionale al numero di msg
- Comunicazione costa N (numero di messaggi)
- B spende una certa quantità di tempo a decifrare la criptazione debole in
  tempo N
- C per rompere lo schema deve fare un bruteforce
  - Prende tutti i messaggi e li rompe tutti
    - Ognuno in tempo N
  - Il costo è $(N^2)/2$
  - Approccio simile ai protocolli moderni
    - Costringe l'attaccante ad avere costi quasi esponenziali

## Problema del logaritmo discreto

- Ci torna molto utile in questo contesto trovare una costruzione matematica
  che si prestino al posto di costruire questi protocolli
  - Problema del logaritmo discreto Su campi finiti con cardinalità numero
    primo molto grande
  - Immaginiamo di avere un insieme di numeri modulo un certo numero intero
    - $Z_{11} \rightarrow \{0,1,2,3,4,5,6,7,8,9,10\}$
    - $3^7 mod 11 = 9$
      - data la base 3 (detta generatore) e il risultato 9 è difficile
        calcolare l'esponente 7
      - sia 11 sia 3 sia 9 sono pubblici, l'unico segreto è 7
      - Il problema del log discreto è quello su cui si basa la crittografia
        asimmetrica
      - posso costruire questo tipo di problema anche con diversi backend
        matematici
        - Operazioni matematiche definite sulle cosiddette curve ellittiche
- Preso un generatore **_g_** di un gruppo ciclico di ordine primo **p**
  - Problema del logaritmo discreto
    - se conosci $g^a$ è difficile calcolare **a**
  - Computational
    - dato $g^a$ e $g^b$ è difficile calcolare $g^{ab}$
  - Decisional
    - dato $g^a$ e $g^b$ è difficile calcolare $g^{ab}$ da un $g^r$ in cui r
      è un numero random

## Diffie-Hellman Key Exchange

<!-- Foto diffie helman k.e. -->

- g$^{ab}$ viene usata come chiave simmetrica
- Non si può distinguere $g^{ab}$ dal random

### Variante con curva ellittica

- Se usassimo una curva ellittica
  - a\*g -> g sarebbe un punto su una curva ellittica e l'operazione sarebbe
    una moltiplicazione puntuale
  - Perchè la curva ellittica?
    - Usarli con nnumeri interi li rende vulnerabili necessitando l'uso di
      numeri molto più grandi per ottenere lo stesso livello di sicurezza

## Digital signatures

- Firme digitali
- Le funzioni mac dell'ambito asimmetrico

  - permettono di garantire autenticità dei dati in ambito asimmetrico

- la funzione `authenticate` diventa
  - `sign(sk, message) -> signature`
- il destinatario invece deve

  - `verify(pk, message, signature) -> {true, false}`

- `sk e pk` sono due chiavi diverse!
  - `pk` è una chiave pubblica
  - `sk` è privata
  - il concetto è uguale al MAC ma abbiamo due chiavi differenti
- Non si può fare una firma sicura senza sapere la _secret key_
- Tutti possono verificare la firma
- Solo un partecipante però conosce la chiave segreta
  - Non ripudiabilità
    - Mittente non potrà negare di aver firmato il messaggio
- Chi conosce la chaive pubblica non può generare una firma valida!
- **Il tutto funziona solamente se Bob conosce la chiave pubblica di Alice**

### What if: Eve è un attaccante attivo - man in the middle

<!-- foto slide 34 -->

- Cosa succede se Eve si mette in mezzo tra Alice e Bob in modo attivo?
- Per proteggersi è necessario usare schemi che proteggano l'autenticità dei
  dati
  - Diventano utilissimi gli schemi di firma digitale!
- Associamo Diffie Hellman alle firme digitali

<!-- foto slide 35 -->

### Signature and Encryption Key pairs

- Termine __Key Pair__
  - Identifica la coppia di chiavi
  - ___(secret key, public key)___
- Ogni kp è associato con un utente
  - La chiave pubblica di un utente è unica e lo identifica
- I protocolli presentati assumono che gli altri utenti conoscano l'associazione tra le chiavi pubbliche e gli altri utenti
  - Questa associazione chiave-nomi può essere fatta solo nell'ambito asimmetrico

### DSA/ECDSA Signature

- Algoritmo di firma digitale
  - __Alternativa__ alle firme RSA
    -  RSA è nativamente deterministico
  - Basato su logaritmo discreto
    - Standard di riferimento è il DSA (_Digital Signature Algorithm_)
    - Al giorno d'oggi viene usato ECDSA 
      - Variante con curve ellittiche
- Contrapposizione con RSA
  - Ricorda: testi uguali devono sempre risultare in testi cifrati differenti
    - La funzione MAC è deterministica
  - Le firme digitali possono essere deterministiche o random a seconda del protocollo che viene usato
    - Gli schemi DSA/ECDSA richiedono ad ogni operazione di firma di generare un numero random _k_
      - Problemi possibili:
        - _Caso Sony_: internamente la playstation usava questi schemi ma questo k era sempre inizializzato allo stesso valore
        - Messaggi diversi con lo stesso _k_: l'attaccante può effettuare calcoli inversi per trovare la chiave segreta
      - Esiste una versione che permette di non dover generare k random
        - Da usare quando non abbiamo accesso a dati random "buoni"

### EdDSA signatures

- Basato su firme digitali ma
  - pensato per essere deterministico
  - Non necessita di sorgenti random
  - Garantiamo l'autenticità, non la confidenzialità