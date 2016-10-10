
## Funzionalità principali

* **Hardware ad alte prestazioni**: queste istanze sono progettate e ottimizzate per applicazioni a elevato uso di calcolo e di rete, incluse le applicazioni HPC (High-Performance Computing) e batch, per la modellazione e per simulazioni su vasta scala.

    Per conoscere le specifiche di base, le funzionalità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md). I dettagli relativi ai processori Intel Xeon E5-2667 v3 (usato nella serie H) e Intel Xeon E5-2670 (usato in A8 - A11), tra cui le estensioni del set di istruzioni supportate, sono disponibili sul sito Web Intel.com.

* **Progettato per i cluster HPC**: distribuire più istanze a elevato uso di calcolo in Azure per creare un cluster HPC autonomo o per aggiungere capacità a un cluster locale. Se si desidera, distribuire strumenti di gestione del cluster e pianificazione dei processi. In alternativa, usare le istanze per i processi a elevato uso di calcolo in un altro servizio di Azure come Azure Batch.

* **Connessione alla rete RDMA per applicazioni MPI**: un subset delle istanze a elevato uso di calcolo (H16r, H16mr, A8 e A9) è dotato di una seconda interfaccia di rete per la connettività ad accesso diretto a memoria remota (RDMA). Questa interfaccia è un'aggiunta all'interfaccia di rete di Azure standard disponibile per gli altri formati di VM.

    Questa interfaccia consente alle istanze con supporto di RDMA di comunicare tra loro attraverso una rete InfiniBand che opera a velocità FDR per macchine virtuali H16r e H16mr e a velocità QDR per macchine virtuali A8 e A9. Le funzionalità RDMA esposte in queste macchine virtuali possono aumentare la scalabilità e le prestazioni di alcune applicazioni Linux e Windows Message Passing Interface (MPI). Per conoscere i requisiti, vedere la sezione [Accesso alla rete RDMA](#access-to-the-rdma-network) in questo articolo.



## Considerazioni sulla distribuzione

* **Sottoscrizione di Azure**: se si prevede di distribuire numerose istanze a elevato utilizzo di calcolo, prendere in considerazione una sottoscrizione con pagamento in base al consumo o altre opzioni di acquisto. Con un [account gratuito di Azure](https://azure.microsoft.com/free/) è possibile usare solo un numero limitato di core di calcolo di Azure.

* **Prezzi e disponibilità**: le dimensioni di VM a elevato uso di calcolo sono offerte solo con il piano tariffario Standard. Per informazioni sulla disponibilità nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

* **Quota di core**: può essere necessario aumentare la quota di core nella sottoscrizione di Azure dal valore predefinito di 20 core per sottoscrizione,se si usa il modello di distribuzione classica, o 20 core per area, se si usa il modello di distribuzione Resource Manager. La sottoscrizione può anche limitare il numero di core che è possibile distribuire in alcune famiglie di dimensioni di VM, inclusa la serie H. Per richiedere un aumento della quota, è possibile [aprire una richiesta di assistenza clienti online](../articles/azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. I limiti predefiniti possono variare in base alla categoria della sottoscrizione.

    >[AZURE.NOTE]Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure. Le quote di Azure sono limiti di credito e non garanzie di capacità. A prescindere dalla quota, viene addebitato solo l'uso dei core effettivamente impiegati.

* **Rete virtuale**: non è necessaria una [rete virtuale](https://azure.microsoft.com/documentation/services/virtual-network/) di Azure per usare le istanze a elevato utilizzo di calcolo. Per molti scenari di distribuzione potrebbe essere tuttavia necessaria almeno una rete virtuale di Azure basata sul cloud. Per l'accesso alle risorse locali, potrebbe essere necessaria anche una connessione da sito a sito, ad esempio un server licenze per le applicazioni. Se è necessaria, creare una nuova rete virtuale per distribuire le istanze. L'aggiunta di una VM a elevato uso di calcolo a una rete virtuale in un gruppo di affinità non è supportata.

* **Servizio cloud o set di disponibilità**: per usare la rete RDMA di Azure, distribuire le VM con supporto di RDMA nello stesso servizio cloud, se si usa il modello di distribuzione classica, o nello stesso set di disponibilità, se si usa il modello di distribuzione Azure Resource Manager. Se si usa Azure Batch, le VM con supporto di RDMA devono trovarsi nello stesso pool.

* **Ridimensionamento**: a causa dell'hardware specializzato usato nelle istanze a elevato uso di calcolo, è possibile ridimensionare solo le istanze a elevato uso di calcolo all'interno della stessa famiglia di dimensioni (serie H o serie A a elevato uso di calcolo). Ad esempio, è possibile ridimensionare una VM della serie H solo da una dimensione della serie H a un'altra. Inoltre, non è supportato il ridimensionamento da una dimensione non a elevato uso di calcolo.

* **Spazio degli indirizzi della rete RDMA**: la rete RDMA in Azure riserva lo spazio degli indirizzi 172.16.0.0/16. Per eseguire applicazioni MPI in istanze distribuite in una rete virtuale di Azure, assicurarsi che lo spazio degli indirizzi di rete virtuale non si sovrapponga alla rete RDMA.

<!---HONumber=AcomDC_0928_2016-->