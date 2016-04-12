

In questo articolo vengono presentate informazioni generali e considerazioni sull'uso delle istanze A8, A9, A10 e A11 di Azure, note anche come istanze *a elevato utilizzo di calcolo*. Le funzionalità principali di queste istanze includono:

* **Hardware ad alte prestazioni**: l'hardware del data center di Azure che esegue queste istanze è progettato e ottimizzato per applicazioni a elevato utilizzo di calcolo e di rete, incluse applicazioni cluster HPC (High-Performance Computing), modellazione e simulazioni.

* **Connessione di rete RDMA per applicazioni MPI**: impostare le istanze A8 e A9 per la comunicazione con altre istanze A8 e A9 in Azure tramite una rete a bassa latenza e velocità effettiva elevata, basata sulla tecnologia di accesso diretto a memoria remota (RDMA). Questa funzionalità consente di potenziare le prestazioni di determinate applicazioni MPI (Message Passing Interface) Linux e Windows.

* **Supporto per cluster HPC** : distribuire il software di gestione dei cluster e di pianificazione dei processi nelle istanze A8, A9, A10 e A11 in Azure per creare un cluster HPC autonomo o per aggiungere capacità a un cluster locale.

>[AZURE.NOTE]Le istanze A10 e A11 presentano le stesse ottimizzazioni delle prestazioni e le stesse specifiche delle istanze A8 e A9. Non includono però l'accesso alla rete RDMA in Azure. Sono progettate per applicazioni di elaborazione ad alte prestazioni che non richiedono la comunicazione costante e a bassa latenza tra nodi, note anche come applicazioni parametriche o parallele.


## Specifiche

### CPU e memoria

Le istanze a elevato utilizzo di calcolo A8, A9, A10 e A11 di Azure presentano CPU multicore ad alta velocità ed elevati volumi di memoria, come illustrato nella tabella seguente.

Dimensione | CPU | Memoria
------------- | ----------- | ----------------
A8 e A10 | Intel Xeon E5-2670<br/>8 core da 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 e A11 | Intel Xeon E5-2670<br/>16 core da 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Per altre informazioni sui processori, incluse estensioni supportate del set di istruzioni, vedere il sito Web Intel.com. Per informazioni dettagliate sulle capacità di archiviazione e i dischi delle VM, vedere [Dimensioni delle macchine virtuali](../articles/virtual-machines/virtual-machines-linux-sizes.md).

### Schede di rete

Le istanze A8 e A9 dispongono di due schede di rete che si connettono alle due reti back-end di Azure seguenti.


Rete | Descrizione
-------- | -----------
Ethernet a 10 Gbps | Si connette ai servizi di Azure, come il servizio di archiviazione e la rete virtuale di Azure, e a Internet.
Rete back-end a 32 Gbps che supporta RDMA | Permette le comunicazioni di applicazioni a bassa latenza e velocità effettiva elevata tra le istanze presenti all'interno di un singolo servizio cloud o set di disponibilità. Riservata per il solo traffico MPI.


>[AZURE.IMPORTANT]Vedere [Accesso alla rete RDMA](#access-the-rdma-network) in questo articolo per requisiti aggiuntivi per applicazioni MPI per l'accesso alla rete RDMA.

Le istanze A10 e A11 dispongono di una singola scheda di rete Ethernet a 10 Gbps che si connette ai servizi di Azure e a Internet.

## Considerazioni sulla distribuzione

* **Account Azure**: se si intende distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento a consumo o altre opzioni di acquisto. È anche possibile usare la sottoscrizione MSDN. Vedere [Benefici di Azure per sottoscrittori MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Con un [account gratuito di Azure](https://azure.microsoft.com/pricing/free-trial/) è possibile utilizzare solo un numero limitato di core di calcolo di Azure.

* **Quota di core**: può essere necessario aumentare la quota di core nella sottoscrizione di Azure dal valore predefinito di 20 core per sottoscrizione (se si usa il modello di distribuzione classico) o 20 core per area (se si usa il modello di distribuzione di Gestione risorse di Azure). Per richiedere un aumento della quota, aprire un ticket di supporto gratuito, come descritto in [Informazioni sui limiti di Azure e su come aumentarli](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Le quote di Azure sono limiti di credito e non garanzie di capacità. Viene addebitato solo l'uso dei core effettivamente impiegati.

* **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molti scenari IaaS potrebbe essere tuttavia necessaria almeno una rete virtuale di Azure basata su cloud. Per l'accesso alle risorse locali, potrebbe essere necessaria anche una connessione da sito a sito, ad esempio un server licenze per le applicazioni. È necessario creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una macchina virtuale A8, A9, A10 o A11 a una rete virtuale in un gruppo di affinità non è supportata.

* **Servizio cloud o set di disponibilità**: per connettersi tramite la rete RDMA, le istanze A8 e A9 devono essere distribuite nello stesso servizio cloud (se si usa il modello di distribuzione classico) o nello stesso set di disponibilità (se si usa il modello di distribuzione di Gestione risorse di Azure).

* **Prezzi**: le dimensioni delle VM A8 - A11 sono disponibili solo per il piano tariffario Standard.

* **Ridimensionamento**: non è possibile ridimensionare un'istanza di dimensioni diverse da A8 - A11 in uno dei formati di istanza a elevato utilizzo di calcolo (A8 - 11) e non è possibile ridimensionare un'istanza a elevato utilizzo di calcolo in una dimensione a utilizzo di calcolo non elevato. Questo per via dell'hardware specializzato e delle ottimizzazioni delle prestazioni, che sono specifici delle istanze a elevato utilizzo di calcolo.

* **Spazio degli indirizzi della rete RDMA**: la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/12. Se si prevede di eseguire applicazioni MPI in istanze A8 e A9 in una rete virtuale di Azure, verificare che lo spazio degli indirizzi della rete virtuale non si sovrapponga alla rete RDMA.

<!---HONumber=AcomDC_0330_2016-->