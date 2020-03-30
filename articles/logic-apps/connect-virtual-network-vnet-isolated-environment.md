---
title: Connettersi a reti virtuali di Azure con ISEConnect to Azure virtual networks with an ISE
description: Creare un ambiente del servizio di integrazione (ISE) che può accedere alle reti virtuali di Azure da app per la logica di AzureCreate an integration service environment (ISE) that can access Azure virtual networks (VNET) from Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fedc1f6ce8fbaeaf0d2cae3a1b04169192868e61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270693"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una rete virtuale di [Azure,](../virtual-network/virtual-networks-overview.md)creare un ambiente del servizio di [ *integrazione* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Un ISE è un ambiente isolato che utilizza l'archiviazione dedicata e altre risorse che vengono mantenute separate dal servizio "globale" app per la logica multi-tenant. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. Un ISE fornisce anche i propri indirizzi IP statici. Questi indirizzi IP sono separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio pubblico multi-tenant.

Quando si crea un ISE, Azure *inserisce* tale ISE nella rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando crei un'app per la logica o un account di integrazione, seleziona ISE come posizione. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Affinché le app per la logica e gli account di integrazione funzionino insieme in isE, entrambi devono usare lo *stesso ISE* della loro posizione.

Un ISE ha aumentato i limiti di durata dell'esecuzione, la conservazione dell'archiviazione, la velocità effettiva, i timeout delle richieste e risposte HTTP, le dimensioni dei messaggi e le richieste di connettori personalizzati. Per altre informazioni, vedere Limiti e configurazione per le app per la logica di Azure.For more [information,](../logic-apps/logic-apps-limits-and-config.md)see Limits and configuration for Azure Logic Apps . Per altre informazioni sulle ISE, vedere Accesso alle risorse della rete virtuale di Azure da App per la logica di Azure.To learn more about ISEs, see [Access to Azure Virtual Network resources from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Questo articolo illustra come completare queste attività usando il portale di Azure:This article shows you how to complete these tasks by using the Azure portal:

* Abilitare l'accesso per ISE.
* Crea il tuo ISE.
* Aggiungi ulteriore capacità alla tua ISE.

È anche possibile creare un ISE usando l'API REST App per la logica, inclusa la configurazione delle chiavi gestite dal cliente:You can also create an ISE by using the Logic Apps REST API, including setting up customer-managed keys:

* [Creare un ambiente del servizio di integrazione (ISE) usando l'API REST delle app per la logicaCreate an integration service environment (ISE) by using the Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurare le chiavi gestite dal cliente per crittografare i dati inattivi per gli ISE](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Le app per la logica, i trigger predefiniti, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario basato sul consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISE, vedere il modello di determinazione dei prezzi app per la [logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per i prezzi, vedere [Prezzi di App per la logica.](../logic-apps/logic-apps-pricing.md)

* Una [rete virtuale di Azure.](../virtual-network/virtual-networks-overview.md) Se non si dispone di una rete virtuale, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md).

  * La rete virtuale deve disporre di quattro subnet *vuote* per la creazione e la distribuzione di risorse in ISE. Ogni subnet supporta un componente diverso app per la logica usato in ISE. È possibile creare queste subnet in anticipo oppure attendere fino a quando non si crea ISE in cui è possibile creare subnet contemporaneamente. Ulteriori informazioni sui [requisiti delle subnet](#create-subnet).

  * I nomi delle subnet devono iniziare con un carattere alfabetico o `<`con `>` `%`un `&` `\\`carattere di sottolineatura e non possono utilizzare i seguenti caratteri: , , , , , , `?`, `/`, . 
  
  * Se si vuole distribuire ISE tramite un modello di Azure Resource Manager, assicurarsi innanzitutto di delegare una subnet vuota a Microsoft.Logic/integrationServiceEnvironment.If you want to deploy the ISE through an Azure Resource Manager template, first make sure that you delegate one empty subnet to Microsoft.Logic/integrationServiceEnvironment. Non è necessario eseguire questa delega quando si distribuisce tramite il portale di Azure.You don't need to do this delegation when you deploy through the Azure portal.

  * Assicurarsi che la rete virtuale [consenta l'accesso per ISE](#enable-access) in modo che ISE possa funzionare correttamente e rimanere accessibile.

  * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), che fornisce una connessione privata ai servizi cloud Microsoft facilitata dal provider di connettività, è necessario [creare una tabella](../virtual-network/manage-route-table.md) di route con la route seguente e collegare tale tabella a ogni subnet usata dall'ISE:

    **Nome**: nome *del percorso* <><br>
    **Prefisso indirizzo**: 0.0.0.0/0<br>
    **Hop successivo**: Internet

* Se si desidera usare server DNS personalizzati per la rete virtuale di Azure, [configurare tali server eseguendo la procedura seguente](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire ISE nella rete virtuale. Per ulteriori informazioni sulla gestione delle impostazioni del server DNS, vedere [Creare, modificare o eliminare una rete virtuale.](../virtual-network/manage-virtual-network.md#change-dns-servers)

  > [!NOTE]
  > Se si modificano le impostazioni del server DNS o DNS, è necessario riavviare ISE in modo che ISE possa rilevare tali modifiche. Per ulteriori informazioni, vedere [Riavviare ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Abilitare l'accesso per l'ISE

Quando si usa ISE con una rete virtuale di Azure, un problema di configurazione comune è la presenza di una o più porte bloccate. I connettori utilizzati per la creazione di connessioni tra ISE e i sistemi di destinazione potrebbero anche avere requisiti di porta specifici. Ad esempio, se si comunica con un sistema FTP utilizzando il connettore FTP, la porta utilizzata nel sistema FTP deve essere disponibile, ad esempio la porta 21 per l'invio dei comandi.

Per assicurarsi che ISE sia accessibile e che le app per la logica in tale ISE possano comunicare tra ogni subnet della rete virtuale, [aprire le porte descritte in questa tabella per ogni subnet.](#network-ports-for-ise) Se le porte necessarie non sono disponibili, ISE non funzionerà correttamente.

* Se si dispone di più istanze ISE che richiedono l'accesso ad altri endpoint con restrizioni IP, distribuire un firewall di [Azure](../firewall/overview.md) o [un'appliance virtuale](../virtual-network/virtual-networks-overview.md#filter-network-traffic) di rete nella rete virtuale e instradare il traffico in uscita attraverso tale firewall o appliance virtuale di rete. È quindi possibile configurare un singolo indirizzo IP in [uscita, pubblico, statico e prevedibile](connect-virtual-network-vnet-set-up-single-ip-address.md) che tutte le istanze ISE nella rete virtuale possono utilizzare per comunicare con i sistemi di destinazione. In questo modo, non è necessario impostare ulteriori aperture del firewall in tali sistemi di destinazione per ogni ISE.

   > [!NOTE]
   > È possibile utilizzare questo approccio per un singolo ISE quando lo scenario richiede di limitare il numero di indirizzi IP che devono accedere. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale sono adatti per lo scenario. Altre informazioni sui prezzi di Firewall di Azure .Learn more about [Azure Firewall pricing](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se sono state create una nuova rete virtuale di Azure e subnet senza vincoli, non è necessario configurare i gruppi di sicurezza di [rete (NSG)](../virtual-network/security-overview.md#network-security-groups) nella rete virtuale per controllare il traffico tra subnet.

* In una rete virtuale esistente è possibile *configurare facoltativamente* i gruppi di sicurezza di rete filtrando il [traffico di rete tra le subnet.](../virtual-network/tutorial-filter-network-traffic.md) Se si vuole usare questa route o se si usano già i gruppi di sicurezza di rete, assicurarsi di [aprire le porte in questa tabella](#network-ports-for-ise) nella rete virtuale in cui sono presenti gruppi di sicurezza di rete o si desidera configurare i gruppi di sicurezza di rete.

  > [!NOTE]
  > Se si utilizzano le regole di [protezione del gruppo](../virtual-network/security-overview.md#security-rules)di sicurezza di rete , è necessario utilizzare entrambi *i* protocolli TCP e UDP. Le regole di sicurezza del gruppo di sicurezza di rete descrivono le porte che è necessario aprire per gli indirizzi IP che devono accedere a tali porte. Assicurarsi che tutti i firewall, i router o altri elementi esistenti tra questi endpoint mantengano tali porte accessibili a tali indirizzi IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porte di rete utilizzate da ISE

Questa tabella descrive le porte nella rete virtuale di Azure usate da ISE e dove vengono usate. Per ridurre la complessità quando si creano regole di sicurezza, i tag del [servizio](../virtual-network/service-tags-overview.md) nella tabella rappresentano gruppi di prefissi di indirizzi IP per un servizio di Azure specifico.

> [!IMPORTANT]
> Le porte di origine sono effimere, quindi `*` assicurati di impostarle su per tutte le regole. Dove indicato, ISE interno e ISE esterno si riferiscono all'endpoint selezionato al momento della creazione di [ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Per ulteriori informazioni, vedere [Accesso agli endpoint](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Scopo | Direction | Porte di destinazione | Tag del servizio di origine | Tag del servizio di destinazione | Note |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicazione intersubnet all'interno della rete virtuale | In ingresso e in uscita | * | Spazio di indirizzi per la rete virtuale con le subnet di ISE | Spazio di indirizzi per la rete virtuale con le subnet di ISE | Necessario per il flusso del traffico *tra* le subnet nella rete virtuale. <p><p>**Importante**: affinché il traffico scorra tra i *componenti* in ogni subnet, assicurarsi di aprire tutte le porte all'interno di ogni subnet. |
| Comunicazione con l'app per la logica | In ingresso | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE esterno: <br>Internet <br>(vedere la colonna **Note)** | VirtualNetwork | Anziché usare il tag del servizio **Internet,** è possibile specificare l'indirizzo IP di origine per il computer o il servizio che chiama qualsiasi trigger di richiesta o webhook nell'app per la logica. <p><p>**Importante:** la chiusura o il blocco di questa porta impedisce chiamate HTTP ad app per la logica con trigger di richiesta. |
| Cronologia di esecuzione dell'app per la logica | In ingresso | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE esterno: <br>Internet <br>(vedere la colonna **Note)** | VirtualNetwork | Anziché usare il tag del servizio **Internet,** è possibile specificare l'indirizzo IP di origine per il computer o il servizio da cui si vuole visualizzare la cronologia di esecuzione dell'app per la logica. <p><p>**Importante**: Anche se la chiusura o il blocco di questa porta non impedisce di visualizzare la cronologia di esecuzione, non è possibile visualizzare gli input e gli output per ogni passaggio della cronologia di esecuzione. |
| Progettazione di App per la logica - proprietà dinamiche | In ingresso | 454 | LogicAppsManagement | VirtualNetwork | Le richieste provengono dagli indirizzi IP [in ingresso dell'endpoint](../logic-apps/logic-apps-limits-and-config.md#inbound) di accesso App per la logica per tale area. |
| Distribuzione dei connettori | In ingresso | 454 | AzureConnettoriAzureConnectors | VirtualNetwork | Obbligatorio per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa l'esito negativo delle distribuzioni di ISE e impedisce gli aggiornamenti o le correzioni dei connettori. |
| Controllo dell'integrità della rete | In ingresso | 454 | LogicApps | VirtualNetwork | Le richieste provengono dall'endpoint di accesso App per la logica per gli indirizzi IP [in ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) e [in uscita](../logic-apps/logic-apps-limits-and-config.md#outbound) per tale area. |
| Dipendenza da Gestione del servizio app | In ingresso | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Comunicazione da Gestione traffico di AzureCommunication from Azure Traffic Manager | In ingresso | ISE interno: 454 <p><p>ISE esterna: 443 | AzureTrafficManager | VirtualNetwork | |
| Gestione API - endpoint di gestione | In ingresso | 3443 | APIManagement | VirtualNetwork | |
| Distribuzione dei criteri connettore | In ingresso | 3443 | APIManagement | VirtualNetwork | Obbligatorio per la distribuzione e l'aggiornamento dei connettori. La chiusura o il blocco di questa porta causa l'esito negativo delle distribuzioni di ISE e impedisce gli aggiornamenti o le correzioni dei connettori. |
| Comunicazione dall'app per la logica | In uscita | 80, 443 | VirtualNetwork | Varia in base alla destinazione | Endpoint per il servizio esterno con cui l'app per la logica deve comunicare. |
| Azure Active Directory | In uscita | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Gestione delle connessioni | In uscita | 443 | VirtualNetwork  | AppService | |
| Pubblicare i log di diagnostica e metriche | In uscita | 443 | VirtualNetwork  | AzureMonitor | |
| Dipendenza da Archiviazione di Azure | In uscita | 80, 443, 445 | VirtualNetwork | Archiviazione | |
| Dipendenza SQL di AzureAzure SQL dependency | In uscita | 1433 | VirtualNetwork | SQL | |
| Integrità risorse di Azure | In uscita | 1886 | VirtualNetwork | AzureMonitor | Obbligatorio per la pubblicazione dello stato di integrità in Integrità delle risorseRequired for publishing health status to Resource Health |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | In uscita | 5672 | VirtualNetwork | Hub eventi | |
| Istanze di accesso Cache Azure per Redis tra Role Instances | In ingresso <br>In uscita | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Inoltre, affinché ISE funzioni con la cache di Azure per Redis, è necessario aprire queste porte in uscita e in ingresso descritte nelle domande frequenti sulla cache di [Azure per Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

1. Nel [portale di Azure](https://portal.azure.com), nella casella `integration service environments` di ricerca principale di Azure immettere come filtro e selezionare Ambienti del servizio di **integrazione**.

   ![Trovare e selezionare "Ambienti del servizio di integrazione"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nel riquadro Ambienti del servizio di **integrazione** selezionare **Aggiungi**.

   ![Trovare e selezionare "Ambienti del servizio di integrazione"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Fornire i dettagli seguenti per l'ambiente e quindi selezionare **Revisione e creazione**, ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*Nome sottoscrizione di AzureAzure-subscription-name*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Sì | <*Nome-gruppo-risorse di AzureAzure-resource-group-name*> | Un gruppo di risorse di Azure nuovo o esistente in cui si vuole creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Sì | <*nome dell'ambiente*> | Il nome ISE, che può contenere solo`-`lettere,`_`numeri, trattini ( ), caratteri di sottolineatura ( ) e punti ( ).`.` |
   | **Percorso** | Sì | <*Area di Azure-datacenterAzure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **Sku** | Sì | **Premium** o **Sviluppatore (nessun sLA)** | SKU ISE da creare e utilizzare. Per le differenze tra questi SKU, vedere [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: Questa opzione è disponibile solo al momento della creazione di ISE e non può essere modificata in un secondo momento. |
   | **Capacità aggiuntiva** | Premium: <br>Sì <p><p>Sviluppatore: <br>Non applicabile | Premium: <br>Da 0 a 10 anni <p><p>Sviluppatore: <br>Non applicabile | Numero di unità di elaborazione aggiuntive da utilizzare per questa risorsa ISE. Per aggiungere capacità dopo la creazione, vedere [Aggiungere capacità ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Endpoint di accesso** | Sì | **Interno** o **Esterno** | Tipo di endpoint di accesso da utilizzare per ISE. Questi endpoint determinano se i trigger di richiesta o webhook nelle app per la logica in ISE possono ricevere chiamate dall'esterno della rete virtuale. <p><p>La selezione influisce anche sul modo in cui è possibile visualizzare e accedere a input e output nell'app per la logica per eseguire la cronologia. Per ulteriori informazioni, vedere [Accesso all'endpoint ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: Questa opzione è disponibile solo al momento della creazione di ISE e non può essere modificata in un secondo momento. |
   | **Rete virtuale** | Sì | <*Nome-rete virtuale-AzureAzure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non si dispone di una rete, creare prima una rete virtuale di [Azure.](../virtual-network/quick-create-portal.md) <p><p>**Importante**: È possibile eseguire questa iniezione *solo* quando si crea l'ISE. |
   | **Subnet** | Sì | <*subnet-resource-list*> | Un ISE richiede quattro subnet *vuote* per la creazione e la distribuzione di risorse nell'ambiente. Per creare ciascuna subnet, [seguire i passaggi descritti in questa tabella](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Creare una subnet**

   Per creare e distribuire risorse nell'ambiente, ISE necessita di quattro subnet *vuote* che non sono delegate ad alcun servizio. Ogni subnet supporta un componente diverso app per la logica usato in ISE. Non *è possibile* modificare questi indirizzi di subnet dopo aver creato l'ambiente. Ogni subnet deve soddisfare i requisiti seguenti:Each subnet needs to meet these requirements:

   * Ha un nome che inizia con un carattere alfabetico o un carattere `<`di `>` `%`sottolineatura (senza numeri) e non utilizza questi caratteri: , , `&`, , , `\\`, `?`, . `/`

   * Utilizza il [formato CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio di indirizzi di classe B.

   * Utilizza almeno `/27` un nello spazio di indirizzi perché ogni subnet richiede *almeno*32 indirizzi . Ad esempio:

     * `10.0.0.0/28`ha solo 16 indirizzi ed è troppo piccolo perché 2<sup>(32-28)</sup> è 2<sup>4</sup> o 16.

     * `10.0.0.0/27` ha 32 indirizzi perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32.

     * `10.0.0.0/24` ha 256 indirizzi perché 2<sup>(32-24)</sup> è 2<sup>8</sup> o 256. Tuttavia, più indirizzi non forniscono ulteriori vantaggi.

     Per ulteriori informazioni sul calcolo degli indirizzi, vedere [Blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), è necessario [creare una tabella](../virtual-network/manage-route-table.md) di route con la route seguente e collegare tale tabella a ogni subnet usata da ISE:

     **Nome**: nome *del percorso* <><br>
     **Prefisso indirizzo**: 0.0.0.0/0<br>
     **Hop successivo**: Internet

   1. Nell'elenco **Subnet** selezionare **Gestisci configurazione subnet.**

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Nel riquadro **Subnet** selezionare **Subnet**.

      ![Aggiungere quattro subnet vuote](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Nel riquadro **Aggiungi subnet**, specificare queste informazioni.

      * **Nome**: Il nome della subnet
      * **Intervallo di indirizzi (blocco CIDR):** intervallo della subnet nella rete virtuale e in formato CIDRAddress range (CIDR block): Your subnet's range in your virtual network and in CIDR format

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Al termine, fare clic su **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

      > [!NOTE]
      > Se le subnet che si tenta di creare non sono valide, il portale di Azure visualizza un messaggio, ma non blocca lo stato di avanzamento.

   Per altre informazioni sulla creazione di subnet, vedere Aggiungere una subnet di [rete virtuale.](../virtual-network/virtual-network-manage-subnet.md)

1. Dopo che Azure ha convalidato correttamente le informazioni ISE, selezionare Crea , ad esempio:After Azure successfully validates your ISE information, select **Create**, for example:

   ![Dopo la convalida, selezionare "Crea"After successful validation, select "Create"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, che in genere richiede entro due ore per completare l'operazione. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nella barra degli strumenti di Azure selezionare l'icona delle notifiche, che apre il riquadro delle notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   In caso contrario, seguire le istruzioni del portale di Azure per la risoluzione dei problemi di distribuzione.

   > [!NOTE]
   > Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
   >
   > Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
   > Quando si eliminano reti virtuali, assicurarsi che nessuna risorsa sia ancora connessa. 
   > Vedere [Eliminare rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Per visualizzare l'ambiente, selezionare **Vai alla risorsa** se Azure non passa automaticamente all'ambiente al termine della distribuzione.

1. Per verificare l'integrità della rete per ISE, vedere [Gestire l'ambiente](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)del servizio di integrazione .

1. Per avviare la creazione di app per la logica e altri elementi in ISE, vedere Aggiungere risorse agli ambienti del servizio di [integrazione.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

   > [!IMPORTANT]
   > I connettori ISE gestiti che diventano disponibili dopo la creazione di ISE non vengono visualizzati automaticamente nel selettore connettore in Progettazione app per la logica. Prima di poter utilizzare questi connettori ISE, è necessario aggiungere manualmente [tali connettori a ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) in modo che vengano visualizzati in Progettazione app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazioneAdd resources to integration service environments](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
