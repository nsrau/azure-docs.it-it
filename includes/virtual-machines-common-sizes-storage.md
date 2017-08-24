
La serie Ls è ottimizzata per carichi di lavoro che richiedono l'archiviazione temporanea a bassa latenza, come i database NoSQL, inclusi Cassandra, MongoDB, Cloudera e Redis. La serie Ls offre fino a 32 vCPU e usa il [processore Intel® Xeon® della famiglia E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). La serie Ls offre le stesse prestazioni CPU della serie G/GS ed è dotato di 8 GiB di memoria per ogni vCPU.  

## <a name="ls-series"></a>Serie Ls

ACU: 180-240
 
| Dimensione          | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Valore massimo per dischi di dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/prestazioni rete previste (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | ND/ND (0)          | 5.000/125                               | 2 / 4000       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | ND/ND (0)          | 10.000/250                              | 4 / 8000  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | ND/ND (0)          | 20.000/500                              | 8 / 6000 - 16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5,630 | 64             | ND/ND (0)          | 40.000/1.000                            | 8 / 20000 | 
 

La massima velocità effettiva del disco possibile con le VM serie Ls può essere limitata dal numero, dalle dimensioni e dallo striping dei dischi collegati. Per altre informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../articles/storage/storage-premium-storage.md). 

*L'istanza è isolata e prevede hardware dedicato per un singolo cliente.

