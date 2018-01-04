

Alcuni carichi di lavoro di database come SQL Server o Oracle richiedono elevato della memoria, archiviazione e la larghezza di banda dei / o, ma non un numero elevato di core. Molti carichi di lavoro di database non sono a elevato utilizzo di CPU. Azure offre alcune dimensioni delle macchine Virtuali in cui è possibile limitare il numero di CPU virtuali VM a ridurre i costi di gestione licenze software, mantenendo la stessa memoria, archiviazione e della larghezza di banda dei / o.

Il numero di CPU virtuali può essere vincolato a metà o un trimestre della dimensione di macchina virtuale originale. Queste nuove dimensioni delle macchine Virtuali hanno un suffisso che specifica il numero di Vcpu active per semplificarne l'identificazione.

Ad esempio, le dimensioni della VM corrente Standard_GS5 viene fornito con 32 Vcpu, 448 GB di RAM, 64 dischi (fino a 256 TB) e 80.000 IOPs o 2 GB/s di larghezza di banda dei / o. La nuova macchina virtuale di dimensioni Standard_GS5-16 e Standard_GS5-8 con 8 e 16 Vcpu active, rispettivamente, mantenendo il resto delle specifiche del Standard_GS5 per la memoria, archiviazione e della larghezza di banda dei / o.

Le commissioni licenze per SQL Server o Oracle sono vincolati al nuovo numero di CPU virtuali e altri prodotti verranno addebitate le spese in base al nuovo numero di CPU virtuali. Questo comporta un aumento di 50-75% il rapporto tra le specifiche VM per Vcpu (fatturabile) attivo. Queste nuove dimensioni delle macchine Virtuali che sono disponibili solo in Azure, che consente di effettuare il push di utilizzo della CPU superiore a una frazione di gestione delle licenze (per core) costo carichi di lavoro. In questo momento, il costo di calcolo, che include Gestione licenze del sistema operativo, rimane identico a quello di come le dimensioni originali. Per ulteriori informazioni, vedere [dimensioni delle macchine Virtuali di Azure per carichi di lavoro più economica database](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| NOME                | vCPU | Specifiche           |
|---------------------|------|-----------------|
| Standard_M64 32ms   | 32   | Identico M64ms   |
| Standard_M64-16 ms   | 16   | Identico M64ms   |
| Standard_M128 64ms  | 64   | Identico M128ms  |
| Standard_M128 32ms  | 32   | Identico M128ms  |
| Standard_E32 16_v3  | 16   | Identico E32s_v3 |
| Standard_E32 8s_v3  | 8    | Identico E32s_v3 |
| Standard_E64 32s_v3 | 32   | Identico E64s_v3 |
| Standard_E64 16s_v3 | 16   | Identico E64s_v3 |
| Standard_GS4-8      | 8    | Identico GS4     |
| Standard_GS4-4      | 4    | Identico GS4     |
| Standard_GS5-16     | 16   | Identico GS5     |
| Standard_GS5-8      | 8    | Identico GS5     |
| Standard_DS13 4_v2  | 4    | Identico DS13_v2 |
| Standard_DS13 2_v2  | 2    | Identico DS13_v2 |
| Standard_DS14 8_v2  | 8    | Identico DS14_v2 |
| Standard_DS14 4_v2  | 4    | Identico DS14_v2 |