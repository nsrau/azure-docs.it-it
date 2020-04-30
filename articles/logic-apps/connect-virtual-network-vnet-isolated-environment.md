---
title: Connettersi alle reti virtuali di Azure con ISE
description: Creare un ambiente del servizio di integrazione (ISE) in grado di accedere alle reti virtuali di Azure (reti virtuali) da app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fa63380a8e27dcc8f4de414c483f8d8ed2323e7b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234114"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [ *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE è un ambiente isolato che usa risorse di archiviazione dedicate e altre risorse che vengono mantenute separate dal servizio "globale" di app per la logica multi-tenant. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. Un ISE fornisce anche indirizzi IP statici. Questi indirizzi IP sono distinti dagli indirizzi IP statici condivisi dalle app per la logica nel servizio pubblico multi-tenant.

Quando si crea un ISE, Azure *inserisce* tale ISE nella rete virtuale di Azure, che quindi distribuisce il servizio app per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare ISE come percorso. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Per interagire con le app per la logica e gli account di integrazione in un ISE, è necessario che entrambi usino lo *stesso ISE* della relativa posizione.

ISE ha aumentato i limiti di durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richiesta e risposta HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [limiti e configurazione per app per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md). Per altre informazioni su ISEs, vedere [accesso alle risorse di rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Questo articolo illustra come completare queste attività usando il portale di Azure:

* Abilitare l'accesso per ISE.
* Creazione di ISE.
* Aggiungere capacità aggiuntiva a ISE.

È anche possibile creare un ISE usando il [modello di avvio rapido di esempio Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) o usando l'API REST di app per la logica, inclusa la configurazione delle chiavi gestite dal cliente:

* [Creare un ambiente del servizio di integrazione (ISE) usando l'API REST di app per la logica](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurare chiavi gestite dal cliente per la crittografia dei dati inattivi per ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Le app per la logica, i trigger incorporati, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica.

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md).

  * La rete virtuale deve avere quattro subnet *vuote* per la creazione e la distribuzione di risorse in ISE. Ogni subnet supporta un componente di app per la logica diverso usato in ISE. È possibile creare queste subnet in anticipo, oppure è possibile attendere fino a quando non si crea ISE in cui è possibile creare le subnet nello stesso momento. Altre informazioni sui [requisiti](#create-subnet)per le subnet.

  * I nomi delle subnet devono iniziare con un carattere alfabetico o un carattere di sottolineatura e non possono `<`usare `>`i `%`caratteri `&`seguenti `\\`: `?`, `/`,,,,,. 
  
  * Se si vuole distribuire ISE tramite un modello di Azure Resource Manager, assicurarsi prima di tutto di delegare una subnet vuota a Microsoft. Logic/integrationServiceEnvironment. Non è necessario eseguire questa delega quando si esegue la distribuzione tramite il portale di Azure.

  * Assicurarsi che la rete virtuale [consenta l'accesso per ISE, in](#enable-access) modo che ISE possa funzionare correttamente e rimanere accessibile.

  * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), che fornisce una connessione privata ai servizi cloud Microsoft facilitati dal provider di connettività, è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route seguente e collegare tale tabella a ogni subnet usata da ISE:

    **Nome**: <*Route-Name*><br>
    **Prefisso indirizzo**: 0.0.0.0/0<br>
    **Hop successivo**: Internet

* Se si vuole usare server DNS personalizzati per la rete virtuale di Azure, [configurare questi server seguendo questa procedura](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire ISE nella rete virtuale. Per ulteriori informazioni sulla gestione delle impostazioni del server DNS, vedere [creare, modificare o eliminare una rete virtuale](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Se si modificano le impostazioni del server DNS o del server DNS, è necessario riavviare ISE in modo che ISE possa rilevare tali modifiche. Per ulteriori informazioni, vedere [la](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)pagina relativa al riavvio di ISE.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Abilitare l'accesso per l'ISE

Quando si usa ISE con una rete virtuale di Azure, un problema di installazione comune è la presenza di una o più porte bloccate. I connettori usati per la creazione di connessioni tra i sistemi ISE e di destinazione potrebbero avere anche requisiti di porta specifici. Se, ad esempio, si comunica con un sistema FTP utilizzando il connettore FTP, è necessario che la porta utilizzata sul sistema FTP sia disponibile, ad esempio la porta 21 per l'invio di comandi.

Per assicurarsi che ISE sia accessibile e che le app per la logica di ISE siano in grado di comunicare in ogni subnet della rete virtuale, [aprire le porte descritte in questa tabella per ogni subnet](#network-ports-for-ise). Se le porte necessarie non sono disponibili, ISE non funzionerà correttamente.

* Se sono presenti più istanze di ISE che richiedono l'accesso ad altri endpoint con restrizioni IP, distribuire un [firewall di Azure](../firewall/overview.md) o un' [appliance di rete virtuale](../virtual-network/virtual-networks-overview.md#filter-network-traffic) nella rete virtuale e indirizzare il traffico in uscita attraverso il firewall o l'appliance virtuale di rete. È quindi possibile [configurare un unico indirizzo IP in uscita, pubblico, statico e prevedibile](connect-virtual-network-vnet-set-up-single-ip-address.md) che tutte le istanze di ISE nella rete virtuale possono usare per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall in questi sistemi di destinazione per ogni ISE.

   > [!NOTE]
   > È possibile usare questo approccio per un singolo ISE quando lo scenario richiede la limitazione del numero di indirizzi IP che richiedono l'accesso. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale hanno senso per lo scenario. Scopri di più sui [prezzi di Azure firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se è stata creata una nuova rete virtuale di Azure e le subnet senza vincoli, non è necessario configurare i [gruppi di sicurezza di rete (gruppi)](../virtual-network/security-overview.md#network-security-groups) nella rete virtuale per controllare il traffico tra le subnet.

* In una rete virtuale esistente è possibile impostare *facoltativamente* gruppi [filtrando il traffico di rete tra le subnet](../virtual-network/tutorial-filter-network-traffic.md). Se si vuole passare a questa route o se si sta già usando gruppi, assicurarsi di [aprire le porte in questa tabella](#network-ports-for-ise) nella rete virtuale in cui si è gruppi o si vuole configurare gruppi.

  > [!NOTE]
  > Se si usano le [regole di sicurezza di NSG](../virtual-network/security-overview.md#security-rules), è necessario usare i protocolli TCP e UDP. *both* Le regole di sicurezza di NSG descrivono le porte che è necessario aprire per gli indirizzi IP che necessitano dell'accesso a tali porte. Assicurarsi che eventuali firewall, router o altri elementi presenti tra questi endpoint mantengano tali porte accessibili anche a tali indirizzi IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porte di rete usate da ISE

Questa tabella descrive le porte nella rete virtuale di Azure che ISE USA e in cui vengono usate le porte. Per ridurre la complessità quando si creano le regole di sicurezza, i [tag del servizio](../virtual-network/service-tags-overview.md) nella tabella rappresentano i gruppi di prefissi di indirizzi IP per un servizio di Azure specifico.

> [!IMPORTANT]
> Le porte di origine sono effimere, quindi assicurarsi di impostarle su `*` per tutte le regole. Dove indicato, ISE interno ed ISE esterno fanno riferimento all' [endpoint selezionato al momento della creazione di ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Per ulteriori informazioni, vedere [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Scopo | Direction | Porte di destinazione | Tag del servizio di origine | Tag del servizio di destinazione | Note |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicazione tra subnet nella rete virtuale | In ingresso e in uscita | * | Lo spazio degli indirizzi per la rete virtuale con le subnet di ISE | Lo spazio degli indirizzi per la rete virtuale con le subnet di ISE | Necessaria per il flusso del traffico *tra* le subnet nella rete virtuale. <p><p>**Importante**: per il flusso del traffico tra i *componenti* in ogni subnet, assicurarsi di aprire tutte le porte all'interno di ogni subnet. |
| Comunicazione con l'app per la logica | In ingresso | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE esterno: <br>Internet <br>(vedere la colonna **Note** ) | VirtualNetwork | Invece di usare il tag di servizio **Internet** , è possibile specificare l'indirizzo IP di origine per il computer o il servizio che chiama i trigger o i webhook di richiesta nell'app per la logica. <p><p>**Importante**: la chiusura o il blocco di questa porta impedisce le chiamate http alle app per la logica con trigger di richiesta. |
| Cronologia di esecuzione dell'app per la logica | In ingresso | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE esterno: <br>Internet <br>(vedere la colonna **Note** ) | VirtualNetwork | Invece di usare il tag di servizio **Internet** , è possibile specificare l'indirizzo IP di origine per il computer o il servizio da cui si vuole visualizzare la cronologia di esecuzione dell'app per la logica. <p><p>**Importante**: Sebbene la chiusura o il blocco di questa porta non impedisca la visualizzazione della cronologia di esecuzione, non è possibile visualizzare gli input e gli output per ogni passaggio della cronologia di esecuzione. |
| Progettazione di App per la logica - proprietà dinamiche | In ingresso | 454 | LogicAppsManagement | VirtualNetwork | Le richieste provengono dagli indirizzi IP in [ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) dell'endpoint di accesso delle app per la logica per tale area. |
| Distribuzione dei connettori | In ingresso | 454 | AzureConnectors | VirtualNetwork | Obbligatorio per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa la mancata riuscita delle distribuzioni di ISE e impedisce aggiornamenti o correzioni del connettore. |
| Controllo integrità rete | In ingresso | 454 | LogicApps | VirtualNetwork | Le richieste provengono dall'endpoint di accesso delle app per la logica per gli indirizzi IP in [ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) e in [uscita](../logic-apps/logic-apps-limits-and-config.md#outbound) per tale area. |
| Dipendenza da Gestione del servizio app | In ingresso | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Comunicazione da Gestione traffico di Azure | In ingresso | ISE interno: 454 <p><p>ISE esterno: 443 | AzureTrafficManager | VirtualNetwork | |
| Gestione API - endpoint di gestione | In ingresso | 3443 | APIManagement | VirtualNetwork | |
| Distribuzione dei criteri del connettore | In ingresso | 3443 | APIManagement | VirtualNetwork | Obbligatorio per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa la mancata riuscita delle distribuzioni di ISE e impedisce aggiornamenti o correzioni del connettore. |
| Comunicazione dall'app per la logica | In uscita | 80, 443 | VirtualNetwork | Varia in base alla destinazione | Endpoint per il servizio esterno con cui l'app per la logica deve comunicare. |
| Azure Active Directory | In uscita | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Gestione delle connessioni | In uscita | 443 | VirtualNetwork  | AppService | |
| Pubblicare i log di diagnostica e metriche | In uscita | 443 | VirtualNetwork  | AzureMonitor | |
| Dipendenza da Archiviazione di Azure | In uscita | 80, 443, 445 | VirtualNetwork | Archiviazione | |
| Dipendenza SQL di Azure | In uscita | 1433 | VirtualNetwork | SQL | |
| Integrità risorse di Azure | In uscita | 1886 | VirtualNetwork | AzureMonitor | Obbligatorio per la pubblicazione dello stato di integrità Integrità risorse |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | In uscita | 5672 | VirtualNetwork | Hub eventi | |
| Istanze di accesso Cache Azure per Redis tra Role Instances | In ingresso <br>In uscita | 6379-6383 | VirtualNetwork | VirtualNetwork | Inoltre, per usare ISE con cache di Azure per Redis, è necessario aprire le [porte in uscita e in ingresso descritte nelle domande frequenti su cache di Azure per Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

1. Nella casella di ricerca di Azure principale del [portale di Azure](https://portal.azure.com)immettere `integration service environments` come filtro e selezionare ambienti del servizio di **integrazione**.

   ![Trovare e selezionare "Integration Services environments"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nel riquadro **ambienti del servizio di integrazione** selezionare **Aggiungi**.

   ![Trovare e selezionare "Integration Services environments"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Specificare i dettagli per l'ambiente in uso e quindi selezionare **Verifica + crea**, ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatoria | Valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*Azure-Subscription-Name*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Sì | <*Azure-Resource-Group-Name*> | Un gruppo di risorse di Azure nuovo o esistente in cui si vuole creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Sì | <*nome ambiente*> | Il nome ISE, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`) e punti`.`(). |
   | **Posizione** | Sì | <*Azure-datacenter-area*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **SKU** | Sì | **Premium** o **Developer (nessun contratto di contratto)** | SKU ISE da creare e usare. Per le differenze tra questi SKU, vedere [SKU di ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: questa opzione è disponibile solo in fase di creazione di ISE e non può essere modificata in un secondo momento. |
   | **Capacità aggiuntiva** | Premium: <br>Sì <p><p>Sviluppatore <br>Non applicabile | Premium: <br>da 0 a 10 <p><p>Sviluppatore <br>Non applicabile | Il numero di unità di elaborazione aggiuntive da usare per questa risorsa ISE. Per aggiungere capacità dopo la creazione, vedere [aggiungere la capacità di ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Endpoint di accesso** | Sì | **Interno** o **esterno** | Tipo di endpoint di accesso da usare per ISE. Questi endpoint determinano se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale. <p><p>La selezione influiscono anche sul modo in cui è possibile visualizzare e accedere agli input e agli output nella cronologia delle esecuzioni dell'app per la logica. Per altre informazioni, vedere [ISE endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: questa opzione è disponibile solo in fase di creazione di ISE e non può essere modificata in un secondo momento. |
   | **Rete virtuale** | Sì | <*Azure-Virtual-Network-Name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non si ha una rete, [creare prima di tutto una rete virtuale di Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante**: è possibile eseguire questo Injection *solo* quando si crea ISE. |
   | **Subnet** | Sì | <*elenco di risorse subnet*> | Un ISE richiede quattro subnet *vuote* per la creazione e la distribuzione di risorse nell'ambiente. Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Creare una subnet**

   Per creare e distribuire le risorse nell'ambiente, ISE necessita di quattro subnet *vuote* che non sono delegate ad alcun servizio. Ogni subnet supporta un componente di app per la logica diverso usato in ISE. *Non è possibile* modificare questi indirizzi subnet dopo aver creato l'ambiente. Ogni subnet deve soddisfare questi requisiti:

   * Ha un nome che inizia con un carattere alfabetico o un carattere di sottolineatura (nessun numero) e non usa i `<`caratteri `>`seguenti `%`: `&`, `\\`, `?`, `/`,,,.

   * Usa il [formato CIDR (Inter-Domain Routing) con classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio di indirizzi della classe B.

   * Usa uno `/27` nello spazio degli indirizzi perché ogni subnet richiede 32 indirizzi. Ad esempio, `10.0.0.0/27` ha 32 indirizzi perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32. Altri indirizzi non offrono ulteriori vantaggi.  Per ulteriori informazioni sul calcolo degli indirizzi, vedere la pagina relativa ai [blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route seguente e collegare la tabella a ogni subnet usata da ISE:

     **Nome**: <*Route-Name*><br>
     **Prefisso indirizzo**: 0.0.0.0/0<br>
     **Hop successivo**: Internet

   1. Nell'elenco **subnet** selezionare **Gestisci configurazione Subnet**.

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Nel riquadro **subnet** selezionare **Subnet**.

      ![Aggiungere quattro subnet vuote](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Nel riquadro **Aggiungi subnet**, specificare queste informazioni.

      * **Nome**: nome della subnet
      * **Intervallo di indirizzi (blocco CIDR)**: l'intervallo della subnet nella rete virtuale e in formato CIDR

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Al termine, fare clic su **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

      > [!NOTE]
      > Se le subnet che si tenta di creare non sono valide, il portale di Azure Visualizza un messaggio, ma non blocca lo stato di avanzamento.

   Per ulteriori informazioni sulla creazione di subnet, vedere [aggiungere una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

1. Dopo che Azure ha convalidato correttamente le informazioni di ISE, selezionare **Crea**, ad esempio:

   ![Dopo aver completato la convalida, selezionare "Crea"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, che in genere richiede entro due ore per il completamento. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure selezionare l'icona notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   In caso contrario, seguire le istruzioni portale di Azure per la risoluzione dei problemi di distribuzione.

   > [!NOTE]
   > Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
   >
   > Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
   > Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
   > Vedere [eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Per visualizzare l'ambiente, selezionare **Vai alla risorsa** se Azure non passa automaticamente all'ambiente al termine della distribuzione.

1. Per verificare lo stato di integrità della rete per ISE, vedere [gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Per iniziare a creare app per la logica e altri artefatti in ISE, vedere [aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > I connettori ISE gestiti che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente in selezione connettore nella finestra di progettazione dell'app per la logica. Prima di poter usare questi connettori ISE, è necessario [aggiungerli manualmente a ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) , in modo che vengano visualizzati nella finestra di progettazione dell'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
