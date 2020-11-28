## Discounted comulative gain
* misura per valutare web search
* La rilevanza non è solo un concetto on off
  * esiste rilevanza parziale
* due assunzioni
  * i documenti piu rilevani sono piu utili dei meno rilevanti
  * più in basso finisce un documento rilevante meno è utile per l'utente
    * più difficilmente verrà raggiunto e esaminato
* Possiamo associare ad ogni documento una misura di utilità, chiamata gain
  * la rilevanza viene usata come utilità
* Il guadagno è accumulato partendo dalla cima del ranking
  * Può essere scontato a rank più bassi
  * Sconto tipico: $1/log(rank)$ 

### DCG

* Comulative Gain(CG) al rank n
  * $r_1, r_2, ..., r_n \rightarrow$ rating degli n documenti 
  * $CG = r_1 + r_2 + r_3 + ... + r_n$
* Discountive CG al rank n
  * $DCG = r_1 +r_2/log_22 + ... + r_n/log_nn$
    * Per il logaritmo possiamo usare qualsiasi base

* Il DCG è il guadagno accumulato dall'utente ad una certa posizione *p*
  * più si avanza più è basso

$$DCG_p = rel_1 + \sum_{i=2}^p\frac{rel_i}{log_2i}$$

  * Formula alternativa
    * usata da alcune compagnie di web seacbing
    * enfasi sul recupero di documenti molto rilevanti

  $$DCG_p = \sum_{i=2}^p\frac{2^{rel_i}-1}{log(i+1)}$$

### NDCG

* Normalized Discounted Cumulative Gain (NDCG) al rank n
* La normalizzazione viene fatta su ranking ideale
  * normalizzo DCG al rank n per il valore DCG al rank n del ranking ideale
  * Il ranking ideale restituisce prima i documenti con il ranking piu alto poi con il ranking successivo e cosi via
* Normalizzazione utile per query con vari numeri di risultati rilevanti

### Giudizio umano

* Costoso
  * Chiedere a delle persone di valutare sistemi
* Inconsistenza
  * Nel tempo
  * Tra i valutatori
* Gli utenti coinvolti non sempre rappresentano gli utenti reali
* Alternativa: vedere gli USER CLICKS

