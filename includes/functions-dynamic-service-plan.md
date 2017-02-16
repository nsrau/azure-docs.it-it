## <a name="dynamic-service-plan"></a>Piano di servizio Dynamic
Nel piano di servizio Dynamic, le app di funzione verranno assegnate a un'istanza dell'applicazione di funzione. Se sono necessarie più istanze, queste verranno aggiunte in modo dinamico.
Tali istanze possono estendersi su più risorse di calcolo, sfruttando al meglio l'infrastruttura di Azure. Le funzioni verranno eseguite in parallelo riducendo al minimo il tempo totale necessario per elaborare le richieste. Il tempo di esecuzione per ogni funzione aumenta, in intervalli di secondi, e viene aggregato dall'app di funzione che contiene le richieste, con il costo definito dal numero di istanze, dalle dimensioni della memoria e dal tempo di esecuzione totale espresso in secondi di Gigabyte. Si tratta di un'ottima scelta se le esigenze di calcolo sono discontinue o le tempistiche del processo tendono a essere molto brevi, poiché consente di pagare solo per le risorse di calcolo quando vengono effettivamente usate.   

### <a name="memory-tier"></a>Livello della memoria
A seconda delle esigenze di funzionalità, è possibile selezionare la quantità di memoria necessaria per eseguire le risorse nell'applicazione di funzione (contenitore di funzioni).
Le opzioni di dimensioni della memoria variano **da 128 MB a 1536 MB**. La dimensione della memoria selezionata corrisponde al Working Set necessario per tutte le funzioni che fanno parte della propria applicazione di funzione. Se il codice richiede maggior quantità di memoria rispetto alla dimensione selezionata, l'istanza dell'app di funzione verrà arrestata a causa della mancanza di memoria disponibile.

### <a name="scaling"></a>Ridimensionamento
La piattaforma Funzioni di Azure valuterà le esigenze di traffico, in base ai trigger configurati, per decidere quando scalare verso l'alto o verso il basso. La granularità della scalabilità è l'applicazione di funzione. In questo caso, scalare verso l'alto significa aggiungere più istanze di un'applicazione di funzione. Al contrario, quando il traffico diminuisce, le istanze dell'app di funzione vengono disabilitate, arrivando fino a zero quando non in esecuzione.  

### <a name="resource-consumption-and-billing"></a>Uso di risorse e fatturazione
In modalità Dynamic, l'allocazione delle risorse avviene in modo diverso rispetto al piano di servizio app Standard, pertanto anche il modello d'uso è differente e risponde a una logica di "pagamento a consumo". L'uso viene segnalato per ogni app di funzione mentre, per l'esecuzione del codice, l'uso viene segnalato in tempo.  
L'uso viene calcolato moltiplicando le dimensioni della memoria (in GB) per la quantità totale di tempo di esecuzione (in secondi) per tutte le funzioni in esecuzione all'interno di dell'app di funzione. L'unità di misura dell'uso è **GB-s (Gigabyte secondi)**.



<!--HONumber=Jan17_HO3-->


