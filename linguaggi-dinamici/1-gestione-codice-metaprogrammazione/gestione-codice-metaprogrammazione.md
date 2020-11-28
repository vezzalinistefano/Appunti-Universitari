- [Gestione del codice e meta-programmazione](#gestione-del-codice-e-meta-programmazione)
  - [*Gestione del codice*](#gestione-del-codice)
  - [*Introspezione*](#introspezione)
    - [Auto-completion](#auto-completion)
    - [Duck typing in python](#duck-typing-in-python)
    - [Lettura scrittura attributi con nomi letti a run-time](#lettura-scrittura-attributi-con-nomi-letti-a-run-time)
    - [Esempio: Simulazione del costrutto *switch/case*](#esempio-simulazione-del-costrutto-switchcase)
    - [Strumenti per la instrospection in python](#strumenti-per-la-instrospection-in-python)
    - [Reflection](#reflection)
      - [L'attributo code](#lattributo-code)
  - [Le funzioni built-in exec e eval](#le-funzioni-built-in-exec-e-eval)
- [Gestione degli errori](#gestione-degli-errori)
  - [Sollevamento manuale eccezioni](#sollevamento-manuale-eccezioni)

# Gestione del codice e meta-programmazione

## *Gestione del codice*

* Gestione del codice
  * Capacità del codice di auto analizzarsi
  * Meta programmazione = programmazione sulla programmazione
    1. Generazione e modifica di codice a tempo di esecuzione
    2. Intercettazione degli errori
    3. auto-analisi del codice a tempo di esecuzione

## *Introspezione*

* Studio caratteristiche oggetti a tempo di esecuzione 
* *type-checking dinamico*
  * fondamentalmente il compilatore non analisi i tipi di dato a tempo di compilazione
    * Viene tutto fatto a tempo di esecuzione
* Propietà tipica dei linguaggi dinamici
  * Più dinamicità $\rightarrow$ più introspezione
* Introspezione c'è anche in `java` ma non nativamente

### Auto-completion

* Viene realizzata con tecniche di introspezione
  1. recuperati metodi pubblici oggetto
  2. si controlla se sono collable
  3. se ne presentano i nomi

### Duck typing in python

* `O` è un oggetto python
  * Scriviamo `O.write()`
  * Il compilatore non esegue controlli sulla presenza del metodo
  * L'eventuale errore si verificherà a tempo di esecuzione
    * se in O o nelle classi di riferimento non viene trovato il metodo write
* Possimao gestire la situazione "intrappolando" l'eventuale eccezione
* Oppure controllando se O ha il metodo write
  * Usiamo il metodo built-in `hasattr`
    * `hasattr(O, 'write')`
  * Si prediligie però l'uso del `try`

### Lettura scrittura attributi con nomi letti a run-time

* Configurare oggetto in seguito alla lettura di un *file di configurazione* o in seguito a una *richiesta http*

### Esempio: Simulazione del costrutto *switch/case*

* Definiamo una generica classe `switch`
  * Questa classe non dovrà essere riscritta al variare delle possibili condizioni
  * Le istanze saranno le singole condizioni


```python
class Switch
    
```

* Definiamo una sottoclasse che include la definizione dei vari casi*
  * Nuovi casi potranno poi essere aggiunti (o rimossi)
  
```python
class TestConditions
```

* Esempio di utilizzo:
  * Il sw di utilizzo è indipendente dal numero e dal nome attribuito alle varie condizioni

```python
```

> * join prende tutti gli elementi di una lista separandoli con il carattere specificato

### Strumenti per la instrospection in python

* type(o) ci dice il tipo di un oggetto
* `isinstance(o,t)`
  * `t` -> tipo
  * restituisce `True` se o è istanza di t, `False` altrimenti
* `o.__dict__` è un attributo che è un dizionario
  * coppia chiave valore di tipo nome_attributo: valore_attributo
* `dir()/dir(o)`
  * senza parametri restituisce i nomi dell'ambiente corrente
  * con un parametro restituisce la lista degli attributi di quell'oggetto
* locals() e globals() per accedere ai diversi mainspace
* hasattr
* getattr
* callable
* obj.__class__
* obj.__class__.__name__
* id(obj)

### Reflection

* capacità di modificare struttura e comportamento a run-time

#### L'attributo code

* è possibile intervenire sul codice utilizzando l'attributo __code__

## Le funzioni built-in exec e eval

* `eval` valuta l'espressione fornita come stringa e la restituisce
  * è possibile fornire un namespace in cui valutare l'espressione
* `exec` esegue codice aribitrario
  * sempre fornito come stringa

# Gestione degli errori

* meccanismi per intercettare e gestire situazioni anomale a run-time
* Eccezioni basate su classi
    * Gerarchia di classe che eredita da root
    * La radice è una classe detta `exception`
        * Fornisce una rappresentazione dello stack

* Le eccezioni sono sollevate
    * Automaticamente
    * Manualmente con istruzioni come `throw` e `rise`

* L'eccezione può poi essere gestita attraverso un opportuno sw di gestione

* Python costrutto `try-except-finally`
    * Un `except` può gestire una o più eccezioni

## Sollevamento manuale eccezioni

```python
raise ExceptionName(arg1, arg2)
```

* Creo un istanza di classe `ExceptionName` e sollevo l'eccezione
* Posso quindi creare le mie speciali eccezioni
* Recuperare argomenti eccezioni
    
```python
except NameException as inst:    
```

* Dove `inst` è l'istanza dell'eccezione e `inst.args` è una tupla che contiene
gli argomenti passati.


























