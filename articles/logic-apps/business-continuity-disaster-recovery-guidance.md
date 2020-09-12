---
title: Continuità aziendale e ripristino di emergenza
description: Progetta la tua strategia per proteggere i dati, recupera rapidamente da eventi di disturbo, ripristina le risorse richieste dalle funzioni aziendali critiche e Mantieni la continuità aziendale per le app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658196"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuità aziendale e ripristino di emergenza per app per la logica di Azure

Per ridurre l'impatto e gli effetti che gli eventi imprevisti hanno sull'azienda e sui clienti, assicurarsi di disporre di una soluzione di [ *ripristino di emergenza* ](https://en.wikipedia.org/wiki/Disaster_recovery) per poter proteggere i dati, ripristinare rapidamente le risorse che supportano le funzioni aziendali critiche e mantenere le operazioni in esecuzione per mantenere la [ *continuità aziendale* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Ad esempio, le interruzioni possono includere interruzioni, perdite nell'infrastruttura o nei componenti sottostanti, ad esempio risorse di archiviazione, rete o calcolo, errori di applicazioni irreversibili o addirittura una perdita di dati completa. Grazie alla disponibilità di una soluzione di continuità aziendale e ripristino di emergenza (BCDR), l'azienda o l'organizzazione può rispondere più rapidamente a interruzioni, pianificate o non pianificate e ridurre i tempi di inattività per i clienti.

Questo articolo fornisce indicazioni e strategie BCDR che è possibile applicare quando si compilano flussi di lavoro automatizzati usando app per la [logica di Azure](../logic-apps/logic-apps-overview.md). I flussi di lavoro delle app per la logica consentono di integrare e orchestrare più facilmente i dati tra app, servizi cloud e sistemi locali riducendo la quantità di codice da scrivere. Quando si pianifica BCDR, assicurarsi di considerare non solo le app per la logica, ma anche le risorse di Azure usate con le app per la logica:

* [Connessioni](../connectors/apis-list.md) create da app per la logica ad altre app, servizi e sistemi. Per ulteriori informazioni, vedere [connessioni alle risorse](#resource-connections) più avanti in questo argomento.

* [Gateway dati locali](../logic-apps/logic-apps-gateway-connection.md) che sono risorse di Azure che è possibile creare e usare nelle app per la logica per accedere ai dati nei sistemi locali. Ogni risorsa del gateway rappresenta un' [installazione separata del gateway dati](../logic-apps/logic-apps-gateway-install.md) in un computer locale. Per ulteriori informazioni, vedere [gateway dati locali](#on-premises-data-gateways) più avanti in questo argomento.

* [Account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) in cui è possibile definire e archiviare gli elementi utilizzati dalle app per la logica per gli scenari di [integrazione aziendale B2B (business to business)](../logic-apps/logic-apps-enterprise-integration-overview.md) . Ad esempio, è possibile [configurare il ripristino di emergenza tra aree per gli account di integrazione](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md).

* [Ambienti del servizio di integrazione (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in cui è possibile creare app per la logica eseguite in un'istanza di runtime di app per la logica isolata in una rete virtuale di Azure. Queste app per la logica possono quindi accedere alle risorse protette da un firewall nella rete virtuale.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Posizioni primarie e secondarie

Ogni app per la logica deve specificare il percorso che si vuole usare per la distribuzione. Questo percorso è un'area pubblica in Azure multi-tenant globale, ad esempio "Stati Uniti occidentali" o un ambiente di Integration Services (ISE) creato in precedenza e distribuito in una rete virtuale di Azure. L'esecuzione di app per la logica in ISE è simile all'esecuzione di app per la logica in un'area globale di Azure, il che significa che la strategia di ripristino di emergenza può essere applicata a entrambi gli scenari. Tuttavia, ISEs hanno altre considerazioni, ad esempio la configurazione dell'accesso alle risorse disponibili solo per ISEs.

> [!NOTE]
> Se l'app per la logica funziona anche con gli artefatti B2B, quali partner commerciali, contratti, schemi, mappe e certificati, archiviati in un account di integrazione, l'account di integrazione e le app per la logica devono specificare lo stesso percorso.

Questa strategia di ripristino di emergenza è incentrata sulla configurazione dell'app per la logica primaria per il [*failover*](https://en.wikipedia.org/wiki/Failover) in un'app per la logica di standby o di backup in un percorso alternativo in cui è disponibile anche app per la logica di Azure. In questo modo, se il database primario soffre di perdite, interruzioni o errori, il database secondario può intraprendere il lavoro. Questa strategia richiede che l'app per la logica secondaria e le risorse dipendenti siano già distribuite e pronte nel percorso alternativo.

Se si seguono buone procedure di DevOps, è già possibile usare [Azure Resource Manager modelli](../azure-resource-manager/management/overview.md) per definire e distribuire le app per la logica e le relative risorse dipendenti. I modelli Gestione risorse offrono la possibilità di usare una singola definizione di distribuzione e quindi di usare i file di parametri per specificare i valori di configurazione da usare per ogni destinazione di distribuzione. Questa funzionalità significa che è possibile distribuire la stessa app per la logica in ambienti diversi, ad esempio sviluppo, test e produzione. È anche possibile distribuire la stessa app per la logica in diverse aree di Azure o ISEs, che supporta strategie di ripristino di emergenza che usano [aree abbinate](../best-practices-availability-paired-regions.md).

Per la strategia di failover, le app per la logica e i percorsi devono soddisfare i requisiti seguenti:

* L'istanza dell'app per la logica secondaria può accedere alle stesse app, servizi e sistemi dell'istanza primaria dell'app per la logica.

* Entrambe le istanze dell'app per la logica hanno lo stesso tipo di host. Quindi, entrambe le istanze vengono distribuite nelle aree di Azure multi-tenant globale oppure entrambe le istanze vengono distribuite in ISEs, che consentono alle app per la logica di accedere direttamente alle risorse in una rete virtuale di Azure. Per le procedure consigliate e altre informazioni sulle aree abbinate per BCDR, vedere [continuità aziendale e ripristino di emergenza (BCdR): aree abbinate di Azure](../best-practices-availability-paired-regions.md).

  Ad esempio, i percorsi primario e secondario devono essere ISEs quando l'app per la logica primaria viene eseguita in ISE e USA [connettori con versione ISE](../connectors/apis-list.md#ise-connectors), azioni http per chiamare risorse nella rete virtuale di Azure o entrambi. In questo scenario l'app per la logica secondaria deve avere anche una configurazione simile nella posizione secondaria come app per la logica primaria.

  > [!NOTE]
  > Per scenari più avanzati, è possibile combinare sia Azure multi-tenant che un ISE come località. Tuttavia, assicurarsi di prendere in considerazione le [differenze tra le modalità di esecuzione delle app per la logica in un host ISE rispetto a Azure multi-tenant](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

* Se si usa ISEs, [assicurarsi che siano scalati orizzontalmente o dispongano di capacità sufficiente](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) per gestire il carico.

#### <a name="example-multi-tenant-azure"></a>Esempio: Azure multi-tenant

Questo esempio mostra le istanze di app per la logica primaria e secondaria, che vengono distribuite in aree separate nell'Azure multi-tenant globale per questo scenario. Un singolo [modello di gestione risorse](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) definisce sia le istanze dell'app per la logica che le risorse dipendenti richieste da tali app per la logica. File di parametri separati specificare i valori di configurazione da usare per ogni percorso di distribuzione:

![Istanze dell'app per la logica primaria e secondaria in posizioni separate](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Esempio: ambiente del servizio di integrazione

Questo esempio mostra le istanze di app per la logica primaria e secondaria precedenti, ma distribuite in ISEs separate. Un singolo modello di Gestione risorse definisce entrambe le istanze dell'app per la logica, le risorse dipendenti richieste da tali app per la logica e ISEs come percorsi di distribuzione. I file di parametri separati definiscono i valori di configurazione da usare per la distribuzione in ogni posizione:

![App per la logica primaria e secondaria in posizioni diverse](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Connessioni alle risorse

App per la logica di Azure offre [trigger e azioni predefiniti, oltre a centinaia di connettori gestiti](../connectors/apis-list.md) che possono essere usati dall'app per la logica per lavorare con altre app, servizi, sistemi e altre risorse, ad esempio account di archiviazione di Azure, database SQL Server, account di posta elettronica aziendali o dell'Istituto di istruzione e così via. Se l'app per la logica necessita dell'accesso a queste risorse, si creano connessioni che autenticano l'accesso a queste risorse. Ogni connessione è una risorsa di Azure separata presente in una posizione specifica e non può essere usata da risorse in altre posizioni.

Per la strategia di ripristino di emergenza, prendere in considerazione le posizioni in cui sono presenti risorse dipendenti rispetto alle istanze dell'app per la logica:

* L'istanza primaria e le risorse dipendenti si trovano in posizioni diverse. In questo caso, l'istanza secondaria può connettersi alle stesse risorse dipendenti o agli stessi endpoint. Tuttavia, è necessario creare connessioni specifiche per l'istanza secondaria. In questo modo, se la località primaria non è più disponibile, le connessioni secondarie non saranno interessate.

  Si supponga, ad esempio, che l'app per la logica primaria si connetta a un servizio esterno, ad esempio Salesforce. In genere, la disponibilità e la posizione del servizio esterno sono indipendenti dalla disponibilità dell'app per la logica. In questo caso, l'istanza secondaria può connettersi allo stesso servizio, ma deve avere una propria connessione.

* Sia l'istanza primaria che le risorse dipendenti si trovano nello stesso percorso. In questo caso, le risorse dipendenti devono avere backup o versioni replicate in un percorso diverso, in modo che l'istanza secondaria possa ancora accedere a tali risorse.

  Si supponga, ad esempio, che l'app per la logica primaria si connetta a un servizio che si trova nella stessa località o area, ad esempio il database SQL di Azure. Se l'intera area non è più disponibile, anche il servizio database SQL di Azure in tale area è probabilmente non disponibile. In questo caso, è necessario che l'istanza secondaria usi un database replicato o di backup insieme a una connessione separata a tale database.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateway dati locali

Se l'app per la logica è in esecuzione in Azure multi-tenant e necessita dell'accesso alle risorse locali, ad esempio SQL Server database, è necessario installare il [gateway dati](../logic-apps/logic-apps-gateway-install.md) locale in un computer locale. È quindi possibile creare una risorsa del gateway dati nel portale di Azure in modo che l'app per la logica possa usare il gateway quando si crea una connessione alla risorsa.

La risorsa del gateway dati è associata a una località o a un'area di Azure, proprio come la risorsa dell'app per la logica. Nella strategia di ripristino di emergenza, verificare che il gateway dati rimanga disponibile per l'uso da parte dell'app per la logica. È possibile [abilitare la disponibilità elevata per il gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando si hanno più installazioni del gateway.

> [!NOTE]
> Se l'app per la logica è in esecuzione in un ambiente Integration Services (ISE) e usa solo connettori con versione ISE per le origini dati locali, non è necessario il gateway dati perché i connettori ISE forniscono accesso diretto alla risorsa locale.
>
> Se non è disponibile alcun connettore con versione ISE per la risorsa locale desiderata, l'app per la logica può comunque creare la connessione usando un connettore non ISE, che viene eseguito nell'istanza globale multi-tenant di Azure, non in ISE. Questa connessione richiede tuttavia il gateway dati locale.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Ruoli attivo-attivo o attivo-passivo

È possibile configurare le posizioni primarie e secondarie in modo che le istanze dell'app per la logica in questi percorsi siano in grado di riprodurre i ruoli seguenti:

| Primario-ruolo secondario | Descrizione |
|------------------------|-------------|
| *Modalità attiva-attiva* | Le istanze dell'app per la logica primaria e secondaria in entrambe le posizioni gestiscono attivamente le richieste seguendo uno di questi modelli: <p><p>- *Bilanciamento del carico*: è possibile fare in modo che entrambe le istanze attendano un endpoint e bilanciare il carico del traffico in ogni istanza, se necessario. <p>- *Consumer concorrenti*: è possibile fare in modo che entrambe le istanze fungano da consumer concorrenti, in modo che le istanze concorrano per i messaggi da una coda. Se un'istanza ha esito negativo, l'altra istanza assume il carico di lavoro. |
| *Modalità attiva-passiva* | L'istanza dell'app per la logica primaria gestisce attivamente l'intero carico di lavoro, mentre l'istanza secondaria è passiva (disabilitata o inattiva). Il database secondario attende un segnale che il database primario non è disponibile o non funziona a causa di interruzioni o errori e assume il carico di lavoro come istanza attiva. |
| Combinazione | Alcune app per la logica svolgono un ruolo attivo-attivo, mentre altre app per la logica svolgono un ruolo attivo-passivo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Esempi di Active-Active

Questi esempi illustrano il programma di installazione attivo-attivo in cui entrambe le istanze dell'app per la logica gestiscono attivamente richieste o messaggi. Un altro sistema o servizio distribuisce le richieste o i messaggi tra le istanze, ad esempio, una di queste opzioni:

* Un servizio di bilanciamento del carico "fisico", ad esempio un componente hardware che instrada il traffico

* Un servizio di bilanciamento del carico "soft", ad esempio [Azure Load Balancer](../load-balancer/load-balancer-overview.md) o [gestione API di Azure](../api-management/api-management-key-concepts.md). Con gestione API è possibile specificare i criteri che determinano come bilanciare il carico del traffico in ingresso. In alternativa, è possibile usare un servizio che supporta il rilevamento dello stato, ad esempio il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md).

  Sebbene questo esempio mostri principalmente Azure Load Balancer, è possibile usare l'opzione più adatta alle esigenze dello scenario:

  ![Installazione "attivo-attivo" che usa un servizio di bilanciamento del carico o un servizio con stato](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Ogni istanza dell'app per la logica funge da consumer e entrambe le istanze competono per i messaggi da una coda:

  ![Installazione "attivo-attivo" che usa "consumer concorrente"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Esempi di attivo-passivo

Questo esempio mostra il programma di installazione attivo-passivo in cui l'istanza dell'app per la logica primaria è attiva in un'unica posizione, mentre l'istanza secondaria rimane inattiva in un'altra posizione. Se si verificano interruzioni o errori nel database primario, è possibile fare in modo che un operatore esegua uno script che attiva il database secondario per l'esecuzione del carico di lavoro.

![Installazione "attiva-passiva" che usa "consumer concorrente"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinazione con Active-Active e attivo-passivo

Questo esempio mostra una configurazione combinata in cui la posizione primaria include entrambe le istanze di app per la logica attive, mentre la località secondaria dispone di istanze di app per la logica attive/passive. Se la posizione primaria riscontra un'interruzione o un errore, l'app per la logica attiva nella posizione secondaria, che sta già gestendo un carico di lavoro parziale, può assumere l'intero carico di lavoro.

* Nel percorso primario, un'app per la logica attiva è in ascolto di una coda del bus di servizio di Azure per i messaggi, mentre un'altra app per la logica attiva controlla i messaggi di posta elettronica usando un trigger di polling di Office 365 Outlook.

* Nel percorso secondario, un'app per la logica attiva usa l'app per la logica nella posizione primaria tramite ascolto e competizione per i messaggi provenienti dalla stessa coda del bus di servizio. Nel frattempo, un'app per la logica inattiva passiva resta in attesa in standby per verificare la presenza di messaggi di posta elettronica quando la posizione primaria non è disponibile, ma è *disabilitata* per evitare di rileggere i messaggi di

![Combinazione "attiva-passiva" e "attiva-passiva" che usa i trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Stato e cronologia dell'app per la logica

Quando l'app per la logica viene attivata e viene avviata l'esecuzione, lo stato dell'app viene archiviato nella stessa posizione in cui l'app è stata avviata e non è trasferibile in un'altra posizione. Se si verifica un errore o un'interruzione, tutte le istanze del flusso di lavoro in corso verranno abbandonate. Quando si dispone di una posizione primaria e secondaria configurata, le nuove istanze del flusso di lavoro iniziano a essere eseguite nella posizione secondaria.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Ridurre le istanze in corso abbandonate

Per ridurre al minimo il numero di istanze del flusso di lavoro in corso abbandonate, è possibile scegliere tra diversi modelli di messaggio che è possibile implementare, ad esempio:

* [Modello di slittamento di routing fisso](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Questo modello di messaggio aziendale che suddivide un processo di business in fasi più piccole. Per ogni fase viene configurata un'app per la logica che gestisce il carico di lavoro per la fase. Per comunicare tra loro, le app per la logica usano un protocollo di messaggistica asincrono, ad esempio code o argomenti del bus di servizio di Azure. Quando si divide un processo in fasi più piccole, si riduce il numero di processi aziendali che potrebbero rimanere bloccati in un'istanza dell'app per la logica non riuscita. Per informazioni più generali su questo modello, vedere [modelli di integrazione aziendale-routing Slip](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Questo esempio illustra un modello di scorrimento di routing in cui ogni app per la logica rappresenta una fase e usa una coda del bus di servizio per comunicare con l'app per la logica successiva nel processo.

  ![Suddividere un processo di business in fasi rappresentate da app per la logica, che comunicano tra loro usando le code del bus di servizio di Azure](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se le istanze di app per la logica primaria e secondaria seguono lo stesso modello di instradamento di routing nelle rispettive posizioni, è possibile implementare il [modello di consumer concorrente](/azure/architecture/patterns/competing-consumers) impostando i [ruoli attivo-attivo](#roles) per tali istanze.

* [Modello di gestione processi (Broker)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Visualizzazione del blocco senza modello di timeout](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Accesso alla cronologia di trigger ed esecuzioni

Per ottenere altre informazioni sulle esecuzioni passate del flusso di lavoro dell'app per la logica, è possibile esaminare il trigger e la cronologia delle esecuzioni dell'app. La cronologia di esecuzione di una cronologia dell'app per la logica viene archiviata nella stessa posizione o area in cui è stata eseguita l'app per la logica. non è quindi possibile eseguire la migrazione della cronologia in un percorso diverso. Se viene eseguito il failover dell'istanza primaria a un'istanza secondaria, è possibile accedere solo al trigger di ogni istanza ed eseguire la cronologia nelle rispettive posizioni in cui sono state eseguite tali istanze. Tuttavia, è possibile ottenere informazioni indipendenti dalla posizione sulla cronologia dell'app per la logica configurando le app per la logica per inviare eventi di diagnostica a un'area di lavoro di Azure Log Analytics. È quindi possibile esaminare l'integrità e la cronologia tra app per la logica eseguite in più posizioni.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Linee guida per il tipo di trigger

Il tipo di trigger usato nelle app per la logica determina le opzioni per la configurazione delle app per la logica nelle diverse posizioni della strategia di ripristino di emergenza. Ecco i tipi di trigger disponibili che è possibile usare nelle app per la logica:

* [Trigger Recurrence](#recurrence-trigger)
* [Trigger di polling](#polling-trigger)
* [Trigger di richiesta](#request-trigger)
* [Trigger webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Trigger Recurrence

Il trigger di **ricorrenza** è indipendente da qualsiasi servizio o endpoint specifico e viene attivato solo in base a una pianificazione specificata e a nessun altro criterio, ad esempio:

* Frequenza e intervallo fisse, ad esempio ogni 10 minuti
* Una pianificazione più avanzata, ad esempio l'ultimo lunedì di ogni mese alle 5:00 PM

Quando l'app per la logica inizia con un trigger di ricorrenza, è necessario configurare le istanze dell'app per la logica primaria e secondaria con i [ruoli attivo-passivo](#roles). Per ridurre l' *obiettivo del tempo di ripristino* (RTO), che fa riferimento alla durata di destinazione per il ripristino di un processo di business dopo un'emergenza o un'emergenza, è possibile configurare le istanze dell'app per la logica con una combinazione di [ruoli attivi e passivi](#roles) e di [ruoli attivi passivi](#roles). In questa configurazione si suddivide la pianificazione tra i percorsi.

Si supponga, ad esempio, di avere un'app per la logica che deve essere eseguita ogni 10 minuti. È possibile configurare le app per la logica e le località in modo che se il percorso primario non è più disponibile, la posizione secondaria può assumere il lavoro:

![Combinazione di "attivo-passivo" e "passiva-attiva" che usa i trigger di ricorrenza](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Nella posizione primaria impostare i [ruoli attivi-passivi](#roles) per queste app per la logica:

  * Per l'app per la logica *attiva* abilitata, impostare il trigger di ricorrenza per iniziare all'inizio dell'ora e ripetere ogni 20 minuti, ad esempio, 9:00 am, 9:20 AM e così via.

  * Per l'app per la logica *passiva* disabilitata, impostare il trigger di ricorrenza sulla stessa pianificazione, ma iniziare a 10 minuti dopo l'ora e ripetere ogni 20 minuti, ad esempio, 9:10 am, 9:30 AM e così via.

* Nella posizione secondaria configurare [passive-Active](#roles) per queste app per la logica:

  * Per l'app per la logica *passiva* disabilitata, impostare il trigger di ricorrenza sulla stessa pianificazione dell'app per la logica attiva nella posizione primaria, che si trova all'inizio dell'ora e si ripete ogni 20 minuti, ad esempio, 9:00 am, 9:10 AM e così via.

  * Per l'app per la logica *attiva* abilitata, impostare il trigger di ricorrenza sulla stessa pianificazione dell'app per la logica passiva nella posizione primaria, che deve iniziare da 10 minuti dopo l'ora e ripetere ogni 20 minuti, ad esempio, 9:10 am, 9:20 AM e così via.

A questo punto, se si verifica un evento di interruzione nella posizione primaria, attivare l'app per la logica passiva nel percorso alternativo. In questo modo, se la ricerca dell'errore richiede tempo, questo programma di installazione limita il numero di ricorrenze mancanti durante tale ritardo.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Trigger di polling

Per controllare regolarmente se i nuovi dati per l'elaborazione sono disponibili da un servizio o un endpoint specifico, l'app per la logica può usare un trigger di *polling* che chiama ripetutamente il servizio o l'endpoint in base a una pianificazione di ricorrenza fissa. I dati forniti dal servizio o dall'endpoint possono avere uno dei seguenti tipi:

* Dati statici, che descrivono i dati che sono sempre disponibili per la lettura
* Dati volatili, che descrivono i dati che non sono più disponibili dopo la lettura

Per evitare di leggere ripetutamente gli stessi dati, l'app per la logica deve ricordare quali dati sono stati letti in precedenza gestendo lo stato sul lato client o sul lato server, servizio o sistema.

* App per la logica che funzionano con lo stato lato client usano trigger che possono mantenere lo stato.

  Ad esempio, un trigger che legge un nuovo messaggio da una posta in arrivo richiede che il trigger possa ricordare il messaggio di lettura più recente. In questo modo, il trigger avvia l'app per la logica solo quando arriva il successivo messaggio non letto.

* Le app per la logica che funzionano con il server, il servizio o lo stato sul lato sistema utilizzano i valori delle proprietà o le impostazioni che si trovano sul lato server, servizio o sistema.

  Un trigger basato su query che legge una riga da un database, ad esempio, richiede che la riga includa una `isRead` colonna impostata su `FALSE` . Ogni volta che il trigger legge una riga, l'app per la logica aggiorna la riga cambiando la `isRead` colonna da `FALSE` a `TRUE` .

  Questo approccio sul lato server funziona in modo analogo per le code o gli argomenti del bus di servizio che hanno una semantica di Accodamento in cui un trigger può leggere e bloccare un messaggio mentre l'app per la logica elabora il messaggio. Al termine dell'elaborazione dell'app per la logica, il trigger Elimina il messaggio dalla coda o dall'argomento.

Dal punto di vista del ripristino di emergenza, quando si configurano le istanze primarie e secondarie dell'app per la logica, assicurarsi di tenere conto di questi comportamenti a seconda che l'app per la logica tenga traccia dello stato sul lato client o sul lato server:

* Per un'app per la logica che funziona con lo stato lato client, assicurarsi che l'app per la logica non legga lo stesso messaggio più di una volta. Una sola posizione può avere un'istanza di app per la logica attiva in un momento specifico. Verificare che l'istanza dell'app per la logica nel percorso alternativo sia inattiva o disabilitata fino a quando non viene eseguito il failover dell'istanza primaria nel percorso alternativo.

  Ad esempio, il trigger Outlook per Office 365 mantiene lo stato lato client e rileva il timestamp per l'ultimo messaggio di posta elettronica di lettura per evitare la lettura di un duplicato.

* Per un'app per la logica che funziona con lo stato sul lato server, è possibile configurare le istanze dell'app per la logica in modo che riproducano i [ruoli attivo-attivo](#roles) dove funzionano come consumer concorrente o [ruoli attivi-passivi](#roles) in cui l'istanza alternativa resta in attesa fino al failover dell'istanza primaria nel percorso alternativo.

  Ad esempio, la lettura da una coda di messaggi, ad esempio una coda del bus di servizio di Azure, USA lo stato lato server perché il servizio di Accodamento mantiene i blocchi sui messaggi per evitare che altri client leggano gli stessi messaggi.

  > [!NOTE]
  > Se l'app per la logica deve leggere i messaggi in un ordine specifico, ad esempio da una coda del bus di servizio, è possibile usare il modello di consumer concorrente ma solo se combinato con le sessioni del bus di servizio, noto anche come modello di serie di istruzioni [ *sequenziali* ](/azure/architecture/patterns/sequential-convoy). In caso contrario, è necessario configurare le istanze dell'app per la logica con i ruoli attivo-passivo.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Trigger di richiesta

Il trigger di **richiesta** rende l'app per la logica richiamabile da altre app, servizi e sistemi e viene in genere usata per fornire queste funzionalità:

* API REST diretta per l'app per la logica che altri utenti possono chiamare

  Usare, ad esempio, il trigger Request per avviare l'app per la logica in modo che altre app per la logica possano chiamare il trigger usando l'azione **chiama flusso di lavoro-app** per la logica.

* Un [webhook](#webhook-trigger) o un meccanismo di callback per l'app per la logica

* Un modo per eseguire manualmente operazioni utente o routine per chiamare l'app per la logica, ad esempio, usando uno script di PowerShell che esegue un'attività specifica

Dal punto di vista del ripristino di emergenza, il trigger di richiesta è un ricevitore passivo perché l'app per la logica non esegue alcuna operazione e attende fino a quando un altro servizio o sistema non chiama in modo esplicito il trigger. Come endpoint passivo, è possibile configurare le istanze primarie e secondarie nei modi seguenti:

* [Attivo-attivo](#roles): entrambe le istanze gestiscono attivamente le richieste o le chiamate. Il chiamante o il router bilancia o distribuisce il traffico tra le istanze.

* [Attivo-passivo](#roles): solo l'istanza primaria è attiva e gestisce tutto il lavoro, mentre l'istanza secondaria attende fino a quando non si verificano interruzioni o errori delle esperienze primarie. Il chiamante o il router determina quando chiamare l'istanza secondaria.

Come architettura consigliata, è possibile usare gestione API di Azure come proxy per le app per la logica che usano trigger di richiesta. Gestione API offre [resilienza incorporata tra più aree e la capacità di instradare il traffico tra più endpoint](../api-management/api-management-howto-deploy-multi-region.md).

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Trigger di webhook

Un trigger *webhook* offre la possibilità per l'app per la logica di sottoscrivere un servizio passando un *URL di callback* a tale servizio. L'app per la logica può quindi restare in ascolto e attendere che si verifichi un evento specifico in tale endpoint di servizio. Quando si verifica l'evento, il servizio chiama il trigger webhook usando l'URL di callback, che quindi esegue l'app per la logica. Se abilitata, il trigger webhook sottoscrive il servizio. Quando è disabilitato, il trigger Annulla la sottoscrizione al servizio.

Dal punto di vista del ripristino di emergenza, configurare istanze primarie e secondarie che usano trigger webhook per riprodurre ruoli attivi e passivi perché solo un'istanza deve ricevere eventi o messaggi dall'endpoint sottoscritto.

## <a name="assess-primary-instance-health"></a>Valutare l'integrità dell'istanza primaria

Per il corretto funzionamento della strategia di ripristino di emergenza, la soluzione necessita di modi per eseguire queste attività:

* [Verificare la disponibilità dell'istanza primaria](#check-primary-availability)
* [Monitorare l'integrità dell'istanza primaria](#monitor-primary-health)
* [Attivare l'istanza secondaria](#activate-secondary)

Questa sezione descrive una soluzione che è possibile usare in modo non corretto o come base per la propria progettazione. Ecco una panoramica visiva di alto livello per questa soluzione:

![Creare un'app per la logica watchdog che monitora un'app per la logica di controllo integrità nella posizione primaria](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Controllare la disponibilità dell'istanza primaria

Per determinare se l'istanza primaria è disponibile, in esecuzione e in grado di funzionare, è possibile creare un'app per la logica di controllo di integrità che si trova nella stessa posizione dell'istanza primaria. È quindi possibile chiamare questa app di controllo dell'integrità da un percorso alternativo. Se l'app di controllo dell'integrità risponde correttamente, l'infrastruttura sottostante per il servizio app per la logica di Azure in tale area è disponibile e funzionante. Se l'app di controllo dell'integrità non riesce a rispondere, è possibile presupporre che il percorso non sia più integro.

Per questa attività, creare un'app per la logica di controllo dello stato di base che esegua queste attività:

1. Riceve una chiamata dall'app watchdog usando il trigger request.

1. Rispondere con uno stato che indica se l'app per la logica selezionata funziona ancora usando l'azione di risposta.

   > [!IMPORTANT]
   > L'app per la logica di controllo integrità deve usare un'azione di risposta in modo che l'app risponda in modo sincrono, non in modo asincrono.

1. Facoltativamente, per determinare ulteriormente se il percorso primario è integro, è possibile calcolare l'integrità di tutti gli altri servizi che interagiscono con l'app per la logica di destinazione in questa posizione. È sufficiente espandere l'app per la logica controllo integrità per valutare l'integrità anche per questi altri servizi.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Creare un'app per la logica watchdog

Per monitorare l'integrità dell'istanza primaria e chiamare l'app per la logica controllo integrità, creare un'app per la logica "watchdog" in un *percorso alternativo*. Ad esempio, è possibile configurare l'app per la logica watchdog in modo che se la chiamata della logica di controllo dell'integrità non riesce, il watchdog può inviare un avviso al team operativo in modo che possa esaminare l'errore e perché l'istanza primaria non risponde.

> [!IMPORTANT]
> Assicurarsi che l'app per la logica watchdog si trovi in un *percorso diverso rispetto alla posizione primaria*. Se il servizio app per la logica nella posizione primaria riscontra problemi, l'app per la logica watchdog potrebbe non essere eseguita.

Per questa attività, nella posizione secondaria creare un'app per la logica watchdog che esegua queste attività:

1. Eseguire in base a una ricorrenza fissa o pianificata usando il trigger di ricorrenza.

   È possibile impostare la ricorrenza su un valore che si trova al di sotto del livello di tolleranza per l'obiettivo del tempo di ripristino (RTO).

1. Chiamare l'app per la logica controllo integrità nella posizione primaria usando l'azione HTTP, ad esempio:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Attivare l'istanza secondaria

Per attivare automaticamente l'istanza secondaria, è possibile creare un'app per la logica che chiama l'API di gestione, ad esempio il [connettore Azure Resource Manager](/connectors/arm/) per attivare le app per la logica appropriate nella posizione secondaria. È possibile espandere l'app watchdog per chiamare questa app per la logica di attivazione dopo un numero specifico di errori.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Raccogliere dati di diagnostica

È possibile configurare la registrazione per le esecuzioni dell'app per la logica e inviare i dati di diagnostica risultanti ai servizi, ad esempio archiviazione di Azure, Hub eventi di Azure e Log Analytics di Azure per un'ulteriore gestione ed elaborazione.

* Se si vogliono usare questi dati con Log Analytics di Azure, è possibile rendere disponibili i dati per i percorsi primario e secondario impostando le **impostazioni di diagnostica** dell'app per la logica e inviando i dati a più aree di lavoro log Analytics. Per altre informazioni, vedere [configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se si vuole inviare i dati ad archiviazione di Azure o hub eventi di Azure, è possibile rendere disponibili i dati per i percorsi primario e secondario impostando la ridondanza geografica. Per altre informazioni, vedere questi articoli:<p>

  * [Ripristino di emergenza e failover dell'account di archiviazione BLOB di Azure](../storage/common/storage-disaster-recovery-guidance.md)
  * [Ripristino di emergenza geografico di hub eventi di Azure](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della resilienza per Azure](/azure/architecture/framework/resiliency/overview)
* [Elenco di controllo per la resilienza per servizi di Azure specifici](/azure/architecture/checklist/resiliency-per-service)
* [Gestione dei dati per la resilienza in Azure](/azure/architecture/framework/resiliency/data-management)
* [Backup e ripristino di emergenza per le applicazioni Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Eseguire il ripristino dopo un'interruzione di servizio a livello di area](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Contratti di servizio Microsoft per i servizi di Azure](https://azure.microsoft.com/support/legal/sla/)
