
Le dimensioni delle serie A8-A11 e H sono note anche come *istanze a elevato uso di calcolo*. L'hardware che esegue queste dimensioni è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, come applicazioni cluster HPC, modellazione e simulazioni. La serie A8-A11 usa Intel Xeon E5-2670 a 2,6 GHZ, mentre la serie H usa Intel Xeon E5-2667 v3 a 3,2 GHz.  Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

Le macchine virtuali serie H di Azure sono le VM high performance computing di prossima generazione che puntano a risolvere esigenze di calcolo di fascia alta, come modellazione molecolare e fluidodinamica computazionale. Questi 8 e 16 CPU virtuali VM sono compilate su Intel-2667 Haswalle E5 V3 processore tecnologia con DDR4 memoria e archiviazione temporanea basate su SSD. 

Oltre alla sostanziale potenza della CPU, la serie H offre diverse opzioni per rete RDMA a bassa latenza con FDR InfiniBand e diverse configurazioni di memoria a supporto di requisiti di calcolo a elevato uso di memoria.



## <a name="h-series"></a>Serie H

ACU: 290-300

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva del disco max: IOPS | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32x500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32x500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> per applicazioni MPI, rete di back-end dedicato RDMA è abilitato per la rete InfiniBand FDR, che recapita minime latenza e ad alta larghezza di banda.

<br>



## <a name="a-series---compute-intensive-instances"></a>Serie A - Istanze a elevato utilizzo di calcolo

ACU: 225

| Dimensione | vCPU | Memoria: GiB | Spazio di archiviazione temp (HDD): GiB | Valore massimo per dischi di dati | Velocità effettiva del disco di dati max: IOPS | Schede di interfaccia di rete max|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>per applicazioni MPI, rete di back-end dedicato RDMA è abilitato per la rete InfiniBand FDR, che recapita minime latenza e ad alta larghezza di banda.

<br>



