
## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- I dischi dati possono operare in modalità memorizzata nella cache o non memorizzata nella cache. Per il funzionamento dei dischi dati memorizzati nella cache, la modalità di cache host è impostata su **ReadOnly** o su **ReadWrite**.  Per il funzionamento dei dischi dati non memorizzati nella cache, la modalità di cache host è impostata su **None**.
-   Se si vogliono ottenere prestazioni ottimali per le macchine virtuali, è necessario limitare il numero di dischi dati a 2 per ogni vCPU.
- Le **prestazioni di rete previste** sono la larghezza di banda aggregata massima allocata per ogni tipo di VM in tutte le schede di interfaccia di rete, per tutte le destinazioni. I limiti massimi non sono garantiti, ma hanno lo scopo di fornire indicazioni per la selezione del tipo di VM corretto per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da svariati fattori, tra cui congestione della rete, carichi dell'applicazione e impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [Ottimizzazione della velocità effettiva di rete per Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Per realizzare le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la VM. Per altre informazioni, vedere [Come eseguire test affidabili della velocità effettiva della macchina virtuale](../articles/virtual-network/virtual-network-bandwidth-testing.md).

- &#8224; Le prestazioni basate su 16 vCPU raggiungeranno stabilmente il limite massimo in una versione futura.


