Le dimensioni delle macchine virtuali ottimizzate per l'archiviazione offrono una velocità effettiva e di I/O elevata per i dischi e sono ideali per i database NoSQL, SQL e Big Data. Questo articolo offre informazioni sul numero di vCPU, dischi dati e schede di rete, oltre che sulla velocità effettiva di archiviazione e sulla larghezza di banda della rete per ogni dimensione di questo raggruppamento. 

La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Dimensione          | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea: IOPS/Mbps | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4    | 32   | 678   | 16    | 20,000 / 200   | 10.000/250        | 2 / 4,000  | 
| Standard_L8s   | 8    | 64   | 1,388 | 32   | 40,000 / 400   | 20.000/500       | 4 / 8,000  | 
| Standard_L16s  | 16   | 128  | 2,807 | 64   | 80.000 / 800   | 40.000/1.000       | 8 / 6,000 - 16,000 &#8224; | 
| Standard_L32s* | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 80.000/2.000     | 8 / 20,000 | 
 

La massima velocità effettiva del disco possibile con le VM serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/virtual-machines/windows/premium-storage.md). 

*L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

