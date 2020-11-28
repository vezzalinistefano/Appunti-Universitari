- [Tipizzazione](#tipizzazione)
  - [_Tipi di dato_](#tipi-di-dato)
  - [_Conseguenze tipizzazione_](#conseguenze-tipizzazione)
    - [Sicurezza](#sicurezza)
    - [Ottimizzazione](#ottimizzazione)
    - [Astrazione](#astrazione)
    - [Documentazione](#documentazione)
    - [Modularità](#modularità)
  - [Quando viene eseguito il type checking?](#quando-viene-eseguito-il-type-checking)
    - [Type checking statico](#type-checking-statico)
    - [Type checking dinamico](#type-checking-dinamico)
      - [Type checking dinamico: servono test accurati](#type-checking-dinamico-servono-test-accurati)
    - [Type checking ibrido](#type-checking-ibrido)
  - [Tipizzazione Forte](#tipizzazione-forte)
  - [Tipizzazione debole](#tipizzazione-debole)
  - [Tipizzazione safe/unsafe](#tipizzazione-safeunsafe)
  - [Tipizzazione e polimorfismo](#tipizzazione-e-polimorfismo)
    - [Polimorfismo classico ad oggetti](#polimorfismo-classico-ad-oggetti)
  - [_Duck typing_](#duck-typing)
    - [Duck test](#duck-test)
    - [Senza Duck Typing](#senza-duck-typing)
    - [Con Duck Typing](#con-duck-typing)
  - [Tipizzazione e polimorfismo #2](#tipizzazione-e-polimorfismo-2)
  - [Alternative per la programmazione generica](#alternative-per-la-programmazione-generica)

# Tipizzazione

## _Tipi di dato_

- Caratteristica che permette di controllare la correttezza delle operazioni
  svolte.
- La tipizzazione è il processo in base al quale il compilatore o interprete
  determina il tipo di un dato, sia esso dichiarato o prodotto/calcolato, in modo da poter effettuare i relativi controlli

## _Conseguenze tipizzazione_

- Sicurezza
- Ottimizzazione
- Astrazione
- Documentazione
- Modularità

### Sicurezza

- Tipizzazione permette di scoprire codice illecito o privo di senso
- Effettuata a tempo di compilazione permette di ridurre errori o risultati
  inattesi a runtime

### Ottimizzazione

- Tipizzazione a tempo di compilazione può permettere _tecniche di ottimizzazione_
- Ad esempio l'istruzione `x*2` può essere ottimizzata:

$$mul \enspace x,2 \rightarrow shift\_left \enspace x$$

### Astrazione

- Tipi di dato permettono di produrre programmi ad un livello di astrazione più
  alto.
- ad esemopio: assenza di tipizzazione
  - unico tipo di dato sono bit e byte

### Documentazione

- Nomi dei tipi di dato possono servire quale fonte di _documentazione del codice_
  - Il tipo di dato illusta la natura di una variabile
- Esempio `timestamp`

### Modularità

- L'uso appropriato dei tipi di dato costituisce uno strumento semplice e robusto per definire interfacce di programmazione (API)

## Quando viene eseguito il type checking?

- Può avvenrie
  - a tempo di compilazione
  - a tempo di esecuzione
- Sono possibili soluzioni miste

### Type checking statico

- Se le operazioni di controllo dei tipi sono eseguite solo a tempo di compilazione
  - C++/C
  - Pascal
  - ...
- Permette di individuare gli errori in largo anticipo
- forma più sicura di verifica del programma
- Il type checking statico peremette inoltre migliori prestazioni

### Type checking dinamico

- Type checking eseguito a tempo di esecuzione
- Nel t.c. dinamico non serve dichiarare il tipo di dato
- Le variabili possono cambiare tipo a run time
- Pssibilità di realizzare strutture complesse
- Vantaggi in fase di codifica
  - maggior overhead a tempo di esecuzione
- Richiedono un efficiente gestione della memoria

#### Type checking dinamico: servono test accurati

- Aumenta probabilità di input inaspettati
- Molto più importante la fase di testing del sw
- Necessario prevedere più condizioni inaspettate
  - Maggiore necessità di :
  - Trattare le situazioni di errore a runtime
    - Gestione delle eccezioni!
  - Verificare la correttezza con testing accurati

### Type checking ibrido

- Ne fa uso Java
  - Anche se è comunque considerato a type checking statico
  - Checking statico dei tipi
  - Checking dinamico delle operazioni di binding tra metodi e classi

## Tipizzazione Forte

- Un linguaggio che impone regole molto rigide ed impedisce usi incoerenti dei
  tipi di dato specificati
- Un linguaggio completamente tipizzato sarebbe quesi totalmente inutilizzabile
- Python tipizzazione forte ma tutta dinamica

## Tipizzazione debole

- Se non impedisce operazioni incongurenti
- Fa uso di operatori di conversione(_casting implicito_) per rendere omogenei
  gli operandi


## Tipizzazione safe/unsafe

- Se non permette ad un operazione di casting implicito di produrre un crash
- Un linguaggio di programmazione adotta una tipizzazione unsafe (non sicura) dei dati se consente operazioni di casting che possono produrre un crash

## Tipizzazione e polimorfismo

- _Polimorfismo_
  - Capacità di differenziare parti del codice in base all'entità a cui
    sono applicati
  - In java viene legato al meccanismo di ereditarietà

### Polimorfismo classico ad oggetti

```
int a = 1;
int b = 3;
float c = 5; 
float d = obj.m(a,b,c)
```

- Applicare polimorfismo e trovare una signature valido implica
  - individuare la classe di appartenenza dell'oggetto, e cercarvi una signature valida per il metodo (nel caso dell'esempio `m(int,int,float) → float`)
  - in caso di mancata individuazione, ripetere il controllo per tutte le superclassi (**operation dispatching**) potenzialmente risalendo fino alla classe radice)
- La ricerca della classe adatta può essere fatta a _compile time_ o a
  _run-time_
  - _Java_ lo fa a tempo di esecuzione, mentre molto altro viene fatto a
    tempo di compilazione
- **Nei linguaggi dinamici si può percorrere la via alternativa del Duck typing**

## _Duck typing_

### Duck test

<center>
"When I see a bird that walks like a duck and swims like a duck and quacks like a duck, I call that bird a duck"
</center>

### Senza Duck Typing

```
Interface IoStarnazzo {
    void starnazzo();
} 

Void f(IoStarnazzo x) {
    x.starnazzo();
}
```

- Una chiamata `f(1)` fallirebbe mentre `f(Paperino)` funziona o fallisce a seconda del fatto che Paperino sia un’istanza di una sottoclasse di `IoStarnazzo`

### Con Duck Typing

- Esempio: consideriamo le classi `Animale`, `Cane` e `Gatto`
- Una funzione `funz()` prende in ingresso un parametro formale **non tipizzato** e ne invoca il metodo `CosaMangia()`

```
def funz(a):
  ...
  a.CosaMangia()
  ...
```

- Non serve che Cane e Gatto siano sottoclassi di Animale per essere passate
  a funz() ed utilizzate
  - Basta che abbiano metodo CosaMangia
  - All'interprete interessa solo che i tipi di oggetto espongano un metodo con lo stesso nome e con lo stessa numero di parametri in ingresso 

- esempio

  ```
  class Duck:
      def quack(self): 
          print("Quaaaaaack!")
  class Person:
      def quack(self):
          print("The person imitates a duck.")
  class Dog:
      def bark(self):
          print("bow wow")
  def in_the_farm(a):
      a.quack()
  ```

  ```
  a = Duck()
  in_the_farm(a)
  ```

  ```
  a = Person()
  in_the_farm(a)
  ```

- A run time l'interprete controlla che il metodo `quack()` sia implementato
  sia in `Duck` che in `Person` (_duck test_)
- Abbiamo la libertà di non dover tipizzare il parametro a per non dover
  chiamare `quack()` solo con istanza di un certo tipo

## Tipizzazione e polimorfismo #2

- Permette di
  - differenziare il comportamento dei metodi in funzione del tipo di dato a cui sono applicati
  - evitare di dover predefinire un metodo, classe, o struttura dati appositi per ogni possibile combinazione di tipi di dati 
- Funzionale al possibile riutilizzo di codice
- In un linguaggio dinamico, tutte le espressioni sono _intrinsecamente polimorfiche_
  - Esempio, consideriamo una generica funzione:
    ```
    calcola(a,b,c)
        return (a+b)*c
    ```
    - In Python la funzione può essere chiamata con almeno tre tipi di parametri
      - `e1 = calcola(1,2,3)`
      - `e2 = calcola([1,2,3],[4,5,6],2)`
      - `e3 = calcola('mele ', 'e arance ', 3)`
    - Naturalmente con diversi risultati

## Alternative per la programmazione generica

- Ad esempio, C++ e Java usano Template e Generics in cui anche il tipo è
  parametrico
  - Questo tipo parametrico sarà poi sostituito a tempo di compilazione da
    un tipo specifico
