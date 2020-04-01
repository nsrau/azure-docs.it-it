---
title: Continuità aziendale e ripristino di emergenza
description: Progettare la strategia per proteggere i dati, eseguire rapidamente il ripristino da eventi dirompenti, ripristinare le risorse necessarie per le funzioni aziendali critiche e mantenere la continuità aziendale per le app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437708"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuità aziendale e ripristino di emergenza per le app per la logica di AzureBusiness continuity and disaster recovery for Azure Logic Apps

Per ridurre l'impatto e gli effetti che gli eventi imprevedibili hanno sull'azienda e sui clienti, assicurarsi di disporre di una soluzione di ripristino di [ *emergenza* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) in modo da poter proteggere i dati, ripristinare rapidamente le risorse che supportano le funzioni aziendali critiche e mantenere in esecuzione le operazioni per mantenere la [ *continuità aziendale* (BC).](https://en.wikipedia.org/wiki/Business_continuity_planning) Ad esempio, le interruzioni possono includere interruzioni, perdite nell'infrastruttura o nei componenti sottostanti, ad esempio risorse di archiviazione, di rete o di calcolo, errori delle applicazioni irreversibili o persino una perdita completa del data center. Con una soluzione di continuità aziendale e ripristino di emergenza (BCDR), l'azienda o l'organizzazione può rispondere più rapidamente alle interruzioni, pianificate o non pianificate e ridurre i tempi di inattività per i clienti.

Questo articolo fornisce indicazioni e strategie BCDR che è possibile applicare quando si creano flussi di lavoro automatizzati usando [App per la logica](../logic-apps/logic-apps-overview.md)di Azure.This article provides BCDR guidance and strategies that you can apply when you build automated workflows by using Azure Logic Apps . I flussi di lavoro delle app per la logica consentono di integrare e orchestrare più facilmente i dati tra app, servizi cloud e sistemi locali riducendo la quantità di codice da scrivere. Quando si pianifica BCDR, assicurarsi di prendere in considerazione non solo le app per la logica, ma anche queste risorse di Azure usate con le app per la logica:When you plan for BCDR, make sure that you consider not just your logic apps, but also these Azure resources that you use with your logic apps:

* [Connessioni](../connectors/apis-list.md) create da app per la logica ad altre app, servizi e sistemi. Per altre informazioni, vedere [Connessioni alle risorse](#resource-connections) più avanti in questo argomento.

* [Gateway dati locali,](../logic-apps/logic-apps-gateway-connection.md) ovvero risorse di Azure create e usate nelle app per la logica, per accedere ai dati nei sistemi locali. Ogni risorsa gateway rappresenta [un'installazione](../logic-apps/logic-apps-gateway-install.md) di gateway dati separata in un computer locale. Per altre informazioni, vedere Gateway dati locali più avanti in questo [argomento.](#on-premises-data-gateways)

* [L'integrazione tiene conto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) dei casi in cui è possibile definire e archiviare gli elementi utilizzati dalle app per la logica per scenari di [integrazione aziendale e aziendale (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Ad esempio, è possibile impostare il ripristino di [emergenza tra aree per gli account di integrazione.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* Ambienti del servizio di integrazione (ISE) in cui si creano app per la logica da eseguire in un'istanza di runtime di app per la logica isolata all'interno di una rete virtuale di [Azure.Integration service environments (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) where you create logic apps that run in an isolated Logic Apps runtime instance within an Azure virtual network. Queste app per la logica possono quindi accedere alle risorse protette dietro un firewall in tale rete virtuale.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Ubicazioni primarie e secondarie

Ogni app per la logica deve specificare il percorso che si vuole usare per la distribuzione. Questo percorso è un'area pubblica in Azure multi-tenant globale, ad esempio "Stati Uniti occidentali", o un ambiente del servizio di integrazione (ISE) creato e distribuito in una rete virtuale di Azure.This location is either o public region in global multi-tenant Azure, such as "West US", or an integration service environment (ISE) that you previously created and deployed into an Azure virtual network. L'esecuzione di app per la logica in un ISE è simile all'esecuzione di app per la logica in un'area di Azure globale, il che significa che la strategia di ripristino di emergenza può essere applicata a entrambi gli scenari. Tuttavia, le ISE presentano altre considerazioni, ad esempio la configurazione dell'accesso alle risorse disponibili solo per le ISE.

> [!NOTE]
> Se l'app per la logica funziona anche con elementi B2B, ad esempio partner commerciali, accordi, schemi, mappe e certificati, archiviati in un account di integrazione, sia l'account di integrazione che le app per la logica devono specificare lo stesso percorso.

Questa strategia di ripristino di emergenza è incentrata sulla configurazione dell'app per la logica principale per il failover in un'app di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure.This disaster recovery strategy focuses on setting up your primary logic app to [*failover*](https://en.wikipedia.org/wiki/Failover) onto a standby or backup logic app in an alternate location where Azure Logic Apps is also available. In questo modo, se il primario subisce perdite, interruzioni o guasti, il secondario può assumere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nella posizione alternativa.

Se si seguono procedure DevOps consigliate, si usano già i modelli di [Azure Resource Manager](../azure-resource-manager/management/overview.md) per definire e distribuire le app per la logica e le relative risorse dipendenti. I modelli di Resource Manager offrono la possibilità di usare una singola definizione di distribuzione e quindi di usare i file di parametri per fornire i valori di configurazione da usare per ogni destinazione di distribuzione. Questa funzionalità significa che è possibile distribuire la stessa app per la logica in ambienti diversi, ad esempio sviluppo, test e produzione. È anche possibile distribuire la stessa app per la logica in diverse aree di Azure o ISE, che supporta strategie di ripristino di emergenza che usano [aree accoppiate.](../best-practices-availability-paired-regions.md)

Per la strategia di failover, le app e le posizioni per la logica devono soddisfare i requisiti seguenti:For the failover strategy, your logic apps and locations must meet these requirements:

* L'istanza dell'app per la logica secondaria ha accesso alle stesse app, servizi e sistemi dell'istanza dell'app per la logica primaria.

* Entrambe le istanze dell'app per la logica hanno lo stesso tipo di host. Pertanto, entrambe le istanze vengono distribuite nelle aree in Azure multi-tenant globale oppure entrambe le istanze vengono distribuite in ISE, che consentono alle app per la logica di accedere direttamente alle risorse in una rete virtuale di Azure.So, either either instances are deployed to regions in global multi-tenant Azure, or both instances are deployed to ISEs, which let your logic apps directly access resources in an Azure virtual network. Per le procedure consigliate e altre informazioni sulle aree abbinate per BCDR, vedere Continuità aziendale e ripristino di [emergenza (BCDR): Aree abbinate](../best-practices-availability-paired-regions.md)di Azure.For best practices and more information about paired regions for BCDR, see Business continuity and disaster recovery (BCDR): Azure paired regions .

  Ad esempio, entrambe le posizioni primarie e secondarie devono essere ISE quando l'app per la logica primaria viene eseguita in un ISE e utilizza connettori con controllo delle versioni [DI ISE,](../connectors/apis-list.md#ise-connectors)azioni HTTP per chiamare le risorse nella rete virtuale di Azure o entrambi. In questo scenario, l'app per la logica secondaria deve avere anche un'installazione simile nella posizione secondaria come l'app per la logica primaria.

  > [!NOTE]
  > Per scenari più avanzati, è possibile combinare Azure multi-tenant e ISE come posizioni. Tuttavia, assicurarsi di considerare e comprendere le differenze tra il modo in cui [le app per la logica vengono eseguite in ISE e Azure multi-tenant.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Se si utilizzano ISE, [assicurarsi che siano scalati orizzontalmente o che dispongano](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) di capacità sufficiente per gestire il carico.

#### <a name="example-multi-tenant-azure"></a>Esempio: Azure multi-tenantExample: Multi-tenant Azure

Questo esempio mostra le istanze dell'app per la logica primaria e secondaria, che vengono distribuite in aree separate nell'Azure multi-tenant globale per questo scenario. Un singolo modello di [Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definisce sia le istanze dell'app per la logica che le risorse dipendenti richieste da tali app per la logica. I file di parametri separati specificano i valori di configurazione da utilizzare per ogni percorso di distribuzione:Separate parameter files specify the configuration values to use for each deployment location:

![Istanze dell'app per la logica primaria e secondaria in posizioni separate](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Esempio: ambiente del servizio di integrazioneExample: Integration service environment

Questo esempio mostra le istanze precedenti dell'app per la logica primaria e secondaria, ma vengono distribuite in ISE separate. Un singolo modello di Resource Manager definisce sia le istanze dell'app per la logica, le risorse dipendenti richieste da tali app per la logica e le ISE come percorsi di distribuzione. File di parametri separati definiscono i valori di configurazione da utilizzare per la distribuzione in ogni posizione:Separate parameter files define the configuration values to use for deployment in each location:

![App per la logica primaria e secondaria in posizioni diverse](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Connessioni alle risorse

App per la logica di Azure offre trigger e azioni predefiniti oltre a [centinaia di connettori gestiti](../connectors/apis-list.md) che l'app per la logica può usare per usare altre app, servizi, sistemi e altre risorse, ad esempio account di archiviazione di Azure, database di SQL Server, account di posta elettronica di Office 365 Outlook e così via. Se l'app per la logica deve accedere a queste risorse, si creano connessioni che autenticano l'accesso a tali risorse. Ogni connessione è una risorsa di Azure separata che esiste in una posizione specifica e non può essere usata dalle risorse in altre posizioni.

Per la strategia di ripristino di emergenza, prendi in considerazione le posizioni in cui sono presenti risorse dipendenti in relazione alle istanze dell'app per la logica:For your disaster recovery strategy, consider the locations where dependent resources exist relative to your logic app instances:

* L'istanza primaria e le risorse dipendenti si esistano in posizioni diverse. In questo caso, l'istanza secondaria può connettersi alle stesse risorse o endpoint dipendenti. Tuttavia, è consigliabile creare connessioni specifiche per l'istanza secondaria. In questo modo, se la posizione principale non è più disponibile, le connessioni del database secondario non vengono modificate.

  Si supponga, ad esempio, che l'app per la logica principale si connetta a un servizio esterno, ad esempio Salesforce.For example, suppose that your primary logic app connects to an external service such as Salesforce. In genere, la disponibilità e la posizione del servizio esterno sono indipendenti dalla disponibilità dell'app per la logica. In questo caso, l'istanza secondaria può connettersi allo stesso servizio, ma deve avere una propria connessione.

* Sia l'istanza primaria che le risorse dipendenti si trova nella stessa posizione. In questo caso, le risorse dipendenti devono avere backup o versioni replicate in un percorso diverso in modo che l'istanza secondaria possa comunque accedere a tali risorse.

  For example, suppose that your primary logic app connects to a service that's in the same location or region, for example, Azure SQL Database. Se l'intera area non è più disponibile, è probabile che anche il servizio database SQL di Azure in tale area non sia disponibile. In questo caso, si desidera che l'istanza secondaria utilizzi un database replicato o di backup insieme a una connessione separata a tale database.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateway dati locali

Se l'app per la logica viene eseguita in Azure multi-tenant e richiede l'accesso a risorse locali, ad esempio database di SQL Server, è necessario installare il [gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un computer locale. È quindi possibile creare una risorsa del gateway dati nel portale di Azure in modo che l'app per la logica possa usare il gateway quando si crea una connessione alla risorsa.

La risorsa del gateway dati è associata a una posizione o a un'area di Azure, proprio come la risorsa dell'app per la logica. Nella strategia di ripristino di emergenza verificare che il gateway dati rimanga disponibile per l'uso da parte dell'app per la logica. È possibile [abilitare la disponibilità elevata per il gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando si dispone di più installazioni gateway.

> [!NOTE]
> Se l'app per la logica viene eseguita in un ambiente del servizio di integrazione (ISE) e usa solo connettori con controllo delle versioni ISE per le origini dati locali, il gateway dati non è necessario perché i connettori ISE forniscono l'accesso diretto alla risorsa locale.
>
> Se non è disponibile alcun connettore con versione ISE per la risorsa locale desiderata, l'app per la logica può comunque creare la connessione usando un connettore non ISE, che viene eseguito nell'Azure multi-tenant globale, non in ISE. Tuttavia, questa connessione richiede il gateway dati locale.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Ruoli attivo-attivo e attivo-passivo

Puoi configurare le posizioni primarie e secondarie in modo che le istanze dell'app per la logica in queste posizioni possano svolgere questi ruoli:You can set up your primary and secondary locations so that your logic app instances in these locations can play these roles:

| Ruolo primario-secondario | Descrizione |
|------------------------|-------------|
| *Attivo-attivo* | Le istanze dell'app per la logica primaria e secondaria in entrambe le posizioni gestiscono attivamente le richieste seguendo uno di questi modelli:The primary and secondary logic app instances in both locations actively handle requests by following one of these patterns: <p><p>- *Bilanciamento del carico:* è possibile fare in modo che entrambe le istanze siano in ascolto di un endpoint e che il traffico di bilanciamento del carico sia dovuto a ogni istanza in base alle esigenze. <p>- *Consumatori concorrenti*: Entrambe le istanze fungono da consumer concorrenti in modo che le istanze competano per i messaggi da una coda. Se un'istanza ha esito negativo, l'altra istanza assume il carico di lavoro. |
| *Attivo-passivo* | L'istanza dell'app per la logica primaria gestisce attivamente l'intero carico di lavoro, mentre l'istanza secondaria è passiva (disabilitata o inattiva). Il database secondario attende un segnale che il database primario non è disponibile o non funziona a causa di un'interruzione o un errore e assume il carico di lavoro come istanza attiva. |
| Combinazione | Alcune app per la logica svolgono un ruolo attivo-attivo, mentre altre app per la logica svolgono un ruolo attivo-passivo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Esempi di attivo-attivo

Questi esempi mostrano la configurazione attivo-attivo in cui entrambe le istanze dell'app per la logica gestiscono attivamente richieste o messaggi. Un altro sistema o servizio distribuisce le richieste o i messaggi tra istanze, ad esempio una di queste opzioni:Some other system or service distributes the requests or messages between instances, for example, one of these options:

* Un servizio di bilanciamento del carico "fisico", ad esempio un componente hardware che instrada il traffico

* Un servizio di bilanciamento del carico "soft", ad esempio [Azure Load Balancer](../load-balancer/load-balancer-overview.md) o Azure API [Management](../api-management/api-management-key-concepts.md). Con Gestione API è possibile specificare criteri che determinano come bilanciare il carico del traffico in ingresso. In alternativa, è possibile usare un servizio che supporta il rilevamento dello stato, ad esempio Bus di servizio di Azure.Or, you can use a service that supports state tracking, for example, [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Anche se questo esempio mostra principalmente Azure Load Balancer, è possibile usare l'opzione più adatta alle esigenze dello scenario:Although this example primarily shows Azure Load Balancer, you can use the option that best suit s your scenario's needs:

  ![Configurazione "Attiva-attiva" che utilizza un servizio di bilanciamento del carico o con stato](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Ogni istanza dell'app per la logica funge da consumer e entrambe le istanze competono per i messaggi da una coda:Each logic app instance acts as a consumer and have both instances compete for messages from a queue:

  ![Configurazione "attiva-attiva" che utilizza "consumatori concorrenti"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Esempi di attivo-passivo

Questo esempio mostra l'impostazione attivo-passivo in cui l'istanza dell'app per la logica primaria è attiva in una posizione, mentre l'istanza secondaria rimane inattiva in un'altra posizione. Se il database primario causa un'interruzione o un errore, è possibile fare in modo che un operatore esegua uno script che attiva il database secondario per assumere il carico di lavoro.

![Configurazione "attivo-passivo" che utilizza "consumatori concorrenti"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinazione con attivo-attivo e attivo-passivo

Questo esempio mostra un'impostazione combinata in cui la posizione primaria include entrambe le istanze dell'app per la logica attiva, mentre la posizione secondaria include istanze dell'app per la logica attivo-passivo. Se la posizione primaria si verifica un'interruzione o un errore, l'app per la logica attiva nella posizione secondaria, che gestisce già un carico di lavoro parziale, può assumere l'intero carico di lavoro.

* Nella posizione primaria, un'app per la logica attiva rimane in ascolto di una coda del bus di servizio di Azure per i messaggi, mentre un'altra app per la logica attiva controlla i messaggi di posta elettronica usando un trigger di polling di Office 365 Outlook.In the primary location, an active logic app listens to an Azure Service Bus queue for messages, while another active logic app checks for emails by using a Office 365 Outlook polling trigger.

* Nella posizione secondaria, un'app per la logica attiva funziona con l'app per la logica nella posizione primaria ascoltando e competendo per i messaggi dalla stessa coda del bus di servizio. Nel frattempo, un'app per la logica inattiva passiva attende in standby per verificare la presenza di messaggi di posta elettronica quando la posizione principale non è più disponibile, ma è *disabilitata* per evitare di rileggere i messaggi di posta elettronica.

![Combinazione "attivo-passivo" e "attivo-passivo" che utilizza trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stato e cronologia dell'app per la logica

Quando l'app per la logica viene attivata e viene avviata l'esecuzione, lo stato dell'app viene archiviato nella stessa posizione in cui l'app è stata avviata e non è trasferibile in un'altra posizione. Se si verifica un errore o un'interruzione, tutte le istanze del flusso di lavoro in corso vengono abbandonate. Quando si dispone di una posizione primaria e secondaria impostata, le nuove istanze del flusso di lavoro iniziano a essere eseguite nella posizione secondaria.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Ridurre le istanze in corso abbandonate

Per ridurre al minimo il numero di istanze del flusso di lavoro in corso abbandonate, è possibile scegliere tra vari modelli di messaggio che è possibile implementare, ad esempio:To minimize the number of abandoned in-progress instances, you can choose from various message patterns that you can implement, for example:

* [Modello di documento di instradamento fisso](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Questo modello di messaggio aziendale che suddivide un processo di business in fasi più piccole. Per ogni fase, si configura un'app per la logica che gestisce il carico di lavoro per tale fase. Per comunicare tra loro, le app per la logica usano un protocollo di messaggistica asincrona, ad esempio le code o gli argomenti del bus di servizio di Azure.To communicate with each other, your logic apps use an asynchronous messaging protocol such as Azure Service Bus queues or topics. Quando si divide un processo in fasi più piccole, si riduce il numero di processi aziendali che potrebbero rimanere bloccati in un'istanza dell'app per la logica non riuscita. Per ulteriori informazioni generali su questo modello, vedere Modelli di [integrazione Enterprise - Distinta di instradamento](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Questo esempio mostra un modello di slittamento di routing in cui ogni app per la logica rappresenta una fase e usa una coda del bus di servizio per comunicare con l'app per la logica successiva nel processo.

  ![Suddividere un processo aziendale in fasi rappresentate da app per la logica, che comunicano tra loro tramite code del bus di servizio di AzureSudplan a business process into stages represented by logic apps, which communicate with each other by using Azure Service Bus queues](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se nelle rispettive posizioni entrambe le istanze dell'app per la logica primaria e secondaria seguono lo stesso modello di slittamento, è possibile implementare il [modello di consumer concorrenti](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) impostando [ruoli attivi-attivi](#roles) per tali istanze.

* [Modello di gestione processi (broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Blocco di visualizzazione senza modello di timeout](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Accesso alla cronologia di attivazione ed esecuzione

Per ottenere altre informazioni sulle esecuzioni precedenti del flusso di lavoro dell'app per la logica, è possibile esaminare la cronologia dei trigger e delle esecuzioni dell'app. La cronologia dell'app per la logica viene archiviata nella stessa posizione o area in cui è stata eseguita l'app per la logica, il che significa che non è possibile eseguire la migrazione di questa cronologia in un percorso diverso. Se l'istanza primaria esegue il failover a un'istanza secondaria, è possibile accedere solo al trigger di ogni istanza ed esegue la cronologia nelle rispettive posizioni in cui sono stati eseguiti tali istanze. Tuttavia, è possibile ottenere informazioni indipendenti dalla posizione sulla cronologia dell'app per la logica configurando le app per la logica per l'invio di eventi di diagnostica a un'area di lavoro di Azure Log Analytics.However, you can get location-agnostic information about your logic app's history by setting up your logic apps to send diagnostic events to an Azure Log Analytics workspace. È quindi possibile esaminare l'integrità e la cronologia tra le app per la logica eseguite in più posizioni.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Linee guida per i tipi di triggerTrigger type guidance

Il tipo di trigger usato nelle app per la logica determina le opzioni disponibili per la configurazione delle app per la logica tra le posizioni nella strategia di ripristino di emergenza. Ecco i tipi di trigger disponibili che puoi usare nelle app per la logica:

* [Trigger Recurrence](#recurrence-trigger)
* [Trigger di polling](#polling-trigger)
* [Trigger di richiesta](#request-trigger)
* [Trigger webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger Recurrence

Il trigger Ricorrenza è indipendente da qualsiasi servizio o endpoint specifico e viene attivato esclusivamente in base a una pianificazione specificata e ad nessun altro criterio, ad esempio:The **Recurrence** trigger is independent from any specific service or endpoint, and fires onely based a specified schedule and no other criteria, for example:

* Una frequenza e un intervallo fissi, ad esempio ogni 10 minuti
* Un programma più avanzato, ad esempio l'ultimo lunedì di ogni mese alle 17:00

Quando l'app per la logica viene avviata con un trigger Recurrence, è necessario configurare le istanze dell'app per la logica primaria e secondaria con i [ruoli attivo-passivo.](#roles) Per ridurre *l'obiettivo* del tempo di ripristino (RTO), che fa riferimento alla durata di destinazione per il ripristino di un processo di business dopo un'interruzione o un'emergenza, è possibile configurare le istanze dell'app per la logica con una combinazione di [ruoli attivo-passivo](#roles) e [ruoli attivi passivi.](#roles) In questa configurazione, si divide la pianificazione tra le ubicazioni.

Si supponga, ad esempio, di disporre di un'app per la logica che deve essere eseguita ogni 10 minuti. È possibile configurare le app e le posizioni per la logica in modo che, se la posizione primaria non è più disponibile, la posizione secondaria possa assumere il controllo del lavoro:You can set up your logic apps and locations so that if the primary location becomes unavailable, the secondary location can take over the work:

![Combinazione "attivo-passivo" e "passivo-attivo" che utilizza trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Nella posizione principale configurare [i ruoli attivo-passivo](#roles) per queste app per la logica:In the primary location, set up active-passive roles for these logic apps:

  * Per l'app per la logica abilitata *attiva,* impostare il trigger Ricorrenza in modo che inizi all'inizio dell'ora e che venga ripetuto ogni 20 minuti, ad esempio 9:00 AM, 9:20 AM e così via.

  * Per l'app per la logica disabilitata *passiva,* impostare il trigger Ricorrenza sulla stessa pianificazione, ma iniziare da 10 minuti dopo l'ora e ripetere ogni 20 minuti, ad esempio 9:10 AM, 9:30 AM e così via.

* Nella posizione secondaria configurare passive-active per queste app per la logica:In the secondary location, set up [passive-active](#roles) for these logic apps:

  * Per l'app per la logica disabilitata *passiva,* impostare il trigger Ricorrenza sulla stessa pianificazione dell'app per la logica attiva nella posizione primaria, che si trova all'inizio dell'ora e ripetere ogni 20 minuti, ad esempio 9:00 AM, 9:10 AM e così via.

  * Per l'app per la logica abilitata *attiva,* impostare il trigger Ricorrenza sulla stessa pianificazione dell'app per la logica passiva nella posizione primaria, che deve iniziare da 10 minuti dopo l'ora e ripetere ogni 20 minuti, ad esempio 9:10 AM, 9:20 AM e così via.

A questo punto, se si verifica un evento di disturbo nella posizione primaria, attivare l'app per la logica passiva nella posizione alternativa. In questo modo, se l'individuazione dell'errore richiede tempo, questa configurazione limita il numero di ricorrenze perse durante tale ritardo.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Trigger di polling

Per verificare regolarmente se i nuovi dati per l'elaborazione sono disponibili da un servizio o un endpoint specifico, l'app per la logica può usare un trigger di *polling* che chiama ripetutamente il servizio o l'endpoint in base a una pianificazione di ricorrenza fissa. I dati forniti dal servizio o dall'endpoint possono avere uno di questi tipi:The data that the service or endpoint provides can have either of these types:

* Dati statici, che descrivono i dati sempre disponibili per la lettura
* Dati volatili, che descrivono dati non più disponibili dopo la lettura

Per evitare di leggere ripetutamente gli stessi dati, l'app per la logica deve ricordare quali dati sono stati letti in precedenza mantenendo lo stato sul lato client o sul lato server, servizio o sistema.

* Le app per la logica che funzionano con lo stato lato client usano trigger che possono mantenere lo stato.

  Ad esempio, un trigger che legge un nuovo messaggio da una casella di posta elettronica richiede che il trigger possa ricordare il messaggio letto più di recente. In questo modo, il trigger avvia l'app per la logica solo quando arriva il messaggio non letto successivo.

* Le app per la logica che funzionano con lo stato del server, del servizio o del lato sistema usano valori di proprietà o impostazioni sul lato server, servizio o sistema.

  Ad esempio, un trigger basato su query che legge una `isRead` riga da un `FALSE`database richiede che la riga contè una colonna impostata su . Ogni volta che il trigger legge una riga, l'app `FALSE` per `TRUE`la logica aggiorna tale riga modificando la `isRead` colonna da a .

  Questo approccio sul lato server funziona in modo analogo per le code del bus di servizio o gli argomenti con semantica di accodamento in cui un trigger può leggere e bloccare un messaggio mentre l'app per la logica elabora il messaggio. Al termine dell'elaborazione dell'app per la logica, il trigger elimina il messaggio dalla coda o dall'argomento.

Dal punto di vista del ripristino di emergenza, quando configuri le istanze primarie e secondarie dell'app per la logica, assicurati di tenere traccia di questi comportamenti in base al fatto che l'app per la logica tenga traccia dello stato sul lato client o sul lato server:

* Per un'app per la logica che funziona con lo stato lato client, assicurati che l'app per la logica non legga lo stesso messaggio più di una volta. Solo una posizione può avere un'istanza dell'app per la logica attiva in un momento specifico. Assicurati che l'istanza dell'app per la logica nel percorso alternativo sia inattiva o disabilitata finché l'istanza primaria non esegue il failover nel percorso alternativo.

  Ad esempio, il trigger di Office 365 Outlook mantiene lo stato lato client e tiene traccia del timestamp per la posta elettronica letta più di recente per evitare la lettura di un duplicato.

* Per un'app per la logica che funziona con lo stato lato server, puoi configurare le istanze dell'app per la logica in modo che riseguano [ruoli attivo-attivo](#roles) in cui funzionano come consumer concorrenti o [ruoli passivi attivi](#roles) in cui l'istanza alternativa attende fino al failover dell'istanza primaria nel percorso alternativo.

  Ad esempio, la lettura da una coda di messaggi, ad esempio una coda del bus di servizio di Azure, utilizza lo stato lato server perché il servizio di accodamento gestisce i blocchi sui messaggi per impedire ad altri client di leggere gli stessi messaggi.

  > [!NOTE]
  > Se l'app per la logica deve leggere i messaggi in un ordine specifico, ad esempio da una coda del bus di servizio, è possibile usare il modello di consumer concorrente, ma solo se combinato con le sessioni del bus di servizio, noto anche come modello di [ *convoglio sequenziale.* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy) In caso contrario, devi configurare le istanze dell'app per la logica con i ruoli attivo-passivo.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger di richiesta

Il trigger **Di richiesta** rende l'app per la logica richiamabile da altre app, servizi e sistemi e viene in genere usato per fornire queste funzionalità:The Request trigger makes your logic app callable from other apps, services, and systems and is typically used to provide these capabilities:

* Un'API REST diretta per l'app per la logica che altri utenti possono chiamare

  Ad esempio, usare il trigger Di richiesta per avviare l'app per la logica in modo che altre app per la logica possano chiamare il trigger usando l'azione Chiama flusso di lavoro - App per la **logica.**

* Meccanismo [di webhook](#webhook-trigger) o callback per l'app per la logica

* Un modo che è possibile eseguire manualmente le operazioni o le routine dell'utente per chiamare l'app per la logica, ad esempio, usando uno script di PowerShell che esegue un'attività specifica

Dal punto di vista del ripristino di emergenza, il trigger di richiesta è un ricevitore passivo perché l'app per la logica non esegue alcuna operazione e attende fino a quando un altro servizio o sistema chiama in modo esplicito il trigger. Come endpoint passivo, è possibile configurare le istanze primarie e secondarie nei modi seguenti:As a passive endpoint, you can set up your primary and secondary instances in these ways:

* [Attivo-attivo:](#roles)entrambe le istanze gestiscono attivamente le richieste o le chiamate. Il chiamante o il router bilancia o distribuisce il traffico tra tali istanze.

* [Attivo-passivo:](#roles)solo l'istanza primaria è attiva e gestisce tutto il lavoro, mentre l'istanza secondaria attende fino a quando l'istanza primaria non si verifica un'interruzione o un errore. Il chiamante o il router determina quando chiamare l'istanza secondaria.

Come architettura consigliata, è possibile usare Gestione API di Azure come proxy per le app per la logica che usano i trigger di richiesta. Gestione API offre [resilienza interregionale incorporata e la possibilità di instradare il traffico tra più endpoint.](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Trigger di webhook

Un trigger *webhook* fornisce la possibilità per l'app per la logica di sottoscrivere un servizio passando un URL di *callback* a tale servizio. L'app per la logica può quindi restare in ascolto e attendere che si verifichi un evento specifico in tale endpoint del servizio. Quando si verifica l'evento, il servizio chiama il trigger webhook utilizzando l'URL di callback, che quindi esegue l'app per la logica. Se abilitato, il trigger webhook sottoscrive il servizio. Se disabilitato, il trigger annulla la sottoscrizione al servizio.

Dal punto di vista del ripristino di emergenza, configurare le istanze primarie e secondarie che usano trigger webhook per riprodurre ruoli attivo-passivo perché solo un'istanza deve ricevere eventi o messaggi dall'endpoint sottoscritto.

## <a name="assess-primary-instance-health"></a>Valutare l'integrità dell'istanza primaria

Affinché la strategia di ripristino di emergenza funzioni, la soluzione ha bisogno di modi per eseguire queste attività:For your disaster recovery strategy to work, your solution needs ways to perform these tasks:

* [Verificare la disponibilità dell'istanza primariaCheck the primary instance's availability](#check-primary-availability)
* [Monitorare l'integrità dell'istanza primariaMonitor the primary instance's health](#monitor-primary-health)
* [Attivare l'istanza secondaria](#activate-secondary)

In questa sezione viene descritta una soluzione che è possibile utilizzare a titolo definitivo o come base per il proprio progetto. Ecco una panoramica visiva di alto livello per questa soluzione:Here's a high-level visual overview for this solution:

![Creare un'app per la logica del cane visualizzato che esegue il monitoraggio di un'app per la logica di controllo di integrità nella posizione primariaCreate watchdog logic app that monitors a health-check logic app in the primary location](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Controllare la disponibilità dell'istanza primariaCheck primary instance availability

Per determinare se l'istanza primaria è disponibile, in esecuzione e in grado di funzionare, è possibile creare un'app per la logica di "controllo dell'integrità" che si trova nella stessa posizione dell'istanza primaria. È quindi possibile chiamare questa app di controllo dello stato da un percorso alternativo. Se l'app di controllo dell'integrità risponde correttamente, l'infrastruttura sottostante per il servizio App per la logica di Azure in tale area è disponibile e funzionante. Se l'app di controllo dello stato non risponde, è possibile presupporre che la posizione non sia più integro.

Per questa attività, creare un'app per la logica di controllo di integrità di base che esegue queste attività:For this task, create a basic health-check logic app that performs these tasks:

1. Riceve una chiamata dall'app watchdog utilizzando il trigger di richiesta.

1. Rispondere con uno stato che indica se l'app per la logica controllata funziona ancora tramite l'azione Risposta.

   > [!IMPORTANT]
   > L'app per la logica del controllo di integrità deve usare un'azione Response in modo che l'app risponda in modo sincrono e non asincrono.

1. Facoltativamente, per determinare ulteriormente se la posizione primaria è integra, è possibile prendere in considerazione l'integrità di altri servizi che interagiscono con l'app per la logica di destinazione in questa posizione. Basta espandere l'app per la logica di controllo dello stato per valutare l'integrità anche per questi altri servizi.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Creare un'app per la logica watchdogCreate a watchdog logic app

Per monitorare l'integrità dell'istanza primaria e chiamare l'app per la logica di controllo dell'integrità, creare un'app per la logica "watchdog" in un *percorso alternativo.* Ad esempio, è possibile configurare l'app per la logica watchdog in modo che se la chiamata alla logica di controllo dell'integrità non riesce, il watchdog può inviare un avviso al team operativo in modo che possano analizzare l'errore e il motivo per cui l'istanza primaria non risponde.

> [!IMPORTANT]
> Assicurarsi che l'app per la logica watchdog si trova in una *posizione diversa dalla posizione primaria.* Se il servizio App per la logica nella posizione principale si verificano problemi, l'app per la logica del watchdog potrebbe non essere eseguita.

Per questa attività, nella posizione secondaria, crea un'app per la logica del watchdog che esegue queste attività:For this task, in the secondary location, create a watchdog logic app that performs these tasks:

1. Eseguire in base a una ricorrenza fissa o pianificata utilizzando il trigger Ricorrenza.

   È possibile impostare la ricorrenza su un valore inferiore al livello di tolleranza per l'obiettivo del tempo di ripristino (RTO).

1. Chiamare l'app per la logica del controllo di integrità nella posizione primaria usando l'azione HTTP, ad esempio:Call the health-check logic app in the primary location by using the HTTP action, for example:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Attivare l'istanza secondaria

Per attivare automaticamente l'istanza secondaria, è possibile creare un'app per la logica che chiama l'API di gestione, ad esempio il connettore di [Azure Resource Manager,](https://docs.microsoft.com/connectors/arm/) per attivare le app per la logica appropriate nella posizione secondaria. È possibile espandere l'app watchdog per chiamare questa app per la logica di attivazione dopo che si è verificato un numero specifico di errori.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Raccogliere dati di diagnostica

È possibile configurare la registrazione per l'esecuzione dell'app per la logica e inviare i dati di diagnostica risultanti a servizi quali Archiviazione di Azure, Hub eventi di Azure e Analisi dei log di Azure per un'ulteriore gestione e l'elaborazione.

* Se si vuole usare questi dati con Azure Log Analytics, è possibile rendere i dati disponibili per le posizioni primaria e secondaria configurando **le impostazioni di diagnostica** dell'app per la logica e inviando i dati a più aree di lavoro di Log Analytics.If you want to use this data with Azure Log Analytics, you can make the data available for both the primary and secondary locations by setting up your logic app's Diagnostic settings and sending the data to multiple Log Analytics workspaces. Per altre informazioni, vedere Configurare i log di Monitoraggio di Azure e raccogliere dati di diagnostica per le app per la logica di Azure.For more information, see [Set up Azure Monitor logs and collect diagnostics data for Azure Logic Apps.](../logic-apps/monitor-logic-apps-log-analytics.md)

* Se si desidera inviare i dati ad Archiviazione di Azure o hub eventi di Azure, è possibile rendere i dati disponibili per le posizioni primaria e secondaria configurando la ridondanza geografica. Per altre informazioni, vedere questi articoli:<p>

  * [Ripristino di emergenza di Archiviazione BLOB di Azure e failover dell'accountAzure Blob Storage disaster recovery and account failover](../storage/common/storage-disaster-recovery-guidance.md)
  * [Ripristino di emergenza geografico degli hub di Azure AzureAzure Event Hubs geo-disaster recovery](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della resilienza per AzureResiliency overview for Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Elenco di controllo per la resilienza per servizi di Azure specifici](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Gestione dei dati per la resilienza in AzureData management for resiliency in Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Backup e ripristino di emergenza per le applicazioni di AzureBackup and disaster recovery for Azure applications](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Ripristino da un'interruzione del servizio a livello di areaRecover from a region-wide service disruption](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Contratti di servizio Microsoft per i servizi di AzureMicrosoft Service Level Agreements (SLa) for Azure services](https://azure.microsoft.com/support/legal/sla/)
