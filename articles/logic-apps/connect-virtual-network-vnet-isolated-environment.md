---
title: Connettersi alle reti virtuali di Azure con un ISE
description: Creare un ambiente del servizio di integrazione (ISE) in grado di accedere alle reti virtuali di Azure da App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 6c5badf4760bff559fb050278df84c7ad6e703bd
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616944"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure usando un ambiente del servizio di integrazione (ISE)

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Un ISE è un ambiente dedicato che usa una risorsa di archiviazione dedicata e altre risorse separate dal servizio App per la logica, "globale" e multi-tenant. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. Un ISE fornisce anche indirizzi IP statici. Tali indirizzi IP sono separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio multi-tenant pubblico.

Durante la creazione di un ISE, Azure *inserisce* tale ISE alla rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare l’ISE come posizione per l'app o l'account. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Per fare in modo che le app per la logica e gli account di integrazione interagiscano tra logo in un ISE, è necessario che entrambi usino lo *stesso ISE* come propria posizione.

Un ISE ha aumentato i limiti per durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richieste e risposte HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [Limiti e configurazione per App per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md). Per altre informazioni sugli ISE, vedere [Accedere alle risorse della rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Questo articolo spiega come completare queste attività usando il portale di Azure:

* Abilitare l'accesso per l'ISE.
* Creare l'ISE.
* Aggiungere capacità aggiuntiva all’ISE.

È anche possibile creare un ISE usando il [modello di avvio rapido di Azure Resource Manager di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) o usando l'API REST App per la logica, inclusa la configurazione delle chiavi gestite dal cliente:

* [Creare un ambiente del servizio di integrazione (ISE) usando l'API REST App per la logica](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurare le chiavi gestite dal cliente per crittografare i dati inattivi per gli ISE](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Le app per la logica, i trigger predefiniti, le azioni predefinite e i connettori eseguiti nell'ambiente del servizio di integrazione usano un piano tariffario diverso da quello con pagamento in base al consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli ISE, vedere il [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md#fixed-pricing). Per informazioni sui prezzi, vedere [Prezzi di App per la logica](../logic-apps/logic-apps-pricing.md).

* Una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). La rete virtuale deve avere quattro subnet *vuote* , necessarie per la creazione e la distribuzione di risorse in ISE e usate da questi componenti interni e nascosti:

  * Calcolo di app per la logica
  * Ambiente del servizio app interno (connettori)
  * Gestione API interna (connettori)
  * Redis interni per la memorizzazione nella cache e le prestazioni
  
  È possibile creare le subnet in anticipo, oppure è possibile attendere fino a quando non si crea ISE per poter creare le subnet nello stesso momento. Tuttavia, prima di creare le subnet, esaminare i [requisiti della subnet](#create-subnet).

  > [!IMPORTANT]
  >
  > Non usare gli spazi di indirizzi IP seguenti per la rete virtuale o le subnet perché non sono risolvibili dalle app per la logica di Azure:<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * Assicurarsi che la rete virtuale [consenta l'accesso per l’ISE](#enable-access) in modo che l’ISE possa funzionare correttamente e rimanere accessibile.

  * Se si usa o si vuole usare [ExpressRoute](../expressroute/expressroute-introduction.md) insieme a [tunneling forzato](../firewall/forced-tunneling.md), è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route specifica seguente e collegare la tabella di route a ogni subnet usata da ISE:

    **Nome** : < *route-name*><br>
    **Prefisso indirizzo** : 0.0.0.0/0<br>
    **Hop successivo** : Internet
    
    Questa tabella di route specifica è necessaria in modo che i componenti delle app per la logica possano comunicare con altri servizi di Azure dipendenti, ad esempio archiviazione di Azure e database SQL di Azure. Per ulteriori informazioni su questa route, vedere il [prefisso degli indirizzi 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Se non si usa il tunneling forzato con ExpressRoute, questa tabella di route specifica non è necessaria.
    
    ExpressRoute consente di estendere le reti locali in Microsoft Cloud e di connettersi ai servizi cloud Microsoft tramite una connessione privata facilitata dal provider di connettività. In particolare, ExpressRoute è una rete privata virtuale che instrada il traffico su una rete privata, anziché tramite la rete Internet pubblica. Le app per la logica possono connettersi a risorse locali che si trovano nella stessa rete virtuale quando si connettono tramite ExpressRoute o una rete privata virtuale.
   
  * Se si usa un' [appliance virtuale di rete](../virtual-network/virtual-networks-udr-overview.md#user-defined), assicurarsi di non abilitare la terminazione TLS/SSL o modificare il traffico TLS/SSL in uscita. Assicurarsi anche di non abilitare l'ispezione per il traffico che ha origine dalla subnet di ISE. Per altre informazioni, vedere [routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

  * Se si desidera usare server DNS personalizzati per la rete virtuale di Azure [configurare tali server seguendo questi passaggi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) prima di distribuire l'ISE sulla rete virtuale. Per altre informazioni sulla gestione delle impostazioni del server DNS, vedere [Creare, modificare o eliminare una rete virtuale](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Se si modificano le impostazioni del server DNS o del server DNS, è necessario riavviare ISE in modo che ISE possa rilevare tali modifiche. Per altre informazioni, vedere [Riavviare l’ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Abilitare l'accesso per l'ISE

Quando si usa un ISE con una rete virtuale di Azure, un problema di configurazione comune è la presenza di una o più porte bloccate. I connettori utilizzati per la creazione di connessioni tra l'ISE e il sistema di destinazione potrebbero avere requisiti propri per le porte. Ad esempio, se si comunica con un sistema FTP usando il connettore FTP, la porta utilizzata nel sistema FTP deve essere disponibile, ad esempio la porta 21 per l'invio di comandi.

Per assicurarsi che l’ISE sia accessibile e che le app per la logica in tale ISE siano in grado di comunicare su ogni subnet della rete virtuale, [aprire le porte descritte in questa tabella per ogni subnet](#network-ports-for-ise). Se le porte obbligatorie non sono disponibili, l’ISE non funzionerà correttamente.

* Se sono presenti più istanze dell’ISE che necessitano dell’accesso ad altri endpoint con restrizioni IP, distribuire un [Firewall di Azure](../firewall/overview.md) o un'[appliance virtuale di rete](../virtual-network/virtual-networks-overview.md#filter-network-traffic) nella rete virtuale e instradare il traffico in uscita attraverso il firewall o l'appliance virtuale di rete. È quindi possibile [configurare un indirizzo IP singolo, in uscita, pubblico, statico e prevedibile](connect-virtual-network-vnet-set-up-single-ip-address.md) che tutte le istanze dell’ISE nella rete virtuale possano usare per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall in questi sistemi di destinazione per ogni ISE.

   > [!NOTE]
   > È possibile usare questo approccio per un singolo ISE quando lo scenario richiede la limitazione del numero di indirizzi IP che richiedono l'accesso. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale hanno senso per lo scenario. Altre informazioni su [Prezzi del servizio Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se è stata creata una nuova rete virtuale di Azure e le subnet senza vincoli, non è necessario configurare [gruppi di sicurezza di rete (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) nella rete virtuale per controllare il traffico tra le subnet.

* Per una rete virtuale esistente, è possibile *facoltativamente* configurare [gruppi di sicurezza di rete (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) per [filtrare il traffico di rete tra subnet](../virtual-network/tutorial-filter-network-traffic.md). Se si vuole passare a questa route o se si sta già usando NSG, assicurarsi di [aprire le porte descritte in questa tabella](#network-ports-for-ise) per tali NSG.

  Quando si configurano [regole di sicurezza NSG](../virtual-network/network-security-groups-overview.md#security-rules), è necessario utilizzare *entrambi* i protocolli **TCP** e **UDP** oppure è possibile seleziona invece **Qualsiasi** in modo da non dover creare regole separate per ogni protocollo. Le regole di sicurezza NSG descrivono le porte che è necessario aprire per gli indirizzi IP che necessitano dell'accesso a tali porte. Assicurarsi che eventuali firewall, router o altri elementi presenti tra questi endpoint mantengano tali porte accessibili a tali indirizzi IP.

* Se si configura il tunneling forzato tramite il firewall per reindirizzare il traffico associato a Internet, esaminare i [requisiti aggiuntivi per il tunneling forzato](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Porte di rete usate dall’ISE

Questa tabella descrive le porte necessarie all’ISE per essere accessibile e lo scopo di tali porte. Per ridurre la complessità quando si configurano le regole di sicurezza, nella tabella vengono usati [tag del servizio](../virtual-network/service-tags-overview.md) che rappresentano gruppi di prefissi di indirizzi IP per un servizio di Azure specifico. Ove indicati, *ISE interno* e *ISE esterno* si riferiscono all’ [endpoint di accesso selezionato durante la creazione dell’ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Per altre informazioni, vedere [Accesso endpoint](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)

> [!IMPORTANT]
> Per tutte le regole, assicurarsi di impostare le porte di origine su `*` perché le porte di origine sono temporanee.

#### <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| Scopo | Tag del servizio di origine o indirizzi IP | Porte di origine | Tag del servizio di destinazione o indirizzi IP | Porte di destinazione | Note |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicazione tra subnet in una rete virtuale | Spazio degli indirizzi per la rete virtuale con subnet ISE | * | Spazio degli indirizzi per la rete virtuale con subnet ISE | * | Necessarie per il flusso del traffico *tra* le subnet nella rete virtuale. <p><p>**Importante** : Per il flusso del traffico tra i *componenti* in ogni subnet, assicurarsi di aprire tutte le porte all'interno di ogni subnet. |
| Both: <p>Comunicazione con l'app per la logica <p><p>Cronologia di esecuzione dell'app per la logica| ISE interno: <br>**VirtualNetwork** <p><p>ISE esterno: **Internet** o vedere **Note** | * | **VirtualNetwork** | 443 | Invece di usare il tag del servizio **Internet** , è possibile specificare l'indirizzo IP di origine per questi elementi: <p><p>- Il computer o il servizio che chiama qualsiasi trigger o webhook di richiesta nell'app per la logica <p>- Il computer o il servizio da cui si vuole accedere alla cronologia di esecuzioni dell'app per la logica <p><p>**Importante** : La chiusura o il blocco di questa porta impedisce le chiamate alle app per la logica con trigger o webhook di richiesta. Viene impedito anche l'accesso a input e output per ogni passaggio nella cronologia di esecuzione. Tuttavia, non viene impedito l'accesso alla cronologia di esecuzioni dell'app per la logica.|
| Progettazione app per la logica: proprietà dinamiche | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Le richieste provengono dagli [indirizzi IP in ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) dell’endpoint di accesso di App per la logica per tale area. |
| Distribuzione dei connettori | **AzureConnectors** | * | **VirtualNetwork** | 454 | Obbligatoria per distribuire e aggiornare i connettori. La chiusura o il blocco di questa porta causa l'esito negativo delle distribuzioni ISE e impedisce gli aggiornamenti e le correzioni del connettore. |
| Controllo integrità della rete | **LogicApps** | * | **VirtualNetwork** | 454 | Le richieste provengono dagli [indirizzi IP in ingresso](../logic-apps/logic-apps-limits-and-config.md#inbound) e gli [indirizzi IP in uscita](../logic-apps/logic-apps-limits-and-config.md#outbound) dell’endpoint di accesso di App per la logica per tale area. |
| Dipendenza da Gestione del servizio app | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Comunicazione da Gestione traffico di Azure | **AzureTrafficManager** | * | **VirtualNetwork** | ISE interno: 454 <p><p>ISE esterno: 443 ||
| Both: <p>Distribuzione dei criteri dei connettori <p>Gestione API - endpoint di gestione | **APIManagement** | * | **VirtualNetwork** | 3443 | Per la distribuzione dei criteri dei connettori, l’accesso alla porta è obbligatorio per distribuire e aggiornare i connettori. La chiusura o il blocco di questa porta causa l'esito negativo delle distribuzioni ISE e impedisce gli aggiornamenti e le correzioni del connettore. |
| Istanze di accesso Cache Azure per Redis tra Role Instances | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383 e vedere le **Note**| Per fare in modo che l’ISE funzioni con Cache di Azure per Redis, è necessario aprire queste [porte in uscita e in ingresso descritte dalle Domande frequenti su Cache Redis di Azure](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| Scopo | Tag del servizio di origine o indirizzi IP | Porte di origine | Tag del servizio di destinazione o indirizzi IP | Porte di destinazione | Note |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicazione tra subnet in una rete virtuale | Spazio degli indirizzi per la rete virtuale con subnet ISE | * | Spazio degli indirizzi per la rete virtuale con subnet ISE | * | Necessarie per il flusso del traffico *tra* le subnet nella rete virtuale. <p><p>**Importante** : Per il flusso del traffico tra i *componenti* in ogni subnet, assicurarsi di aprire tutte le porte all'interno di ogni subnet. |
| Comunicazione dall'app per la logica | **VirtualNetwork** | * | Varia in base alla destinazione | 80, 443 | La destinazione varia in base agli endpoint per il servizio esterno con cui l'app per la logica deve comunicare. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Dipendenza da Archiviazione di Azure | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Gestione delle connessioni | **VirtualNetwork** | * | **AppService** | 443 ||
| Pubblicare i log di diagnostica e metriche | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Dipendenza Azure SQL | **VirtualNetwork** | * | **SQL** | 1433 ||
| Integrità risorse di Azure | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Obbligatoria per la pubblicazione dello stato di integrità in Integrità risorse. |
| Dipendenza dal criterio Registra a Hub eventi e dall'agente di monitoraggio | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Istanze di accesso Cache Azure per Redis tra Role Instances | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383 e vedere le **Note**| Per fare in modo che l’ISE funzioni con Cache di Azure per Redis, è necessario aprire queste [porte in uscita e in ingresso descritte dalle Domande frequenti su Cache Redis di Azure](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Risoluzione dei nomi DNS | **VirtualNetwork** | * | Indirizzi IP per qualsiasi server DNS (Custom Domain Name System) nella rete virtuale | 53 | Obbligatorio solo quando si usano server DNS personalizzati nella rete virtuale |
|||||||

Inoltre, è necessario aggiungere regole in uscita per [ambiente del servizio app (ASE)](../app-service/environment/intro.md):

* Se si usa il firewall di Azure, è necessario configurare il firewall con il [tag FQDN (nome di dominio completo)](../firewall/fqdn-tags.md#current-fqdn-tags)ambiente del servizio app (ASE), che consente l'accesso in uscita al traffico della piattaforma ASE.

* Se si usa un'appliance firewall diversa da Azure firewall, è necessario configurare il firewall con *tutte* le regole elencate nelle [dipendenze di integrazione del firewall](../app-service/environment/firewall-integration.md#dependencies) necessarie per ambiente del servizio app.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Requisiti per il tunneling forzato

Se si configura o si usa il [tunneling forzato](../firewall/forced-tunneling.md) attraverso il firewall, è necessario consentire dipendenze esterne aggiuntive per ISE. Il tunneling forzato consente di reindirizzare il traffico associato a Internet a un hop successivo designato, ad esempio la rete privata virtuale (VPN) o a un'appliance virtuale, anziché a Internet, in modo che sia possibile ispezionare e controllare il traffico di rete in uscita.

In genere, tutto il traffico delle dipendenze in uscita ISE passa attraverso l'indirizzo IP virtuale (VIP) di cui è stato effettuato il provisioning con ISE. Tuttavia, se si modifica il routing del traffico verso o da ISE, è necessario consentire le seguenti dipendenze in uscita nel firewall impostando l'hop successivo su `Internet` . Se si usa il firewall di Azure, seguire le [istruzioni per configurare il firewall con la ambiente del servizio app](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).

Se non si consente l'accesso per queste dipendenze, la distribuzione di ISE ha esito negativo e l'ISE distribuito smette di funzionare:

* [Indirizzi di gestione dell'Ambiente del servizio app](../app-service/environment/management-addresses.md)

* [Indirizzi di gestione API di Azure](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Indirizzi di gestione di gestione traffico di Azure](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [App per la logica indirizzi in ingresso e in uscita per l'area ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [Indirizzi IP di Azure per i connettori nell'area ISE, che si trovano in questo file di download](https://www.microsoft.com/download/details.aspx?id=56519)

* È necessario abilitare gli endpoint di servizio per SQL di Azure, archiviazione, bus di servizio e hub eventi perché non è possibile inviare traffico attraverso un firewall a questi servizi.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

1. Nella casella di ricerca principale di Azure del [portale di Azure](https://portal.azure.com), immettere `integration service environments` come filtro e selezionare **ambienti del servizio di integrazione**.

   ![Trovare e selezionare "Ambienti del servizio di integrazione"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nel riquadro **Ambienti del servizio di integrazione** , selezionare **Aggiungi**.

   ![Selezionare "Aggiungi" per creare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Specificare questi dettagli per l'ambiente, quindi selezionare **Rivedi e crea** , ad esempio:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **Sottoscrizione** | Sì | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente |
   | **Gruppo di risorse** | Sì | <*Azure-resource-group-name*> | Un gruppo di risorse di Azure nuovo o esistente in cui si desidera creare l'ambiente |
   | **Nome dell'ambiente del servizio di integrazione** | Sì | <*Nome ambiente*> | Il nome dell'ISE, che può contenere solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`) e punti (`.`). |
   | **Posizione** | Sì | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui distribuire l'ambiente |
   | **SKU** | Sì | **Premium** o **Developer (nessun contratto di servizio)** | SKU ISE da creare e usare. Per le differenze tra questi SKU, vedere [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante** : Questa opzione è disponibile solo in fase di creazione di ISE e non può essere modificata in un secondo momento. |
   | **Capacità aggiuntiva** | Premium: <br>Sì <p><p>Developer: <br>Non applicabile | Premium: <br>da 0 a 10 <p><p>Developer: <br>Non applicabile | Numero di unità di elaborazione supplementari da usare per questa risorsa dell'ambiente del servizio di integrazione. Per aggiungere la capacità dopo la creazione, vedere [Aggiungere la capacità ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Endpoint di accesso** | Sì | **Interno** o **Esterno** | Tipo di endpoint di accesso da usare per l’ISE. Questi endpoint determinano se i trigger o webhook di richiesta nelle app per la logica nell’ISE possono ricevere chiamate dall'esterno della rete virtuale. <p><p>Se ad esempio si vogliono usare i trigger basati su webhook seguenti, assicurarsi di selezionare **External (esterno** ): <p><p>-Azure DevOps <br>-Griglia di eventi di Azure <br>-Common Data Service <br>-Office 365 <br>-SAP (versione ISE) <p><p>La selezione influisce anche sul modo in cui è possibile visualizzare e accedere agli input e agli output nella cronologia di esecuzione delle app per la logica. Per altre informazioni, vedere [Accesso endpoint dell’ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante** : È possibile selezionare l'endpoint di accesso solo durante la creazione dell’ISE e non è possibile modificare questa opzione in un secondo momento. |
   | **Rete virtuale** | Sì | <*Azure-virtual-network-name*> | La rete virtuale di Azure in cui si desidera collegare l'ambiente in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Se non si dispone di una rete, [creare prima una rete virtuale di Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante** : È possibile seguire questo collegamento *solo* quando si crea l'ISE. |
   | **Subnet** | Sì | <*subnet-resource-list*> | Un ISE richiede quattro subnet *vuote* , che sono necessarie per la creazione e la distribuzione di risorse in ISE e vengono usate dai componenti interni delle app per la logica, ad esempio i connettori e la memorizzazione nella cache per le prestazioni. <p>**Importante** : assicurarsi [di esaminare i requisiti della subnet prima di continuare con la procedura seguente per creare le subnet](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Crea subnet**

   ISE richiede quattro subnet *vuote* , che sono necessarie per creare e distribuire le risorse in ISE e vengono usate dai componenti interni delle app per la logica, ad esempio i connettori e la memorizzazione nella cache per le prestazioni. *Non è possibile* modificare gli indirizzi di queste subnet dopo aver creato l'ambiente. Se si crea e si distribuisce ISE tramite il portale di Azure, assicurarsi di non delegare tali subnet a tutti i servizi di Azure. Tuttavia, se si crea e si distribuisce ISE tramite l'API REST, Azure PowerShell o un modello di Azure Resource Manager, è necessario [delegare](../virtual-network/manage-subnet-delegation.md) una subnet vuota a `Microsoft.integrationServiceEnvironment` . Per ulteriori informazioni, vedere [Add a subnet Delegation](../virtual-network/manage-subnet-delegation.md).

   Ogni subnet deve soddisfare questi requisiti:

   * Usa un nome che inizia con un carattere alfabetico o un carattere di sottolineatura (nessun numero) e non usa i caratteri seguenti: `<` , `>` , `%` , `&` , `\\` , `?` , `/` .

   * Usa il [formato CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e uno spazio indirizzi di classe B.
   
     > [!IMPORTANT]
     >
     > Non usare gli spazi di indirizzi IP seguenti per la rete virtuale o le subnet perché non sono risolvibili dalle app per la logica di Azure:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Usa un `/27` nello spazio degli indirizzi perché ogni subnet richiede 32 indirizzi. Ad esempio, `10.0.0.0/27` ha 32 indirizzi perché 2<sup>(32-27)</sup> è 2<sup>5</sup> o 32. Altri indirizzi non offrono ulteriori vantaggi. Per altre informazioni sul calcolo degli indirizzi, vedere [Blocchi CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md), è necessario [creare una tabella di route](../virtual-network/manage-route-table.md) con la route seguente e collegare tale tabella a ogni subnet usata dall’ISE:

     **Nome** : < *route-name*><br>
     **Prefisso indirizzo** : 0.0.0.0/0<br>
     **Hop successivo** : Internet

   1. Nell'elenco **Subnet** , selezionare **Gestisci configurazione subnet**.

      ![Gestisci configurazione subnet](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Nel riquadro **Subnet** , scegliere **Subnet**.

      ![Aggiungere quattro subnet vuote](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Nel riquadro **Aggiungi subnet** , specificare queste informazioni.

      * **Name** : Nome per la subnet
      * **Intervallo di indirizzi (blocco CIDR)** : L'intervallo di subnet nella rete virtuale e nel formato CIDR

      ![Aggiungere i dettagli della subnet](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Al termine, fare clic su **OK**.

   1. Ripetere questi passaggi per altre tre subnet.

      > [!NOTE]
      > Se le subnet che si tenta di creare non sono valide, il portale di Azure visualizza un messaggio, ma non blocca lo stato di avanzamento.

   Per altre informazioni sulle creazione di subnet, vedere [Aggiungere una subnet di rete virtuale](../virtual-network/virtual-network-manage-subnet.md).

1. Dopo l'avvenuta convalida da parte di Azure delle informazioni dell'ISE, selezionare **Crea** , ad esempio:

   ![Al termine della convalida, selezionare "Crea"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure avvia la distribuzione dell'ambiente, che in genere richiede due ore per il completamento. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure, selezionare l'icona delle notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se la distribuzione avviene con successo, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   In caso contrario, seguire le istruzioni portale di Azure per la risoluzione dei problemi di distribuzione.

   > [!NOTE]
   > Se la distribuzione non riesce o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora o possibilmente più a lungo in rari casi, prima di rilasciare le subnet. Quindi, potrebbe essere necessario attendere prima di poter riutilizzare tali subnet in un altro ISE.
   >
   > Se si elimina la rete virtuale, Azure impiega in genere fino a due ore per rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
   > Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
   > Vedere [Eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Per visualizzare l'ambiente, selezionare **Vai alla risorsa** se Azure non mostra automaticamente l'ambiente al termine della distribuzione.

1. Per un ISE con accesso endpoint *esterno* , è necessario creare un gruppo di sicurezza di rete, se non ne è già presente uno, e aggiungere una regola di sicurezza in ingresso per consentire il traffico dagli indirizzi IP in uscita del connettore gestito. Per impostare questa regola, attenersi alla seguente procedura:

   1. Nel menu ISE, in **Impostazioni** , selezionare **Proprietà**.

   1. In **indirizzi IP in uscita connettore** copiare gli intervalli di indirizzi IP pubblici, che vengono visualizzati anche in questo articolo, [limiti e configurazione-indirizzi IP in uscita](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Creare un gruppo di sicurezza di rete, se non ne è già presente uno.
   
   1. In base alle informazioni riportate di seguito, aggiungere una regola di sicurezza in ingresso per gli indirizzi IP in uscita pubblici copiati. Per altre informazioni, vedere [esercitazione: filtrare il traffico di rete con un gruppo di sicurezza di rete usando il portale di Azure](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Scopo | Tag del servizio di origine o indirizzi IP | Porte di origine | Tag del servizio di destinazione o indirizzi IP | Porte di destinazione | Note |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Consentire il traffico dagli indirizzi IP in uscita del connettore | <*connettore-pubblico-in uscita-IP-indirizzi*> | * | Spazio degli indirizzi per la rete virtuale con subnet ISE | * | |
      |||||||

1. Per verificare lo stato di integrità della rete per l’ISE, vedere [Gestire l'ambiente del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Se la rete di ISE diventa non integra, il ambiente del servizio app interno (ASE) usato da ISE può diventare non integro. Se l'ambiente del servizio app non è integro per più di sette giorni, l'ambiente del servizio app viene sospeso. Per risolvere questo stato, controllare la configurazione della rete virtuale. Risolvere gli eventuali problemi riscontrati, quindi riavviare ISE. In caso contrario, dopo 90 giorni, l'ambiente del servizio app sospeso viene eliminato e l'ISE diventa inutilizzabile. Quindi, assicurarsi di lasciare l'ambiente ISE integro per consentire il traffico necessario.
   > 
   > Per altre informazioni, vedere gli argomenti seguenti:
   >
   > * [Panoramica della diagnostica del servizio app di Azure](../app-service/overview-diagnostics.md)
   > * [Registrazione dei messaggi per ambiente del servizio app di Azure](../app-service/environment/using-an-ase.md#logging)

1. Per iniziare a creare app per la logica e altri artefatti nell’ISE, vedere [Aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Dopo aver creato ISE, i connettori gestiti ISE diventano disponibili per l'uso, ma non vengono visualizzati automaticamente nella finestra di selezione del connettore nella finestra di progettazione dell'app per la logica. Prima di poter usare questi connettori ISE, è necessario [aggiungerli e distribuirli](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) manualmente in ISE, in modo che vengano visualizzati nella finestra di progettazione dell'app per la logica.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
