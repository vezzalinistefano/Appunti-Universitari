# Autenticazione criptata e composizione generica

* Usare funzioni di cifratura e una funzione MAC
* Come li combino?
    * Cripto poi MAC
    * MAC e poi cripto
    * Cripto e MAC insieme
> inserire foto schemi

* Se io non conosco la funzione di cifratura e la funzione MAC
    * l'unica costruzione sicura è la Encrypt-then-MAC
    * le funzioni di cifratura non devono mai produrre lo stesso msg cifrato
        * si fa usando i nonce o initialization vector
        * quindi se usiamo una tecnica diversa riveliamo che stiamo
        proteggendo sempre lo stesso tag!

* Regole da seguire
    1. il MAC deve autenticare ciphertext e nonce
    2. lo schema di criptazione simmetrica e il MAC devono usare differenti
       chiavi crittografiche
    3. nella fase di decrypt il MAC deve essere verificato PRIMA di iniziare
       a decriptare il cyphertext
       * evitare attacci di manipolazione
       * la verifica deve essere *time-constant*

## Criptazione e Criptazione Autenticata

* Dovremmo usare schemi di cifratura standardizzata
    :   * *schemi di cifratura autenticata*
    :   * evitiamo errori di implementazione
    :   * efficenza migliorata

