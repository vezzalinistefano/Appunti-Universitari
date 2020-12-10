# Comple

## Metodi statici o metodi di classe

- In python tutto è oggetto
  - Distinzione tra metodi statici e non va contestualizzata
- Primo parametro != da self
- Può alterare lo stato della classe

### Usi tipici

- _factory method_
  - Usati come mezzi per creare oggetti
- Metodi di classe e metodi statici non sono nativi

### Esempi

## Overriding di operatori

### Operatori di confronto

- Attraverso la redifinizione dei magic method `__eq__, ...` possiamo modificare il comportamento degli operatori di confronto
  - (==, >=, <=, ecc)
- Se faccio ad esempio `3 < 4` dobbiamo pensare che 3 e 4 sono oggetti!

#### Esempio

#### Esempio2: ordinamento di archi di un grafo pesato

- Algoritmo di sorting
  - Prende due oggetti a coppie e applica gli operatori di confronto **degli oggetti**

### Operatori aritmetici

- Attraverso la redifinizione di magic method... è possibile ridefinire il comportamento degli operatori aritmetici

### Commutatività

## Contenitori

- Attraverso la redifinizione dei metodi ...
- Contenitori immutable non implementano `__setitem__, __delitem__`

### `__getitem__`

- Viene eseguito quando l'interprete incontra la scrittura `[]` che altro non è se non zucchero sintattico
- `obj[k]` è uguale a dire `obj.__getitem__(k)`

## Rappresentazione

- Modi in cui voglio rappresentare un oggetto

### `__str__` e `__repr__`

- __str__ è un interpretazione human readable

### `__format__`

- quando un'istanza della classe è utilizzata in una stringa di formato
  - Ad esempio in una f-string

### `__hash__`

- Viene invocato come implementazione della funzione hash
- Gli oggetti che implementano il metodo __hash__ possono essere usati come chiavi di un dizionario
  - Detti hashable object
  - Ricorda sono oggetti immutable