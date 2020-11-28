- Ricorda architettura
  - Documenti ricevono preprocessing
  - Indexing
- Inverted index word based

  - Tengono traccia della posizione della parola all'interno del documento
  - Utili per fare phrasal retrieval
  - Proximity query

- Tolerant retrieval
  - no match esatto tra ciò che l'utente scrive e il termine nel documento
    - Wildcard queries
    - Spelling correction
      - Ad esempio utente scrive "csaa" invece di "casa"
    - soundex
      - caso particolare di spelling correction

## Wildcard queries

- Docuenti possono essere trovati attraverso inverted index
- Come le wildcard queries possono essere risolte su inverted index?
  - Prefix
  - Postfix
  - \* in the middle

### Prefix queries

- Se ho il vocabulary ordinato posso farle con una ricerca dicotomica
  - trovo la prima parola che fa matching con la wildcard q.
    - ricerca dicotomica della parola 'mon'
    - fallirà ma troverò ad esempio 'moneta'
    - da qui scendo e accedo alle posting list di tutte le parole
    - fino alla prima parola che non inizia con 'mon'
- Possono essere implementate con algoritmi ulteriori direttamente sull'inverted
  index
  - ma serve un voc ordinato, un b tree o un trie
  - quindi non servono altre strutture

### Postfix queries

- \*mon
- posso ricondurlo ad una postifix query
- ribalto costruendo un altro vocabolario
  - con le parole che si leggono da destra a sx
  - dobbiamo appunto costruire una struttura accessoria
    - usiamo un **reverse B-tree**
      - sopra all'inverted index

### \* All'interno della parola

- es.: m\*nchen
- intersezione tra m\* e \*nchen
  - Costoso!
- Usiamo Permutation index
  - Lo facciamo diventare un problema di permutation index
  - Facciamo in modo che ogni wildcard query lo \* finisca in fondo
  - Nel PERMUTATION INDEX ogni parola viene memorizzata con tutte le sue
    rotazioni
- Esempio: "HELLO"
  - Memorizzo:
    - hello$
    - ello$h
    - llo$he
    - lo$hel
      - Memorizzo tutte queste permutazioni in un B-tree
  - Query:
    - Per X cerco X$
    - Per X* cerco $X*
    - Per _X cerco X$_
    - Per \*X\* cerco X\*
    - [...]
- Alternativa: uso dei q-grammi

### Q-Grammi

- q-gramma sequenza di q caratteri della stringa originale
- Esempio per q=3 --- "Vacations"
  - `{##v, #va, vac, aca, cat, ati, tio, ion, ons, ns$, s$$}`
  - Detta Sliding window
  - Data una parola lunga L costruisco L + q - 1 q-grammi
- Perchè usarli?
  - Rispetto al permuterm index i q-grammi non sono univoci
  - Dimensione in memoria molto minore!
- numeriamo tutti i q-grammi in ogni termine [minuto 56:47]
- term-document inverted index
- q-gram index

### In conclusione...

- nessuna tecnica per le wildcard è otima
- permuterm poco efficiente
- q-gram più leggera ma richiede un post processing a causa dei falsi positivi
- Quindi come fare a ottimizzare l'utilizzo di wildcard?
  - solitamente chi fornisce sistemi di IR lo implementa ma lo nasconde

## Spelling correction

- Andare a correggere la sintassi e l'ortografia delle parole
- Usi principali
  - correggere documenti indicizzati
  - correggere la query dell'utente
    - per recuperare risultati rilevanti per il bisogno informativo reale
      dell'utente che ha commesso un errore
- 2 contesti
  1. Isolated word correction
     - controlla ogni parola
     - non trova parole corrette che però rimangono parole esistenti
       - e.g. **_from->form_**
  2. Context sensitive correction
     - Guarda anche le parole circostanti
     - e.g., **_I flew form Heathrow to Narita_**
