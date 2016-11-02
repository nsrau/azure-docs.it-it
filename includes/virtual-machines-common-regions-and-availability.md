# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Aree e disponibilità per le macchine virtuali in Azure
È importante comprendere dove e come funzionano le macchine virtuali (VM) in Azure, così come le possibilità di ottimizzare le prestazioni, la disponibilità e la ridondanza. Azure è compatibile con svariati datacenter in tutto il mondo. Questi datacenter sono raggruppati in aree geografiche, per lasciare l'utente libero di scegliere dove creare le applicazioni. Questo articolo offre una panoramica delle funzionalità di disponibilità e ridondanza di Azure.

## <a name="what-are-azure-regions?"></a>Che cosa sono le aree di Azure?
Azure consente di creare risorse, ad esempio VM, in determinate aree geografiche, come "Stati Uniti occidentali", "Europa settentrionale" o "Asia sud-orientale". Al momento esistono 30 aree di Azure nel mondo. Per conoscerle, è possibile consultare l' [elenco delle aree e le relative posizioni](https://azure.microsoft.com/regions/). Ogni area ospita più data center per assicurare la ridondanza e la disponibilità. Questo approccio garantisce flessibilità nella compilazione di applicazioni e permette di creare macchine virtuali più vicine agli utenti che rispondano a requisiti legali, di conformità o fiscali.

## <a name="special-azure-regions"></a>Aree speciali di Azure
Durante la compilazione di applicazioni è possibile usare alcune aree speciali di Azure per finalità legali o di conformità. Le aree speciali includono:

- **US Gov Virginia** e **US Gov Iowa**
    - Un'istanza logica e fisica di Azure isolata dalla rete per i partner e gli enti pubblici statunitensi, gestita da persone selezionate negli Stati Uniti. Include certificazioni di conformità aggiuntive, come [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Ulteriori informazioni su [Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/).
- **India centrale**, **India meridionale** e **India occidentale**
    - Queste aree sono attualmente disponibili per clienti e partner titolari di contratti multilicenza con iscrizione locale in India. L'accesso sarà aperto alle sottoscrizioni online dirette per tutto il 2016.
