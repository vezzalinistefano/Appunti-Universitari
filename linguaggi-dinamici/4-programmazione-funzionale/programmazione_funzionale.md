# Programmazione funzionale in Python

## Linguaggi funzionali

- Nascono da un formalismo detto lambda calcolo
- In un linguaggio puramente funzionale i programmi sono costituiti solo da un insieme di funzioni
- Non esiste il concetto di stato
- Mapping diretto tra identificatori e valori

## Python e programmazione funzionale

- Non è un linguaggio funzionale ma la supporta largamente
- Include elementi di supporto
    1. Funzioni come first class object
    2. Funzioni lambda
    3. Iteratori e generatori
    4. Supporto al map-reduce
    5. Closure, decoratori e callback
    6. Funzioni di oridne superiore in generale  

## Schema algoritmico map-reduce

- Calcolo tipico dei linguaggi funzionali
- Di solito usato su numeri enormi di oggetti
- Due fasi
  1. Fase di _map_: a ogni oggetto viene applicata una stessa funzione/trasfromazione
  2. Fase di _reduce_ in cui una funzione viene applicata su tutti gli oggetti trasformati

### e.g.: Somma di quadrati di una lista di numeri

## Anatomia del paradigma

1. Fase map: una stessa operazione viene applicata singolarmente ad ogni dato che compone la collezione di input
   - Si parallelizza molto bene 
2. Fase di reduce: un operazione tipicamente binaria viene applicata ai dati prodotti in output