
Le dimensioni delle macchine virtuali ottimizzate per la GPU sono destinate alle macchine virtuali specializzate per livelli intensivi di rendering della grafica e modifica di video, disponibili con GPU singole o più GPU. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, nonché sulla velocità effettiva di archiviazione e sulle prestazioni di rete per ogni dimensione di questo raggruppamento. 


Le dimensioni NC e NV sono dette anche istanze abilitate tramite GPU. Si tratta di dimensioni di macchine virtuali specializzate dotate di schede GPU NVIDIA e ottimizzate per diversi scenari e casi d'uso. Le dimensioni NV sono ottimizzate e progettate per le operazioni di visualizzazione remota, streaming, giochi, codifica e scenari VDI che utilizzano framework come OpenGL e DirectX. Le dimensioni NC sono più adatte per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, tra cui applicazioni e simulazioni basate su CUDA e OpenCL. 


Le istanze NV sono basate su GPU NVIDIA Tesla M60 e NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. Il modello Tesla M60 ospita 4.096 core CUDA in una dual GPU con un massimo di 36 flussi H264 da 1080p. 

Le istanze NC sono basate sulla scheda NVIDIA Tesla K80. Gli utenti possono ora elaborare i dati molto più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, analisi dettagliate e altro ancora. Il modello Tesla K80 ospita 4.992 core CUDA in una dual GPU con prestazioni che raggiungono 2,91 teraflop di precisione doppia e 8,93 teraflop di precisione singola.

## <a name="nv-instances"></a>Istanze NV

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1.440 | 4 | 64 |

1 GPU = Pari a metà scheda M60.

## <a name="nc-instances"></a>Istanze NC

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 64 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA


