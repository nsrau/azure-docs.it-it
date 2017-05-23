Le dimensioni NC e NV sono dette anche istanze abilitate tramite GPU. Si tratta di macchine virtuali specializzate dotate di schede GPU NVIDIA e ottimizzate per diversi scenari e casi d'uso. Le dimensioni NV sono ottimizzate e progettate per le operazioni di visualizzazione remota, streaming, giochi, codifica e scenari VDI che utilizzano framework come OpenGL e DirectX. Le dimensioni NC sono più adatte per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, tra cui applicazioni e simulazioni basate su CUDA e OpenCL. 


Le istanze NV sono basate su GPU NVIDIA Tesla M60 e NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti potranno visualizzare i propri dati o le proprie simulazioni. Gli utenti potranno visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore e inoltre eseguire singoli carichi di lavoro di precisione, come la codifica e il rendering. Il modello Tesla M60 ospita 4.096 core CUDA in una dual GPU con un massimo di 36 flussi H264 da 1080p. 

Le istanze NC sono basate sulla scheda NVIDIA Tesla K80. Gli utenti possono ora elaborare i dati molto più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, analisi dettagliate e altro ancora. Il modello Tesla K80 ospita 4.992 core CUDA in una dual GPU con prestazioni che raggiungono 2,91 teraflop di precisione doppia e 8,93 teraflop di precisione singola.

## <a name="nv-instances"></a>Istanze NV

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 64 |
| Standard_NV12 |12 |112 |680 | 2 | 64 |
| Standard_NV24 |24 |224 |1.440 | 4 | 64 |

1 GPU = Pari a metà scheda M60.

## <a name="nc-instances"></a>Istanze NC

| Dimensione | Core CPU | Memoria: GiB | Unità SSD locale: GiB | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 12 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 48 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 48 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA


