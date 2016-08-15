# Panoramica su aree e disponibilità
Azure è compatibile con svariati datacenter in tutto il mondo. Questi datacenter sono raggruppati in aree geografiche, per lasciare l'utente libero di scegliere dove creare le applicazioni. È anche possibile impostare la disponibilità elevata nelle proprie app sfruttando le caratteristiche di ridondanza e disponibilità della piattaforma di Azure, come la replica di archiviazione e i set di disponibilità.

## Che cosa sono le aree di Azure?
Azure consente di creare risorse, ad esempio VM, in determinate aree geografiche, come "Stati Uniti occidentali", "Europa settentrionale" o "Asia sud-orientale". Al momento esistono 26 aree di Azure nel mondo. Per conoscerle, è possibile consultare l'[elenco delle aree e le relative posizioni](https://azure.microsoft.com/regions/). Ogni area ospita più datacenter, in modo da assicurare ridondanza e disponibilità. Ciò garantisce flessibilità quando si sviluppano le applicazioni per creare macchine virtuali (VM) più vicine possibile agli utenti e per finalità legali, di conformità o fiscali.

## Aree speciali di Azure
Quando si configurano le applicazioni, è possibile usare alcune aree speciali di Azure per finalità legali o di conformità.

Le aree speciali esistenti includono:

- **US Gov Virginia** e **US Gov Iowa**
    - Un'istanza logica e fisica di Azure isolata dalla rete per i partner e gli enti pubblici statunitensi, gestita da persone selezionate negli Stati Uniti. Include certificazioni di conformità aggiuntive, come [FedRAMP](https://www.microsoft.com/it-IT/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/it-IT/TrustCenter/Compliance/DISA). Ulteriori informazioni su [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).
- **Australia orientale** e **Australia sud-orientale**
    - Queste aree sono disponibili per i clienti con aziende in Australia o Nuova Zelanda.
- **India centrale**, **India meridionale** e **India occidentale**
    - Al momento sono disponibili queste aree per i clienti e i partner con contratti multilicenza e registrati a livello locale in India; per tutto il 2016, l'accesso sarà aperto a tutte le sottoscrizioni online dirette.
