# Encryption Frameworks

* Schema ad alto livello nasconde nonce, in. vector, etc...
* Potremmo trovarci a lavorare con interfaccie fatte in modo diverso
    * a seconda di librerie e altri fattori con cui possiamo imbatterci
* che cosa abbiamo quindi a disposizione?

## Black-Box authenticated enryption, framework probabilistico

* Schemi probabilistici
    * funzioni in cui non è possibile agire su nonce o initialization vector
        * vengono nascosti dettagli, ad esempio nonce generato all'interno della
        funzione 
            * fatte per i "principianti della crittografia"
    * viene sempre generato un initialization vector random
        * impossibile gestire un contatore ad esempio
* Schemi deterministici 
    ```
    encrypt(key, n, p) → ciphertext
    ```
    * Qui il programmatore deve stare attento a scegliere il nonce

## AED[^1]

* Framework usato praticamente in ogni standard di autenticazione moderna

> p → plaintext<br> 
> a → associated data<br>
> c → ciphertext<br>
> n → nonce

* I dati associati sono dati di cui vogliamo garantire autenticità ma non
  vogliamo garantire la confidenzialità
  * questo framework è pensato per offrire tutte le funzionalità con diverse
  sfaccettature
    ```
    encrypt(key, n, a, p) → c
    ```
  * nel mondo reale è comune avere dati che strutturalmente non possiamo
  cifrare
    * quelli che spesso vengono chiamati metadati
        * devono essere noti, possono servire
            * ad esempio agli stack di comunicazione
            * garantiamo autenticità dei metadati ma semplicemente non li
            cifriamo
        * ma non è obbligatorio che ci siano

## Ottenere materiale random crypto-sicuro

* Molti protocolli crittografici si afidano alla generazione di dati random
    * La generazione di numeri random è complessa!
    * spesso i dati generati sono PSEUDO-random 
        * (ad esempio funzione `urandom`)
        * però possono essere generazioni adatte a funzioni crittografiche
        * generati prendendo input da fenomeni fisici
            * ma sono limitati! (esempio server che non ha fenomeni di
            input)
            * in molte CPU vengono presi in input fenomeni fisici
            provenienti dalla cpu stessa
            * in alcuni server sono montati dispositivi fatti apposta per
            generare correnti residue usate nella generazione di dati random
        * in assenza di ciò "ci si adatta con quel che si ha"
    * ATTENZIONE: esistono funzioni random che non sono adatte a contesti di
    sicurezza
        >   bisogna leggere la documentazione!

[^1]: Authenticated encryption with associated data