- Document correction
  - Lo usano i sistemi di acquisizione dei documenti tipo OCR
  - Si prendono in considerazione i contesti in cui il documento è stato
    scritto
  - Di solito non si effettua il document correction ma ci si limita alla
    query correction

### Isolated Word correction

- Ci serve un lessico con cui confrontarci
  - Usiamo un dizionario standard
  - Usiamo il vocabolario del nostro inverted index
    - Incluse le parole sbagliate che possono esserci nei documenti
- Disponiamo una distanza tra parole query e parole nel nostro lessico di
  riferimento
  - Funzione che data una coppia di parole restituirà un valore reale che ci
    permette di effettuare il ranking tra le parole più probabili
    - Vediamo una parola come una sequenza di caratteri

#### Edit distance(s)

- Distanza di digitazione che date due stringhe S1 e S2 ci dice quanti
  caratteri cambiare per passare da una stringa all'altra
  - Lavenshtein Distance: Insert, Delete, Replace
  - Damerau-Lavenshtein: Insert, Delete, Replace, Transposition
- Trovati dalla programmazione dinamica

##### Algoritmo

```
Data stringhe X e Y creo una matrice di dimensione pari a |X|+1 x |Y|+1
chiamata C

Ogni Cij rappresenta il numero minimo di operazioni per matchare x1,..i con
y1,..j

La matrice è cosi fatta
  Ci,0 = i
  C0,j = j
  Ci,j =
    Ci-1,j-1 se xi = yi
    1+min(Ci-1,j, Ci,j,1, Ci-1,j-1) altrimenti.

L'edit distance è l'ultima casella in basso a destra

Le parole in rosso sono le operazioni ottime per trasformare una stringa
nell'altra
```

<!---
- Riascoltarsi sta parte
-->

##### Implementazione in python

##### Weighted edit distance

- In base al carattere coinvolto è possibile aggiungere un valore che
  dipende dalla coppia di caratteri coinvolti
  - Questo mi porta ad avere un valore di Edit distance diverso
  - Abbiamo bisogno di una matrice pesata
  - Possiamo modificare l'algoritmo per poter gestire questi pesi

##### Usare edit distances

- Data una quer, numeriamo tutte le sequenze di caratteri
  - Tipicamente si mette una soglia
- Mostro all'utente i temrini trovati come suggerimenti
- Cerchiamo tutte le possibili soluzioni nel nostro inverted index
- Cerchiamo solo con la correzione pù probabile

##### Commenti sull'algoritmo

- Flessibilità
- Spazio
- Nel caso peggiore è quadratico

### Edit distance per ogni termine del dizionario?

- Data una query sbagliata, computiamo la sua edit distance con ogni termine
  del dizionario?
  - Come scegliamo i candidati?
- Una possibilità è usare filtri basati sui q-grammi
  - Possono anche essere usati per la spelling correction
  - **Sovrapposizione q-grammi**

### Q-Gram overlap

- Enumero tutti i q-grammi della query
- Uso il q-gram index che hanno un numero elevato di q-grammi in comune con
  la mia parola
  - più q-grammi ho in comune con la query (sbagliata) più la parola trovata
    è una correzione della parola in ingresso
  - soglia di numero minimo di q-grammi che una parola deve avere per essere
    una proposta di correzione della parola in ingresso
    - possiamo anche stavolta dare un peso ai diversi q-grammi

#### Coefficiente di Jaccard

- Ci permette di misurare quanto due insiemi sono simili in base alla
  quantità di esempi in comune
- Una misura comune di overlap
- Siano X e Y due insiemi allora il coefficiente di Jaccard:

$$|X \cap Y|/|X \cup Y|$$

- Fa 1 quando X e Y sono uguali
- Fa 0 quando sono completamente disgiunti
- X e Y non devono avere la stessa dimensione

#### Q-gram overlap usando il q-gram index

- Complessità non dipende dal numero di parole che sono nel vocabolarioa