- **Cina orientale** e **Cina settentrionale**
    - Queste aree sono disponibili nel contesto di una partnership esclusiva tra Microsoft e 21Vianet, in virtù della quale i datacenter non sono gestiti direttamente da Microsoft. Altre informazioni sulla [disponibilità di Microsoft Azure in Cina](http://www.windowsazure.cn/).

Altre aree speciali annunciate sono:

- **Germania centrale** e **Germania nord-orientale**
    - Azure sarà disponibile con un nuovo modello di trustee dei dati, in base a cui i dati dei clienti rimangono in Germania sotto il controllo di T-Systems, una società Deutsche Telekom, che funge da trustee dei dati tedesco.

## Coppie di aree
Ogni area di Azure è associata a un'altra area con la stessa collocazione geografica (ad esempio Stati Uniti, Europa o Asia). Questo consente di replicare le risorse, ad esempio la memoria delle VM, in una stessa area geografica in modo da ridurre la probabilità che calamità naturali, agitazioni, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. Tra gli ulteriori vantaggi delle coppie di aree:

- Nel caso di un'interruzione di Azure di vaste proporzioni, viene assegnata la priorità a un'area di ogni coppia, in modo da accelerare il ripristino per le applicazioni.
- Gli aggiornamenti pianificati di Azure vengono implementati nelle coppie di aree uno alla volta, per ridurre al minimo il tempo di inattività e il rischio di interruzione delle applicazioni.
- I dati continueranno a trovarsi all'interno della stessa coppia di aree geografiche (ad eccezione del Brasile meridionale) per finalità fiscali e adempimenti legali.

Esempi di coppie di aree includono:

| Primario | Secondario |
|:---------------|:------------|
| Stati Uniti occidentali | Stati Uniti orientali |
| Europa settentrionale | Europa occidentale |
| Asia sudorientale | Asia orientale |

Consultare [qui l'elenco completo delle coppie di aree](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## Disponibilità delle funzionalità
Alcuni servizi o funzionalità delle VM sono disponibili solo in determinate aree geografiche, ad esempio alcuni tipi di archiviazione o dimensioni delle VM. Per alcuni servizi globali di Azure, inoltre, non è necessario selezionare un'area geografica, come nel caso di [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Gestione traffico](../articles/traffic-manager/traffic-manager-overview.md) o [DNS Azure](../articles/dns/dns-overview.md). Per facilitare la progettazione dell'ambiente applicativo, è possibile controllare la [disponibilità dei servizi di Azure in ogni area geografica](https://azure.microsoft.com/regions/#services).


## Disponibilità dell'archiviazione
Conoscere le aree geografiche di Azure diventa importante quando si considerano le opzioni disponibili per la replica dell'archiviazione di Azure. Quando si crea un account di archiviazione, è necessario selezionare una delle opzioni di replica seguenti:

- Archiviazione con ridondanza locale (LRS)
    - I dati vengono replicati tre volte all'interno dell'area in cui è stato creato l'account di archiviazione.
- Archiviazione con ridondanza della zona (ZRS)
    - I dati vengono replicati tre volte in due o tre strutture distribuite in un'area sola o in due aree.
- Archiviazione con ridondanza geografica (GRS)
    - I dati vengono replicati in un'area secondaria a centinaia di chilometri di distanza dall'area primaria.
- Archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
    - I dati vengono replicati in un'area secondaria, come con la ridondanza geografica, ma risultano anche accessibili in sola lettura nell'area secondaria.

La tabella seguente fornisce una rapida panoramica delle differenze tra i tipi di replica di archiviazione:

| Strategia di replica | Archiviazione con ridondanza locale | ZRS | Archiviazione con ridondanza geografica | RA-GRS |
|:-----------------------------------------------------------------------------------|:----|:----|:----|:-------|
| I dati vengono replicati in più strutture | No | Sì | Sì | Sì |
| I dati possono essere letti dalla posizione secondaria oltre che da quella primaria | No | No | No | Sì |
| Numero di copie di dati mantenute in nodi distinti | 3 | 3 | 6 | 6 |

Per ulteriori informazioni, consultare [qui le opzioni di replica di Archiviazione di Azure](../articles/storage/storage-redundancy.md).

### Costi di archiviazione
I prezzi variano a seconda del tipo di archiviazione e della disponibilità selezionata.

- L'archiviazione standard è supportata dai normali dischi a rotazione e addebitata in base alla capacità in uso e alla disponibilità di archiviazione desiderata.
    - Per l'archiviazione RA-GRS, il trasferimento dati con replica geografica prevede l'ulteriore addebito del costo della larghezza di banda per la replica dei dati in un'altra area di Azure.
- L'archiviazione Premium è supportata da unità a stato solido (SSD); il costo dipende dalla capacità del disco.

Per informazioni sulle opzioni di disponibilità e sui prezzi dei vari tipi di archiviazione, vedere i [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).


## Immagini di Azure
In Azure vengono create VM da un'immagine. In genere tale immagine verrà creata da [Azure Marketplace](https://azure.microsoft.com/marketplace/); al suo interno i partner possono fornire il sistema operativo interamente preconfigurato oppure le immagini delle applicazioni.

Quando si crea una VM da un'immagine ricavata da Azure Marketplace, di fatto si utilizzano modelli. I modelli di Azure Resource Manager sono file JavaScript Object Notation (JSON) dichiarativi utilizzabili per creare ambienti applicativi complessi che comprendono VM, archiviazione, rete virtuale e così via. Sono disponibili ulteriori informazioni sul come utilizzare i [modelli di Azure Resource Manager](../articles/resource-group-overview.md) e su come [creare modelli personalizzati](../articles/resource-group-authoring-templates.md).

È inoltre possibile creare immagini personalizzate e caricarle tramite l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) per creare rapidamente VM personalizzate in base a specifici requisiti di compilazione. Quando si utilizzano immagini personalizzate, le VM devono essere archiviate nello stesso account di archiviazione dell'immagine stessa. Non è possibile caricare un'immagine in una singola area e quindi creare le VM in altre aree di Azure.


## Set di disponibilità
Un set di disponibilità è un raggruppamento logico di VM che consente ad Azure di risalire alla modalità di compilazione dell'applicazione per fornire ridondanza e disponibilità. In un set di disponibilità è consigliabile creare due o più VM, in modo da garantire un'applicazione altamente disponibile e raggiungere il [99,95% di disponibilità previsto dal contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Il set di disponibilità comprende due ulteriori raggruppamenti che proteggono dagli errori hardware (domini di errore) e consentono di applicare gli aggiornamenti in modo sicuro (domini di aggiornamento).

![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Sono disponibili ulteriori informazioni su come gestire la disponibilità delle [VM Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) o delle [VM Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### Domini di errore
Un dominio di errore è un raggruppamento logico di tutto l'hardware sottostante che condivide una fonte di alimentazione e uno switch di rete comuni, come a un rack in un datacenter locale. Quando si creano VM all'interno di un set di disponibilità, la piattaforma Azure le distribuisce automaticamente in questi domini di errore per limitare le conseguenze di potenziali errori dell'hardware fisico e interruzioni di rete o di alimentazione.

### Domini di aggiornamento
Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione oppure riavviato nello stesso momento. Quando si creano VM all'interno di un set di disponibilità, la piattaforma Azure le distribuisce automaticamente in questi domini di aggiornamento per garantire che almeno una delle istanze dell'applicazione rimanga sempre in esecuzione nei momenti di manutenzione periodica della piattaforma Azure. Tenere presente che, durante la manutenzione pianificata, i domini di aggiornamento non vengono necessariamente riavviati in sequenza, ma ne viene riavviato uno solo alla volta.


## Passaggi successivi
Sono disponibili informazioni specifiche sulle [best practice per la disponibilità di Azure](../articles/best-practices-availability-checklist.md).

<!---HONumber=AcomDC_0803_2016-->