- **Cina orientale** e **Cina settentrionale**
    - Queste aree sono disponibili nel contesto di una partnership esclusiva tra Microsoft e 21Vianet, in virtù della quale i datacenter non sono gestiti direttamente da Microsoft. Altre informazioni sulla [disponibilità di Microsoft Azure in Cina](http://www.windowsazure.cn/).
- **Germania centrale** e **Germania nord-orientale**
    - Queste regioni sono attualmente disponibili tramite un modello di trustee dei dati. In base a tale modello i dati dei clienti rimangono in Germania sotto il controllo di T-Systems, una società Deutsche Telekom che funge da trustee dei dati tedesco.

## <a name="region-pairs"></a>Coppie di aree
Ogni area di Azure è associata a un'altra area con la stessa collocazione geografica (ad esempio Stati Uniti, Europa o Asia). Questo approccio consente la replica delle risorse, come lo spazio di archiviazione delle macchine virtuali, in una stessa area geografica in modo da ridurre la probabilità che calamità naturali, agitazioni, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. Tra gli ulteriori vantaggi delle coppie di aree:

- Nel caso di un'interruzione di Azure di vaste proporzioni, viene assegnata la priorità a un'area di ogni coppia, in modo da accelerare il ripristino per le applicazioni. 
- Gli aggiornamenti pianificati di Azure vengono implementati nelle coppie di aree uno alla volta, per ridurre al minimo il tempo di inattività e il rischio di interruzione delle applicazioni.
- Le coppie di dati continuano a trovarsi all'interno della stessa area geografica, ad eccezione del Brasile meridionale, per finalità fiscali e adempimenti legali.

Esempi di coppie di aree includono:

| Primario        | Secondario   |
|:---------------|:------------|
| Stati Uniti occidentali        | Stati Uniti orientali     |
| Europa settentrionale   | Europa occidentale |
| Asia sudorientale | Asia orientale   |

Consultare [qui l'elenco completo delle coppie di aree](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilità delle funzionalità
Alcuni servizi o funzionalità delle VM sono disponibili solo in determinate aree geografiche, ad esempio alcuni tipi di archiviazione o dimensioni delle VM. Per alcuni servizi globali di Azure non è necessario selezionare un'area geografica specifica, come nel caso di [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Gestione traffico](../articles/traffic-manager/traffic-manager-overview.md) o [DNS Azure](../articles/dns/dns-overview.md). Per facilitare la progettazione dell'ambiente applicativo, è possibile controllare la [disponibilità dei servizi di Azure in ogni area geografica](https://azure.microsoft.com/regions/#services). 


## <a name="storage-availability"></a>Disponibilità dell'archiviazione
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

| Strategia di replica                                                        | Archiviazione con ridondanza locale | ZRS | Archiviazione con ridondanza geografica | RA-GRS |
|:----------------------------------------------------------------------------|:----|:----|:----|:-------|
| I dati vengono replicati in più strutture                              | No  | Sì | Sì | Sì    |
| I dati possono essere letti dalla località secondaria e da quella primaria. | No  | No  | No  | Sì    |
| Numero di copie di dati mantenute in nodi distinti                      | 3   | 3   | 6   | 6      |

Per ulteriori informazioni, consultare [qui le opzioni di replica di Archiviazione di Azure](../articles/storage/storage-redundancy.md).

### <a name="storage-costs"></a>Costi di archiviazione
I prezzi variano a seconda del tipo di archiviazione e della disponibilità selezionata. 

- L'archiviazione Premium è supportata da unità a stato solido (SSD); il costo dipende dalla capacità del disco.
- L'archiviazione standard è supportata dai normali dischi a rotazione e addebitata in base alla capacità in uso e alla disponibilità di archiviazione desiderata.
    - Per l'archiviazione RA-GRS, il trasferimento dati con replica geografica prevede l'ulteriore addebito del costo della larghezza di banda per la replica dei dati in un'altra area di Azure.

Per informazioni sulle opzioni di disponibilità e sui prezzi dei vari tipi di archiviazione, vedere i [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .


## <a name="azure-images"></a>Immagini di Azure
In Azure vengono create VM da un'immagine. In genere le immagini provengono da [Azure Marketplace](https://azure.microsoft.com/marketplace/)i partner possono inserire immagini di applicazioni o sistemi operativi completi preconfigurati.

Quando si crea una VM da un'immagine ricavata da Azure Marketplace, di fatto si utilizzano modelli. I modelli di Azure Resource Manager sono file JavaScript Object Notation (JSON) dichiarativi utilizzabili per creare ambienti applicativi complessi che comprendono VM, archiviazione, rete virtuale e così via. Sono disponibili altre informazioni sull'uso dei [modelli di Azure Resource Manager](../articles/resource-group-overview.md) e su come [compilare modelli personalizzati](../articles/resource-group-authoring-templates.md).

È anche possibile creare immagini personalizzate e caricarle usando l'[interfaccia della riga di comando di Azure](../articles/virtual-machines/virtual-machines-linux-upload-vhd.md) o [Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-upload-image.md) per creare rapidamente macchine virtuali personalizzate in base a requisiti di compilazione specifici.

## <a name="availability-sets"></a>Set di disponibilità
Un set di disponibilità è un raggruppamento logico di macchine virtuali che permette ad Azure di comprendere come è compilata l'applicazione per garantirne la ridondanza e la disponibilità. È consigliabile creare due o più macchine virtuali in un set di disponibilità, per garantire un'elevata disponibilità dell'applicazione e raggiungere il [99,95% di disponibilità previsto dal contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Il set di disponibilità comprende due ulteriori raggruppamenti che proteggono dagli errori hardware (domini di errore) e consentono di applicare gli aggiornamenti in modo sicuro (domini di aggiornamento).

![Rappresentazione concettuale della configurazione di domini di aggiornamento e di errore](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Sono disponibili altre informazioni su come gestire la disponibilità delle macchine virtuali [Linux](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) o [Windows](../articles/virtual-machines/virtual-machines-linux-manage-availability.md).

### <a name="fault-domains"></a>Domini di errore
Un dominio di errore è un raggruppamento logico di tutto l'hardware sottostante che condivide una fonte di alimentazione e uno switch di rete comuni, come a un rack in un datacenter locale. Man mano che si creano le macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure le distribuisce automaticamente in questi domini di errore. Questo approccio consente di limitare l'impatto di eventuali guasti dell'hardware fisico, interruzioni di rete o interruzioni dell'alimentazione.

### <a name="update-domains"></a>Domini di aggiornamento
Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione oppure riavviato nello stesso momento. Man mano che si creano le macchine virtuali all'interno di un set di disponibilità, la piattaforma Azure le distribuisce automaticamente in questi domini di aggiornamento. Questo approccio garantisce che almeno un'istanza dell'applicazione rimanga in esecuzione durante gli interventi di manutenzione periodica della piattaforma Azure. I domini di aggiornamento non vengono necessariamente riavviati in ordine sequenziale durante la manutenzione pianificata, ma ne viene riavviato uno solo alla volta.


## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a usare le funzionalità di ridondanza e disponibilità per compilare l'ambiente Azure. Per altre informazioni, vedere le [procedure consigliate per la disponibilità di Azure](../articles/best-practices-availability-checklist.md).

<!--HONumber=Oct16_HO2-->


