
Le dimensioni delle macchine virtuali ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica. Questo articolo offre informazioni sul numero e sul tipo di GPU, vCPU, dischi dati e schede di interfaccia di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

* Le dimensioni **NC, NCv2 e ND** sono adatte per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, tra cui applicazioni e simulazioni basate su CUDA e OpenCL, intelligenza artificiale e Deep Learning. 
* Le dimensioni **NV** sono ottimizzate e progettate per le operazioni di visualizzazione remota, streaming, giochi, codifica e scenari VDI che utilizzano framework come OpenGL e DirectX.  


## <a name="nc-instances"></a>Istanze NC

Le istanze NC sono basate sulla scheda [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf). Gli utenti possono elaborare i dati più velocemente sfruttando i core CUDA per eseguire l'analisi del consumo delle applicazioni, simulazioni di arresto anomalo, rendering con ray tracing, Deep Learning e altro ancora. La configurazione NC24r offre un'interfaccia di rete ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.


| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1.440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1.440 | 4 | 64 |

1 GPU = Pari a metà scheda K80.

*Con supporto di RDMA

## <a name="ncv2-instances"></a>Istanze NCv2

Le istanze NCv2 sono la nuova generazione dei computer della serie NC, basate su GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Queste GPU possono offrire prestazioni di calcolo più che raddoppiate rispetto a quelle dell'attuale serie NC. I clienti possono sfruttare i vantaggi di queste GPU aggiornate per carichi di lavoro HPC tradizionali, come la modellazione delle riserve, il sequenziamento del DNA, l'analisi di proteine, le simulazioni Monte Carlo e altro ancora. Analogamente alla serie N, la serie NVv2 offre una configurazione con un'interfaccia ad alta velocità effettiva e a bassa latenza, ottimizzata per carichi di lavoro di calcolo paralleli strettamente associati.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) nella sottoscrizione è impostata inizialmente su 0 in ogni area. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 |

1 GPU = Pari a una scheda P100.

*Con supporto di RDMA

## <a name="nd-instances"></a>Istanze ND

Le macchine virtuali della serie ND sono una novità della famiglia di GPU progettata per carichi di lavoro di intelligenza artificiale e Deep Learning. Offrono prestazioni ottimali per il training e l'inferenza. Le istanze ND sono basate su GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Queste istanze offrono prestazioni eccellenti per le operazioni con precisione singola e virgola mobile e per carichi di lavoro di intelligenza artificiale che usano Microsoft Cognitive Toolkit, TensorFlow, Caffe e altri framework. La serie ND offre anche dimensioni di memoria di GPU significativamente superiori (24 GB), ottimali per modelli di rete neurale molto più grandi. Analogamente alla serie NC, la serie ND offre una configurazione con latenza bassa secondaria, rete con velocità effettiva elevata tramite RDMA e connettività InfiniBand, in modo che sia possibile eseguire processi di training su vasta scala per molte GPU.

> [!IMPORTANT]
> Per questa famiglia di dimensioni la quota di vCPU (core) per area nella sottoscrizione è impostata inizialmente su 0. [Richiedere un aumento della quota di vCPU ](../articles/azure-supportability/resource-manager-core-quotas-request.md) per questa famiglia in un'[area disponibile](https://azure.microsoft.com/regions/services/).
>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 336 | 1 | 12 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 |
| Standard_ND24s |24 |448 | 1344 | 4 | 32 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 |

1 GPU = Pari a una scheda P40.

*Con supporto di RDMA

## <a name="nv-instances"></a>Istanze NV



Le istanze NV sono basate su GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) e sulla tecnologia NVIDIA GRID per applicazioni con accelerazione grafica per desktop e desktop virtuali con cui i clienti possono visualizzare i propri dati o le proprie simulazioni. Gli utenti possono visualizzare i flussi di lavoro con utilizzo intensivo di grafica nelle istanze NV per ottenere una funzionalità grafica di livello superiore ed eseguire anche singoli carichi di lavoro di precisione, come la codifica e il rendering. 

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Numero massimo di dischi dati |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1.440 | 4 | 64 |

1 GPU = Pari a metà scheda M60.


