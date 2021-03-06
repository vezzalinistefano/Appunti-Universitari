- [Gestione della memoria](#gestione-della-memoria)
  - [Gestione Manuale](#gestione-manuale)
  - [Gestione nei linguaggi dinamici](#gestione-nei-linguaggi-dinamici)
  - [_Elementi di gestione automatica della memoria_](#elementi-di-gestione-automatica-della-memoria)
  - [Allocazione della memoria](#allocazione-della-memoria)
  - [Garbage collection](#garbage-collection)
    - [Due aspetti da considerare](#due-aspetti-da-considerare)
  - [Approcci al Garbage Collection](#approcci-al-garbage-collection)
    - [Nota terminologica](#nota-terminologica)
    - [Tracing garbage collection](#tracing-garbage-collection)
      - [Cause irraggiungibilità](#cause-irraggiungibilità)
    - [Reference counting](#reference-counting)
    - [Generetional garbage collection](#generetional-garbage-collection)
    - [_Algoritmi basati su tracing_](#algoritmi-basati-su-tracing)
    - [Mark and Sweep](#mark-and-sweep)
      - [Limiti dell'algoritmo](#limiti-dellalgoritmo)
    - [Tri-color marking](#tri-color-marking)
      - [L'algoritmo](#lalgoritmo)
      - [Dimostrazione (non richiesta vedere notebook)](#dimostrazione-non-richiesta-vedere-notebook)
      - [Efficienza algoritmo](#efficienza-algoritmo)
    - [Reference counting](#reference-counting-1)
      - [Inizializzazione del RC](#inizializzazione-del-rc)
      - [Operazioni che modificano il RC](#operazioni-che-modificano-il-rc)
      - [Vantaggi e svantaggi](#vantaggi-e-svantaggi)
    - [_Gestione della memoria dell'interprete CPython_](#gestione-della-memoria-dellinterprete-cpython)
      - [Arena, pool e blocchi](#arena-pool-e-blocchi)
        - [Vantaggi e svantaggi](#vantaggi-e-svantaggi-1)
    - [_Garbage Collection CPython_](#garbage-collection-cpython)
      - [Rilevamento dei cicli](#rilevamento-dei-cicli)
  - [_Generational Garbage Collection_](#generational-garbage-collection)
    - [Esecuzione algoritmo](#esecuzione-algoritmo)
    - [Vantaggi/Svantaggi](#vantaggisvantaggi)
  - [Approccio generazionale in CPython](#approccio-generazionale-in-cpython)
    - [Cicli di attivazione](#cicli-di-attivazione)
    - [Le soglie](#le-soglie)
    - [La old generation](#la-old-generation)

# Gestione della memoria

- Due approcci
  - Filosofia C
    - Lasciata al programmatore
  - Approccio Java
- Due aree principali gestite
  - **Stack**
    - contiene i nomi globali
    - dopodichè ogni scalino contiene una funzione
      - che contiene le variabili locali
        - sono tutti puntatori a heap
  - **Heap**
    - Ha bisogno di gestione
      - Troppe zone irraggiungibili possono portare ad un memory leak
      - Nei linguaggi dinamici e in Java le zone rosse vengono recuperate
        automaticamente
    - Esiste al di fuori del contesto di programmazione che lo ha creatoa

![](/img/3_gestione_memoria/gc_problem.png)

## Gestione Manuale

- Vantaggi
  - La velocità delle operazioni eseguite è molto elevata
  - I pattern di acquisizione e rilascio sono espliciti
- Svantaggi
  - Rischi dovuti all'elevato onere di programmazione
    - Segmentation fault
    - Dangling Reference
    - Memory leak

## Gestione nei linguaggi dinamici

- Seguono l'approccio base di Java
- Particolarmente complessa con tipizzazione dinamica

## _Elementi di gestione automatica della memoria_

- Sistema automatico di gestione della memoria diviso in due parti

  - Modulo che alloca la memoria dinamicamente
  - Il _garbage collector_

- Handle
  - Si pone tra Heap e il programma
    - piccolo hoverhead di spazio
    - quando faccio compattazione cambiano gli indirizzi nella handle ma
      non nel programma

## Allocazione della memoria

- La _heap_ è quella porzione di memoria che può essere acceduta anche al di fuori del contesto di programmazione da cui è stata allocata
- Oggetti nello heap possono essere acceduti mediante riferimenti di due tipi
  - riferimento (indirizzo) dell'oggetto in memoria
  - riferimento ad una c.d. handle che a sua volta referenzia l'oggetto

![](img/3_gestione_memoria/handle-based-heap.jpg)

## Garbage collection

- Individua oggetti non piu raggiungibili e libera la memoria che occupano
- Il GC deve
  - essere efficiente
    - In termini di tempo e spazio
  - essere corretto
    - non deve eliminare oggetti ancora raggiungibili
- L'overhead in termini di tempo viene calcolato in base a _throughput_ e
  _latenza_
  - La _latenza_ è il tempo in cui il GC deve fermare l'esecuzione dell'applicazione
  - Il _throughput_ è la quantità di oggetti rilasciati ad ogni ciclo di garbage collection

### Due aspetti da considerare

1. **Quando viene attivato il GC?**
   - Attivazione periodica
   - Attivazione al superamento di una soglia
2. **Come avviene il rilascio della memoria**
   - Garbage collection _non in movimento_
     - Rilascio gli oggetti irraggiungibili senza spostarli
   - Garbage collection _in movimento_
     - Copio tutti gli oggetti raggiungibili in una nuova area di memoria
     - Più costosa ma ha **2 importanti vantaggi**:
       1. Rende disponibili consistenti aree di memoria _contigua_
       2. Possibilità di aumentare la _località spaziale_, con corrispondente
          aumento della probabilità che oggetti "vicini" si trovino sulla stessa
          linea della cache o nella pagina di memoria virtuale

## Approcci al Garbage Collection

- [Tracing garbage collection](#tracing-garbage-collection): ricerca degli oggetti ancora referenziati come
  se fossimo in un grafo
- [Reference counting](#reference-counting): adottata da python, basato su idea che ogni ogg ha un
  contore, quando il contatore è a 0 l'oggetto può essere disallocato
- [Algoritmi generazionali](#generetional-garbage-collection): gli oggetti che sono allocati da più tempo probabilmente avranno vita più lunga.

### Nota terminologica

- Nelle documentazioni si può trovare il termine ___"mutator"___
  - Un mutator è un componente (può essercene più d'uno) responsabile dell'esecuzione del codice utente che alloca e modifica gli oggetti
  
### Tracing garbage collection

- Fa il tracciamento degli oggetti ancora referenziati
  - Attraverso una catena di riferimenti che parte dagli oggetti radice
- L'insieme degli oggetti radice sono detti root set
  - sono gli oggetti che stanno sullo stack
  - Tracciamento: parto da oggetti radice e seguo il grafo (orientato)
- Gli oggetti ancora raggiungibili sono detti _raggiungibili_
- Raggiungibilità è ricorsiva
  - Un oggetto radice è raggiungibile
  - Un oggetto referenziato da un oggetto raggiungibile è anch'esso raggiungibile
- Dopo aver rilevato tutti gli oggetti raggiungibili l'insieme di oggetti rimasto fuori viene liberato

#### Cause irraggiungibilità

- Causa _sintattica_
  - Non più raggiungibili per _vincoli sintattici_
    - ri-assegnamento di un puntatore o di un riferimento
  - Semplice da verificare
- Cause _semantiche_
  - oggetti non più raggiunti per via del _flusso di codice_
    - esempio: branch di codice mai usato a run time
  - non esiste algoritmo in grado di identificare i semantic garbage per
    ciascun possibile input
    - __Problemi indecidibili__

### Reference counting

- Leggi sopra

### Generetional garbage collection

- Leggi sopra

### _Algoritmi basati su tracing_

- Due distinti algoritmi
  - _Mark and swep_
  - _Tri-color marking_

### Mark and Sweep

- Ad ogni oggetto viene assegnato un flag (bit)
  - Quando viene creato l'oggetto il bit è settato a 0
- Quando l'oggetto viene creato si va nella fase di _mark_
  - Equivale a fare una _visita al grafo_ e si setta al valore 1 i flag dei nodi
    raggiunti dalla ___depth first search___
- Nella fase di _sweep_ gli oggetti non raggiungibili hanno flag a 0
  - Scansiono tutta la memoria, quando trovo un flag a 0 la memoria viene
    recuperata nella memoria libera
    - I flag che erano ad uno vengono resettati a 0
- L'algoritmo viene ripetuto periodicamente

#### Limiti dell'algoritmo

- Semplice ma poco performante
- L'interprete si deve interrompere durante l'esecuzione dell'algortimo
  - Grossa latenza
- Memoria deve essere scansionata più volte
  - Fase di mark
    - Tutti i _living object_
  - Fase di sweep
    - Tutta quandta la memoria
  
### Tri-color marking

- Ideato da `E.W. Dijkstra`
- Algoritmo di tracing
- Oggetti e riferimenti modellati come un grafo orientato
- Visitare un oggetto:
  - Analizzare i riferimenti uscenti verso altri oggetti
- __Nodo finito__
  - Un nodo x si dice _finito_ se tutti i riferimenti da x verso altri nodi sono stati analizzati

#### L'algoritmo

- L'algoritmo suddivide gli oggetti in tre sottoinsiemi
  - bianco
  - grigio
  - nero
- _white set_
  - nodi candidati ad essere eliminati alla fine dell'algoritmo
    - cioè nodi che non sono ancora stati raggiunti
- _grey set_
  - serve all'algoritmo ma alla fine non ci saranno piu nodi grigi
  - nodo che è stato raggiunto ma che non è ancora stato completamente
    vistitato
    - cioè non sono ancora visitati i link uscenti, non è ancora __finito__
  - alla fine diventerà nero
- _black set_
  - nodi raggiunti e __finiti__
  - quelli che alla fine verranno mantenuti

- Inizialmente i nodi del root set sono grigi, tutti gli altri sono bianchi

```
Finché ci sono nodi nel Grey set:
        Scegli un nodo X nel Grey set e spostalo nel Black set
        Identifica tutti i nodi Y1, ..., Yk puntati direttamente da X
        Considera l’insieme di oggetti 
            {W1, ..., Wj} = {Y1, ..., Yk} ∩ White set
        (cioè i nodi bianchi puntati da X)
        e spostali nel Grey set
```

- I nodi che rimarranno bianchi verranno disallocati

- L'algoritmo mantiene la _condizione invariante_ in base alla quale nessun
  nodo nero punta ad un nodo bianco
  - Si può dimostrare per induzione che l'algoritmo è corretto, cioè richiama
    tutti e soli i nodi non più raggiungibili nel programma

#### Dimostrazione (non richiesta vedere notebook)

#### Efficienza algoritmo

- Molto più efficiente del mark and sweep
- Più efficiente del mark and sweep
- L'esecuzione può essere "interallacciata" con la computazione
  dell'interprete
  - Prima colorazione avviene quando un oggetto viene _creato_(e la memoria viene allocata)
  - l'interprete è libero di accedere qualsiasi parte del grafo
    -  I mutator possono allocare nuovi nodi mentre il GC determina quelli raggiungibili
 - L'interprete deve fermarsi solo durante la fase di deallocazione di memoria, ovvero dei nodi bianchi (_la terza_)
   - Il gc potrebbe disallocarlo subito prima del cambio colore

### Reference counting

- **Usato da Python**
- Ad ogni oggetto viene associata una variabile _contatore dei riferimenti(RC)_
- Nel grafo questa variabile _conta il numero di archi entranti nel
  nodo/oggetto_
  - Questa proprietà è la condizione invariante che l'algoritmo deve
    soddisfare
    - Quando il RC di un nodo X arriva al valore 0 siamo certi che non ci sono più oggetti nello heap o nel root set che puntano ad X
      - Quindi X può essere disallocato

#### Inizializzazione del RC

- Effettuata dal mutator quando l'oggetto viene creato
  - E il suo indirizzo assegnato ad una variabile nel root set o inserito in un altro oggetto

#### Operazioni che modificano il RC

- RC può essere cambiato
  - Mediante _assegnamento_
    - un riferimento all'oggetto appare come parte destra di un assegnamento
  - se viene passato come _parametro ad una funzione_
    -  perché l'oggetto viene legato al parametro formale
  - se viene _inserito in un contenitore_ 
    - ad esempio una lista 

#### Vantaggi e svantaggi

- Non dobbiamo attendere l'esecuzione periodica (o a soglia) di un algoritmo
- Memoria liberabile nel momento in cui RC arriva a 0
- Importante proteggere l'accesso all'RC degli oggetti
  - Diversi thread potrebbero voler accedere contemporaneamente ad uno stesso RC
    - _Race condition_
    - Può portare a situazioni di errore 
      - _memory leak, dandling reference_
- Possibile esistenza di _riferimenti circolari_
  - impedisce la corretta de-allocazione di oggetti con rischi di _memory leak_

<img src="images/RC_cycle.jpg" title="Memory leak da riferimenti circolari" width="500">

### _Gestione della memoria dell'interprete CPython_

- Analizziamo come viene organizzata e gestita la memoria a disposizione di un programma
  - Questa dipende dall'___interprete___
    - Ovvero dall'_implementazione_ di Python
    - __CPython__ è quella più diffusa

#### Arena, pool e blocchi

<img src="images/arena_pool_block.jpg">

- Gerarchia di memoria gestita su tre livelli
  - Arene
  - Pool
  - Blocchi
- Arene
  - Al livello più alto della gerarchia
  - porzioni da **256KB** di memoria
  - Tutte le arene sono collegate tra loro mediante una lista
    bidirezionale (doubly-linked-list)
- Ogni arena è suddivisa in _64 pool_ ciascuna di dimensione fissa pari a _4KB_
- Ogni pool è suddiviso in blocchi la cui dimensione è fissata all'interno di ogni pool ma che può essere differente da pool a pool
  - I blocchi possono essere da un _minimo di 8 byte_ fino a un _massimo di 512 bytes_
  - Se vengono richiesti k byte (con k <= 512)
    - L'allocazione si traduce in $b_k=\left\lfloor\frac{k-1}{8}\right\rfloor+1$ byte
  - Un pool può essere in tre stati
    - _**in uso**_
      - Blocchi allocati ma anche blocchi liberi
      - questi sono organizzati in una struttura a due livelli
        - una lista semplice
          - per i free pools
        - una lista doppia di pool in uso
          - mentre l'inserimento può sempre avvenire in testa, l'eliminazione può avvenire in qualsiasi posizione e la lista doppia consente di farlo in tempo costante
    - _**completo**_
      - non necessitano di esssere tenuti in nessuna struttura particolare
    - _**vuoto**_
      - vuoti e completi vengono tenuti in una lista lineare
        (unidirezionale)
  - Un pool è una struct C che mantengono puntatori a pool precedente e successivo
  - La classe di un pool viene determinata in base alla "prima" richiesta di allocazione

<img src="images/pool_states.jpg" width="500" align="center">

- I blocchi sono di 3 tipi
  - Occupati(verde)
  - Mai allocati(celeste)
  - Liberi(verdino)
    - Ovvero che erano stati occupati in precedenza ma ora sono liberi
  - Situazione ideale
    - Verdi sotto/Celesti sopra

---
- Quando viene fatta una richiesta di memoria di una certa classe
  - Si va  a vedere se ci sono blocchi liberi nel pool

  - I blocchi allocati possono essere inframezzati da blocchi liberi
    - Se ho solo blocchi occupati allocherò il primo blocco celeste
    - Nella struttura che definisce il pool trovo:
      - Numero blocchi mai allocati
      - Puntatore di testa a una lista libera di blocchi liberi
        - questa lista ha la precedenza rispetto ai blocchi azzuri quando viene richiesta l'allocazione
- Se arriva una richiesta di disallocazione ad un blocco in un pool full
  - quello andrà a finire nella lista degli used pools

---
 
##### Vantaggi e svantaggi

- Tutto molto veloce e efficente 
- Limita il problema della frammentazione
- Ma
  - Ci può essere un uso inefficente della memoria
    - Potrei avere pool di ogni classe ma con un blocco solo per ogni pool
  - Python non restituisce mai memoria al sistema operativo
    - Difficile che si rilascino tutti i pool di una stessa arena

### _Garbage Collection CPython_

- Con il solo approccio reference count gli oggetti che fanno parte di cicli di riferimenti non possono essere disallocati
- CPython utilizza un algoritmo di _rilevamento dei cicli_
  - Costituisce propriamente il _Garbage collector di (C)Python_
  - Viene usato un algoritmo di tracing all'interno di un approccio generazionale
    - Oggetti suddivisi in base alla loro "antichità"

#### Rilevamento dei cicli

- I cicli possono esserci solo se ci sono degli oggetti contenitori(container)
  - Oggetti non immutable
- Gli oggetti container vengono mantenuti in una _double-linked-list L_
  - C'è piu di una L ma assummiamo che ce ne sia solo una
- Il GC(rilevamento dei cicli) viene attivato quando il numero di elementi nella lista supera una certa _soglia_

<br>
 
```
1. Viene fatta una copia del reference counting di ogni oggetto chiamato RC_L
2. Viene fatta una scansione di L, per ogni oggetto X che si trova l'algoritmo va
 a esaminare gli oggetti che sono referenziati da X e si decrementa di 1 il
  valore RC_L di questi ultimi
  - Alla fine il valore del contatore ci dirà se ci sono riferimenti che arrivano dall'esterno
3. Scansiono L una seconda volta e per ogni oggetto X esamino il valore RC_L
  - se RC_L = 0, etichetto l'oggetto come presumibilmente irraggiungibile
  - se RC_L > 0 e X non è già stato etichettato, allora lo etichettiamo come raggiungibile
    - Allora saranno raggiungibili tutti gli oggetti in L raggiungibili da X
      - Si fa una depth first search partendo da X in tutti gli oggetti Y raggiungibili e si etichettano tutti come raggiungibili (ecco perchè presumibilmente)
  - se RC_L > 0 e X è già marcato (ovviamente come raggiungibile) passo all'oggetto successivo
4. Quando l'algritmo termina gli oggetti che rimangono presumibilmente irraggiungibili sono effettivamente tali e possono essere disallocati
```

## _Generational Garbage Collection_

- Oggetti divisi in generazioni
- Vengono considerate 3 o 4 generazioni
- Idea euristica supportata da studi sperimentali
  - Più un oggetto sta in memoria più è probabile che ci serva
  - Molti oggetti che vengono creati muoiono subito
    - e.g.: oggetti creati localmente

### Esecuzione algoritmo

- Il GC viene eseguito su una generazione
  - Se è raggiungibile viene spostato in una generazione successiva
    - Ovvero più anziana (a patto che ci sia)
  - Altrimenti viene disallocato

### Vantaggi/Svantaggi

- Ogni ciclo può essere fatto su un numero minore di oggetti
  - Quindi più veloce
- A seconda delle soglie però può essere invocato più frequentemente
- Minore precisione
- Spesso vengono usati approcci ibridi
  - Ogni tanto si fa una full-collection

## Approccio generazionale in CPython

- CPython usa di default 3 generazioni
  - 0, 1, 2 (0 è la più giovane)
- Gli oggetti di ogni generazione sono tenuti in una lista doppia
  - Algoritmo eliminazione dei cicli è embedded in un approccio generazionale
  - Ci stanno solo gli oggetti contenitori

### Cicli di attivazione

- La garbage collection su una generazione g > 0 viene fatta su tutte le generazioni i <= g
- Quando inizia la gc su g vengono unite le liste relative alla generazione g e, eventualmente, a quelle più giovani
- Una collection 

### Le soglie

- I valori default sono 700, 10, 10
  - La soglia per la prima generazione (700) è un valore assoluto
  - Le soglie per le generazioni successiva indicano invece il numero di attivazioni del GC sulla generazione precedente
    - Il gc sulla generazione

### La old generation

- La generazione 2 esiste una condizione ulteriore
  - Una full collection viene eseguita solo se più del 25% deglio oggetti sono stati allocati a partire dalla ultima full collection