<!-- La prof al minuto 22:56 ha iniziato a scrivere cose -->

- Caso in cui conosco il numero di q-grammi minimo che deve avere la mia
  query con il mio vocabolario

### Advanced Q-Gram filtering

- Voglio calcolare edit distance in un gruppo di parole
  - per ridurre i tempi sono stati proposti tre principali filtri
    - Lenght filter
    - Count filter
    - Position filter
- "Fare filtering"
  - Idea basata su: più facile dire se una coppia di parole...
    - Immaginiamo di avere due parole:
      1. _Ada_: w1
      2. _Adeguata_: w2
      - `ed(w1, w2)` $\leq$ 1
      - Quindi più facile dire se due parole non fanno match piuttosto che
        se lo fanno!
      - Il filtering permette di buttare via le parole che sicuramente non
      faranno match!

#### Length filter

- Due stringhe con edit distance $\leq$ di k hanno lunghezze
  abs(length(S1)-length(S2)) $\leq$ k

#### Count filtering

- Stringhe simili hanno molti q-grammi in comune
- Minimo numero di q-grammi tale che ed(s1,s2) $\leq$ k

- Massimo della lunghezza delle due stringhe + q -q - kq

<!-- minuto 82 sta scrivendo qualcosa -->

#### Position filtering

#### Spelling correction and approximate string join

- La spelling correction è un _approximate string matching problem_
- __SPELLING CORRECTION__
  - Dato un pattern P e una text collection TC and a 
- Dato un insieme di parole voglio trovare tutte le coppie di parole con un ed <= di k
- Approximate string join vado a trovare tutte quelle coppie di stringhe in cui la distanza è entro un certo K

<br>

- Possiamo sfruttare i filtri per fare queste operazioni di approximate string join
  - ASJ da fare sul prodotto cartesiano tra le due text collection
  - Calcolare l'ed per ogni coppia è molto costoso
  - invece che calcolare l'ed sul prodotto cartesiano
    - lo calcolo solamente su un insieme candidato, ovvero un sottoinsieme del prodotto cartesiano formato da tutte quelle coppie che soddisfano tutti i filtri
      - assicurano correttezza
      - assicurano efficenza
        -  più l'insieme candidato sarà piccolo più il calcolo dell'ASJ sarà efficente
    - butto via tutte le coppie che non soddisfano almeno un filtro

<!-- circa minuto 28 riassunto generale -->

### Context sensitive correction

- Ho bisogno di un contesto (obv)
- Idea: cercare termini nel dizionario vicini (in weighted edit distance) ad ogni termine nella query
  - Poi provo tutte le possibili alternative con una parola fixata alla volta

#### Problemi nella spelling correction

- __Interfaccia utente__
  - correzione automatica vs. suggerita
  - _Did you mean..._ funziona solo per un suggerimento
    - Quale presento all'utente?
      - L'alternativa che colpisce piu documenti: poco efficente
      - Query log analysis: molto più efficente
  - E per le soluzioni alternative multiple?
- __Costi__
  - La spelling correction è potenzialmente costosa
  - La lancio su ogni query?
    - Lancio solo quando i risultati sono molto inferiori a quelli attesi

## Soundex

- Principio per cui errore viene da un errore grammaticale 
  - Non si sa come una parola va scritta e si va a "tentoni"

- Soundex è un algoritmo fonetico per indicizzare nomi sulla base dei suoni

- L'idea è di creare classi di appartenenza basate sul suono di una parola
  - Vengono introdotte delle euristiche affinchè la nostra parola venga trasformata in un espressione che rappresenza la codifica
  - e.g. ___chebyshev &#8594; tcebycheff___ 
- L'algoritmo codifica principalmente le consonanti
  - Vocali codificate solo quando sono la prima lettera
  - Sei classi fonetiche basate su dove si mettono le labbra e la lingua

- Quanto è utile?


### Algoritmo tipico

