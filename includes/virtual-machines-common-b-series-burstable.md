
La famiglia di macchine virtuali della serie B consente di scegliere le dimensioni di VM che offrono le prestazioni di livello base necessarie per il carico di lavoro, con la possibilità di aumentare le prestazioni della CPU fino al 100% per una vCPU con processore Intel® Broadwell E5-2673 v4 2,3GHz o Intel® Haswell 2,4 GHz E5-2673 v3.

Le VM della serie B sono ideali per carichi di lavoro che non necessitano in modo continuativo delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. La serie B offre la possibilità di acquistare una dimensione di VM con prestazioni baseline e l'istanza della VM accumula crediti quando il relativo utilizzo è inferiore alla baseline. Quando la VM ha accumulato crediti, può eseguire il burst rispetto alla baseline, usando fino al 100% della vCPU quando l'applicazione richiede prestazioni superiori per la CPU.

La serie B offre le sei dimensioni seguenti per le macchine virtuali:

| Dimensione          | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Prestazioni base della CPU della VM | Prestazioni massime della CPU della VM | Crediti accumulati/Ora | Crediti massimi accumulati |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Domande e risposte 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>D: Come si ottengono prestazioni baseline del 135% da una VM?
**R**: La quantità pari al 135% è condivisa tra 8 vCPU che costituiscono le dimensioni della VM. Se, ad esempio, l'applicazione sfrutta 4 degli 8 core dedicati all'elaborazione batch e ognuna di queste 4 vCPU è in esecuzione con un livello di utilizzo pari al 30%, la quantità totale di prestazioni della CPU della VM equivale al 120%.  La macchina virtuale, quindi, accumula tempo di credito in base al differenziale del 15% rispetto alle prestazioni baseline.  Quando sono disponibili crediti, la stessa VM può quindi usare il 100% di tutte le 8 vCPU, ottenendo un livello massimo di prestazioni della CPU pari all'800%.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>D: Come si possono monitorare il saldo e l'utilizzo del credito?
**R**: Nelle prossime settimane verranno introdotte 2 nuove metriche, ovvero la metrica **Credit** che consentirò di visualizzare la quantità di crediti accumulati dalla VM e la metrica **ConsumedCredit** che mostrerà la quantità di crediti di CPU utilizzata dalla VM rispetto alla quantità accumulata.    Sarà possibile visualizzare queste metriche dal riquadro delle metriche nel portale o a livello di programmazione tramite le API di Monitoraggio di Azure.

Per altre informazioni su come accedere ai dati delle metriche per Azure, vedere [Panoramica delle metriche in Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>D: In che modo vengono accumulati i crediti?
**R**: La velocità di accumulo e di utilizzo delle VM viene impostata in modo che una VM in esecuzione esattamente al rispettivo livello di prestazioni di base non avrà alcun accumulo netto o alcun utilizzo di crediti di burst.  Una VM presenterà un incremento netto in crediti in caso di esecuzione a un livello inferiore al rispettivo livello di prestazioni di base e presenterà una riduzione netta in crediti in caso di utilizzo della CPU da parte della VM a un livello superiore rispetto al rispettivo livello di prestazioni di base.

**Esempio**: Si distribuisce una VM usando le dimensioni B1ms per una piccola applicazione di database relativa a orari e presenze. Queste dimensioni consentono all'applicazione di usare fino al 20% di una vCPU come baseline, che corrisponde a 0,2 crediti al minuto utilizzabili o accumulabili. 

L'applicazione è occupata all'inizio o alla fine della giornata lavorativa dei dipendenti, tra le 7:00-9:00 e le 16:00-18:00. Durante le altre 20 ore della giornata l'applicazione è solitamente inattiva e usa solo il 10% della vCPU. Per gli orari non di picco si accumulano 0,2 crediti al minuto ma si utilizzano solo 0,1 crediti al minuto, quindi la VM accumulerà 0,1 x 60 = 6 crediti all'ora.  Per le 20 ore non di picco, si accumuleranno 120 crediti.  

Durante le ore di picco l'applicazione utilizza in media il 60% della vCPU, quindi si accumulano comunque 0,2 crediti al minuto ma si utilizzano 0,6 crediti al minuto, per un costo netto di 0,4 crediti al minuto o 0,4 x 60 = 24 crediti all'ora. Ogni giorno presenta 4 ore di picco di utilizzo, quindi il costo del picco di utilizzo è pari a 4 x 24 = 96 crediti.

Se si considerano i 120 crediti accumulati negli orari non di picco e si sottraggono i 96 crediti usati per le ore di picco, ogni giorni si accumulano 24 crediti che possono essere usati per altri burst delle attività.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>D: La serie B supporta i dischi dati di Archiviazione Premium?
**R**: Sì, tutte le dimensioni della serie B supportano dischi dati di Archiviazione Premium.   
    


    

    
