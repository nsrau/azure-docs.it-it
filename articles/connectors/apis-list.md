---
title: Connettori per App per la logica di Azure
description: Automatizzare i flussi di lavoro con i connettori per le app per la logica di Azure, ad esempio i connettori predefiniti, gestiti, locali, di integrazione, ISE e aziendali
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247332"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

I connettori consentono di accedere rapidamente da App per la logica di Azure a eventi, dati e azioni tra app, servizi, sistemi, protocolli e piattaforme diversi. Usando i connettori delle app per la logica è possibile espandere le capacità delle applicazioni locali e cloud per eseguire attività con i dati creati e già disponibili.

Mentre App per la logica offre [centinaia di connettori,](https://docs.microsoft.com/connectors)in questo articolo vengono descritti i connettori *popolari e più comunemente usati* che vengono utilizzati con successo da migliaia di app e milioni di esecuzioni per l'elaborazione di dati e informazioni. Per trovare l'elenco completo dei connettori e le informazioni di riferimento di ogni connettore, ad esempio trigger, azioni e limiti, esaminare le pagine di riferimento dei connettori in [Panoramica connettori](https://docs.microsoft.com/connectors). Inoltre, ulteriori informazioni su [trigger e azioni](#triggers-actions), Logic Apps modello di [prezzo,](../logic-apps/logic-apps-pricing.md)e [Logic Apps dettagli di prezzo](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Per eseguire l'integrazione con un servizio o un'API che non dispone di connettore, è possibile chiamare direttamente il servizio tramite un protocollo come HTTP oppure creare un [connettore personalizzato.](#custom)

## <a name="connector-types"></a>Tipi di connettore

I connettori sono disponibili come trigger e azioni incorporati o come connettori gestiti.

<a name="built-in"></a>

* [**Built-in**](#built-ins): Trigger e azioni incorporati sono "nativi" per le app per la logica di Azure e consentono di eseguire queste attività per le app per la logica:Built-in : Built-in triggers and actions are "native" to Azure Logic Apps and help you perform these tasks for your logic apps:

  * Esegui su pianificazioni personalizzate e avanzate.

  * Organizzare e controllare il flusso di lavoro dell'app per la logica, ad esempio cicli e condizioni, nonché usare variabili e operazioni sui dati.

  * Comunicare con altri endpoint.

  * Ricevere e rispondere alle richieste.

  * Chiamare le funzioni di Azure, le app API di Azure (app Web), le API gestite e pubblicate con Gestione API di Azure e le app per la logica annidate in grado di ricevere richieste.

<a name="managed-connectors"></a>

* [**Connettori gestiti:**](#managed-api-connectors)distribuiti e gestiti da Microsoft, questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, inclusi Office 365, Archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora. Alcuni connettori supportano in modo specifico scenari di comunicazione business-to-business (B2B) e richiedono un account di [integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica. Prima di usare alcuni connettori, potrebbe essere necessario creare prima le connessioni gestite da App per la logica di Azure.Before using certain connectors, you might have to first create connections, which are managed by Azure Logic Apps.

  Ad esempio, se si utilizza Microsoft BizTalk Server, le app per la logica possono connettersi e comunicare con BizTalk Server utilizzando il connettore locale di [BizTalk Server.](#on-premises-connectors) È quindi possibile estendere o eseguire operazioni di tipo BizTalk in App per la logica utilizzando [connettori dell'account di integrazione](#integration-account-connectors).

  I connettori sono classificati come Standard o Enterprise. [I connettori aziendali](#enterprise-connectors) forniscono l'accesso a sistemi aziendali come SAP, IBM MQ e IBM 3270 a un costo aggiuntivo. Per determinare se un connettore è Standard o Enterprise, vedere i dettagli tecnici nella pagina di riferimento di ciascun connettore in [Panoramica connettori](https://docs.microsoft.com/connectors).

  È inoltre possibile identificare i connettori utilizzando queste categorie, anche se alcuni connettori possono attraversare più categorie. Ad esempio, SAP è un connettore Enterprise e un connettore locale:For example, SAP is an Enterprise connector and an on-premises connector:

  |   |   |
  |---|---|
  | [**Connettori gestiti**](#managed-api-connectors) | Consentono di creare app per la logica che usano i servizi, ad esempio archiviazione BLOB di Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e molti altri. |
  | [**Connettori locali**](#on-premises-connectors) | Dopo aver installato e configurato il [gateway dati locale][gateway-doc], questi connettori consentono alle app per la logica di accedere a sistemi locali quali SQL Server, SharePoint Server, Oracle DB, condivisioni di file e altri. |
  | [**Connettori dell'account di integrazione**](#integration-account-connectors) | Disponibili quando si crea e si paga un account di integrazione, questi connettori trasformano e convalidano XML, codificano e decodificano file flat ed elaborano messaggi business-to-business (B2B) con i protocolli AS2, EDIFACT e X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Connettersi da un ambiente di servizio di integrazioneConnect from an integration service environment

Per le app per la logica che richiedono l'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un ambiente del servizio di integrazione isolata [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in cui è possibile compilare, distribuire ed eseguire le app per la logica in risorse dedicate. In Progettazione app per la logica, quando si esplorano i connettori che si desidera utilizzare per le app per la logica in un ISE, viene visualizzata un'etichetta **CORE** nei trigger e nelle azioni incorporati, mentre l'etichetta **ISE** viene visualizzata in alcuni connettori:

* **CORE**: I trigger predefiniti e le azioni con questa etichetta vengono eseguiti nella stessa ISE delle app per la logica, ad esempio:

  ![Connettore ISE di esempio](./media/apis-list/example-core-connector.png)

* **ISE**: i connettori gestiti con questa etichetta vengono eseguiti nella stessa ISE delle app per la logica, ad esempio:

  ![Connettore ISE di esempio](./media/apis-list/example-ise-connector.png)

  Se si dispone di un sistema locale connesso a una rete virtuale di Azure, un ISE consente alle app per la logica di accedere direttamente a tale sistema senza il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) In alternativa, è possibile utilizzare il connettore **ISE** di tale sistema, se disponibile, un'azione HTTP o un [connettore personalizzato.](#custom) Per i sistemi locali che non dispongono di connettori **ISE,** usare il gateway dati locale. Per esaminare i connettori ISE disponibili, vedere [Connettori ISE](#ise-connectors).

* Tutti gli altri connettori senza l'etichetta CORE o **ISE,** che è possibile continuare a usare, vengono eseguiti nel servizio globale App per la logica multi-tenant, ad esempio:All other connectors without the **CORE** or ISE label, which you can continue to use, run in the global, multi-tenant Logic Apps service, for example:

  ![Esempio di connettore multi-tenant](./media/apis-list/example-multi-tenant-connector.png)

Le app per la logica eseguite in isE e i relativi connettori, indipendentemente dalla posizione in cui vengono eseguiti, seguono un piano tariffario fisso rispetto al piano tariffario basato sul consumo. Per altre informazioni, vedere queste pagine:For more information, see these pages:

* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Dettagli sui prezzi delle app per la logica](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Predefinito

App per la logica fornisce trigger e azioni incorporati che consentono di creare flussi di lavoro basati sulla pianificazione, consentire alle app per la logica di comunicare con altre app e servizi, controllare il flusso di lavoro tramite le app per la logica e gestire o modificare i dati.

|   |   |   |   |
|---|---|---|---|
| [![][schedule-icon]<br>Icona API**Pianificazione**][schedule-doc] | - Eseguire un'app per la logica su una ricorrenza specificata, che va dalle pianificazioni di base a quelle avanzate con il trigger [ **Ricorrenza** ][schedule-recurrence-doc]. <p>- Eseguire un'app per la logica che deve gestire i dati in blocchi continui con il trigger [ **Finestra scorrevole** ][schedule-sliding-window-doc]. <p>- Sospendere l'app per la logica per una durata specificata con l'azione [ **Ritardo** ][schedule-delay-doc]. <p>- Sospendere l'app per la logica fino alla data e all'ora specificate con l'azione [ **Ritarda fino all'azione** ][schedule-delay-until-doc]. | [![][batch-icon]<br>Icona API**Batch**][batch-doc] | - Elaborare i messaggi in batch con il trigger **Messaggi batch**. <p>- Richiamare le app per la logica con trigger di batch esistenti tramite l'azione **Invia messaggi al batch**. |
| [![][http-icon]<br>Icona API**HTTP**][http-doc] | Chiamare endpoint HTTP o HTTPS con trigger e azioni per HTTP. Altri trigger e azioni http incorporati includono [HTTP, Swagger][http-swagger-doc] e [HTTP e Webhook][http-webhook-doc]. | [![][http-request-icon]<br>Icona API**Richiesta**][http-request-doc] | - Rendere l'app per la logica richiamabile da altre app o servizi, attiva gli eventi delle risorse della griglia di eventi o attiva le risposte agli avvisi di Centro sicurezza con il pulsante **Richiesta**. <p>- Inviare risposte a un'app o a un servizio con l'azione **Risposta**. |
| [![Icona API**Gestione <br>API di Azure** ][azure-api-management-icon]<br>][azure-api-management-doc] | Richiamare trigger e azioni definite dalle proprie API che vengono gestite e pubblicate con Azure API Management. | [![Icona API][azure-app-services-icon]<br>Servizi**app <br>di Azure**][azure-app-services-doc] | Chiamare l'App per le API Azure o le app Web, ospitato nel servizio App di Azure. I trigger e le azioni definiti da queste app appaiono come qualsiasi altro trigger e azione di prima classe quando Swagger è incluso.|
| [![Icona][azure-logic-apps-icon]<br>API**App <br>per la logica di Azure**][nested-logic-app-doc] | Chiamare altre app per la logica che iniziano con il trigger **Di richiesta.** |
|||||

### <a name="run-code-from-logic-apps"></a>Eseguire codice dalle app per la logicaRun code from logic apps

App per la logica fornisce azioni predefinite per l'esecuzione di codice personalizzato nel flusso di lavoro dell'app per la logica:Logic Apps provides built-in actions for running your own code in your logic app's workflow:

|   |   |   |   |
|---|---|---|---|
| [![Icona API][azure-functions-icon]<br>Funzioni**di Azure**][azure-functions-doc] | Richiamare le funzioni Azure che eseguono frammenti di codice personalizzati (C# o Node.js) dalle app per la logica. | [![][inline-code-icon]<br>Icona API Codice**inlineato**][azure-functions-doc] | Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica. |
|||||

### <a name="control-workflow"></a>Controllare il flusso di lavoro

App per la logica fornisce azioni predefinite per la strutturazione e il controllo delle azioni nel flusso di lavoro dell'app per la logica:Logic Apps provides built-in actions for structuring and controlling the actions in your logic app's workflow:

|   |   |   |   |
|---|---|---|---|
| [![][condition-icon]<br>**Condizione** dell'icona integrata][condition-doc] | Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false. | [![Icona][for-each-icon]<br>integrata**Per ogni**][for-each-doc] | Eseguire le stesse azioni su ogni elemento in una matrice. |
| [![][scope-icon]<br>**Ambito** dell'icona integrato][scope-doc] | Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito. | [![][switch-icon]<br>**Interruttore** a icone integrato][switch-doc] | Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito. |
| [![][terminate-icon]<br>**Icona** integrata Termina][terminate-doc] | Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva. | [![Icona][until-icon]<br>**integrata fino a**][until-doc] | Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato. |
|||||

### <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

App per la logica fornisce azioni incorporate per l'utilizzo degli output dei dati e dei relativi formati:Logic Apps provides built-in actions for working with data outputs and their formats:

|   |   |
|---|---|
| [![Operazioni predefinite sui][data-operations-icon]<br>**dati** delle icone][data-operations-doc] | Eseguire operazioni con i dati: <p>- **Componi**: crea un singolo output da più input con tipi diversi. <br>- **Crea tabella CSV**: creare una tabella CSV (valore separato da virgole) da una matrice con oggetti JSON. <br>- **Crea tabella HTML**: crea una tabella HTML da una matrice di oggetti JSON. <br>- **Filtra matrice**: creare una matrice da elementi di un'altra matrice che soddisfano i criteri specificati. <br>- **Join**: crea una stringa da tutti gli elementi in una matrice e separa gli elementi con il delimitatore specificato. <br>- **Analizza JSON:** crea token di facile utilizzo dalle proprietà e dai relativi valori nel contenuto JSON in modo da poterusare tali proprietà nel flusso di lavoro. <br>- **Seleziona**: crea una matrice con oggetti JSON trasformando elementi o valori in un’altra matrice ed eseguendo il mapping di tali elementi per proprietà specifiche. |
| ![Icona predefinita][date-time-icon]<br>**Data Ora** | Eseguire operazioni con i timestamp: <p>- **Aggiungi a time**: aggiunge il numero di unità specificato a un timestamp. <br>- **Converti fuso orario**: converte un timestamp dal fuso orario di origine al fuso orario di destinazione. <br>- **Tempo corrente**: restituisce il timestamp corrente come stringa. <br>- **Recupera time futuro**: restituisce il timestamp corrente più le unità di tempo specificate. <br>- **Recupera time passato**: restituisce il timestamp corrente meno le unità di tempo specificate. <br>- **Sottrai da time**: sottrae un numero di unità di tempo da un timestamp. |
| [![][variables-icon]<br>**Variabili** di icona integrate][variables-doc] | Eseguire operazioni con le variabili: <p>- **Accoda a variabile di matrice**: inserisce un valore come l'ultimo elemento nella matrice memorizzato da una variabile. <br>- **Accoda a variabile di stringa**: inserire un valore come l'ultimo carattere in una stringa archiviata da una variabile. <br>- **Decrementa variabile**: riduce una variabile di un valore costante. <br>- **Incrementa variabile**: aumenta una variabile di un valore costante. <br>- **Inizializza variabile**: crea una variabile e dichiara il tipo di dati e il valore iniziale. <br>- **Imposta variabile**: assegna un valore diverso ad una variabile esistente. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Connettori gestiti

App per la logica fornisce questi connettori Standard più diffusi per l'automazione di attività, processi e flussi di lavoro con questi servizi o sistemi:Logic Apps provides these popular Standard connectors for automating tasks, processes, and workflows with these services or systems:

|   |   |   |   |
|---|---|---|---|
| [![Icona][azure-service-bus-icon]<br>API**Bus di servizio** di Azure][azure-service-bus-doc] | Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica. | [![][sql-server-icon]<br>Icona API SQL**Server**][sql-server-doc] | Connettersi a SQL Server in locale o a un database SQL di Azure nel cloud in modo da poter gestire record, eseguire stored procedure o eseguire query. |
| [![Icona API**Archiviazione<br>BLOB** di Azure][azure-blob-storage-icon]<br>][azure-blob-storage-doc] | Connettersi all'account di archiviazione in modo da poter creare e gestire il contenuto BLOB. | [![Icona API**Office 365<br>Outlook** ][office-365-outlook-icon]<br>][office-365-outlook-doc] | Connettersi all'account di posta elettronica di Office 365 in modo da poter creare e gestire messaggi di posta elettronica, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora. |
| [![][sftp-ssh-icon]<br>Icona API**SFTP-SSH**][sftp-ssh-doc] | Connettersi ai server SFTP a cui è possibile accedere da Internet utilizzando SSH in modo da poter lavorare con file e cartelle. | [![][sharepoint-online-icon]<br>Icona API**SharePoint<br>Online**][sharepoint-online-doc] | Connettersi a SharePoint Online in modo da poter gestire file, allegati, cartelle e altro ancora. | 
| [![][dynamics-365-icon]<br>Icona API Dynamics**365<br> **][dynamics-365-doc] | Connettiti al tuo account Dynamics 365 in modo da poter creare e gestire record, elementi e altro ancora. | [![Icona][azure-queues-icon]<br>API Code di**Azure <br>**][azure-queues-doc] | Connettersi all'account di archiviazione di Azure in modo da poter creare e gestire code e messaggi |
| [![][ftp-icon]<br>Icona API**FTP**][ftp-doc] | Connettersi ai server FTP a cui è possibile accedere da Internet in modo da poter lavorare con file e cartelle. | [![Icona][file-system-icon]<br>API**File <br>system**][file-system-doc] | Connettersi alla condivisione file locale in modo da poter creare e gestire i file. |
| [![Icona API Hub di eventi di**Azure** ][azure-event-hubs-icon]<br>][azure-event-hubs-doc] | Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale. | [![Icona API**Griglia** **di eventi di**<br>Azure][azure-event-grid-icon]<br>][azure-event-grid-doc] | Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate. |
| [![][salesforce-icon]<br>Icona API**Salesforce**][salesforce-doc] | Connettiti al tuo account Salesforce in modo da poter creare e gestire elementi come record, lavori, oggetti e altro ancora. | [![][twitter-icon]<br>Icona API**Twitter**][twitter-doc] | Connettiti al tuo account Twitter in modo da poter gestire tweet, follower, cronologia e altro ancora. Salvare i tweet in Excel, SQL o SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connettori locali

Ecco alcuni connettori standard comunemente usati che App per la logica fornisce per l'accesso a dati e risorse nei sistemi locali. Prima di creare una connessione a un sistema locale, è necessario innanzitutto [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria.

|   |   |   |   |   |
|---|---|---|---|---|
| [![][biztalk-server-icon]<br>Icona API**BizTalk** <br> **Server**][biztalk-server-doc] | [![Icona][file-system-icon]<br>API**File <br>system**][file-system-doc] | [![][ibm-db2-icon]<br>Icona API**IBM DB2**][ibm-db2-doc] | [![][ibm-informix-icon]<br>Icona API**IBM** <br> **Informix**][ibm-informix-doc] | [![][mysql-icon]<br>Icona API**MySQL**][mysql-doc] |
| [![][oracle-db-icon]<br>Icona API Oracle**DB**][oracle-db-doc] | [![][postgre-sql-icon]<br>Icona API**PostgreSQL**][postgre-sql-doc] | [![][sharepoint-server-icon]<br>Icona API**SharePoint <br>Server**][sharepoint-server-doc] | [![Icona API**SQL <br>Server** ][sql-server-icon]<br>][sql-server-doc] | [![][teradata-icon]<br>Icona API**Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione

App per la logica fornisce connettori standard per la creazione di soluzioni business-to-business (B2B) con le app per la logica quando si crea e si paga un account di [integrazione,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)disponibile tramite Enterprise Integration Pack (EIP) in Azure. Con questo account, è possibile creare e archiviare artefatti B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via. Per usare questi artefatti, associare le app per la logica con l'account di integrazione. Se attualmente si usa BizTalk Server, questi connettori potrebbero sembrare già familiari.

|   |   |   |   |
|---|---|---|---|
| [![][as2-icon]<br>Icona API Decodifica**AS2 <br>**][as2-doc] | [![][as2-icon]<br>Icona API Codifica**AS2 <br>**][as2-doc] | [![Icona API**Decodifica <br>EDIFACT** ][edifact-icon]<br>][edifact-decode-doc] | [![Icona API**Codifica <br>EDIFACT** ][edifact-icon]<br>][edifact-encode-doc] |
| [![Icona API**Decodifica <br>file flat** ][flat-file-decode-icon]<br>][flat-file-decode-doc] | [![Icona][flat-file-encode-icon]<br>API**Codifica <br>file piatta**][flat-file-encode-doc] | [![Icona][integration-account-icon]<br>API**Account di integrazione <br>**][integration-account-doc] | [![][liquid-icon]<br>Icona API**Trasformazioni liquide** <br> **transforms**][json-liquid-transform-doc] |
| [![Icona API**Decodifica <br>X12** ][x12-icon]<br>][x12-decode-doc] | [![Icona API**Codifica <br>X12** ][x12-icon]<br>][x12-encode-doc] | [![Trasformazioni][xml-transform-icon]<br>**XML** <br> **transforms** dell'icona API][xml-transform-doc] | [![][xml-validate-icon]<br>Icona API Convalida**XML <br>**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connettori aziendali

App per la logica fornisce questi connettori Enterprise per l'accesso a sistemi aziendali, ad esempio SAP e IBM MQ:

|   |   |   |
|---|---|---|
| [![][ibm-3270-icon]<br>Icona API**IBM 3270**][ibm-3270-doc] | [![][ibm-mq-icon]<br>Icona API**IBM MQ**][ibm-mq-doc] | [![][sap-icon]<br>Icona API**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Connettori ISE

Per le app per la logica create ed eseguite un ambiente del servizio di integrazione isolata [(ISE),](#integration-service-environment)Progettazione app per la logica identifica i trigger e le azioni predefiniti che vengono eseguiti in ISE utilizzando l'etichetta **CORE.** I connettori gestiti eseguiti in ISE visualizzano l'etichetta **ISE,** mentre i connettori eseguiti nel servizio App per la logica multi-tenant globale non visualizzano nessuna delle due etichette. Questo elenco mostra i connettori che attualmente dispongono di versioni ISE:

|   |   |   |   |   |
|---|---|---|---|---|
[![][as2-icon]<br>Icona API**AS2**][as2-doc] | [![Icona API**Archiviazione<br>BLOB** di Azure][azure-blob-storage-icon]<br>][azure-blob-storage-doc] | [![Icona API**Azure <br> Cosmos DB** ][azure-cosmos-db-icon]<br>][azure-cosmos-db-doc] | [![Icona API**Hub <br>di eventi** di Azure][azure-event-hubs-icon]<br>][azure-event-hubs-doc] | [![Icona API**Archiviazione<br>file di Azure** ][azure-file-storage-icon]<br>][azure-file-storage-doc] |
| [![Icona][azure-service-bus-icon]<br>API**Bus <br>di servizio** di Azure][azure-service-bus-doc] | [![Icona][azure-sql-data-warehouse-icon]<br>API**Di <br>Azure SQL Data Warehouse**][azure-sql-data-warehouse-doc] | [![Icona API][azure-table-storage-icon]<br>Archiviazione**tabelle <br>di Azure**][azure-table-storage-doc] | [![Icona][azure-queues-icon]<br>API Code di**Azure <br>**][azure-queues-doc] | [![][edifact-icon]<br>Icona API**EDIFACT**][edifact-doc] |
| [![Icona][file-system-icon]<br>API**File <br>system**][file-system-doc] | [![][ftp-icon]<br>Icona API**FTP**][ftp-doc] | [![][ibm-3270-icon]<br>Icona API**IBM 3270**][ibm-3270-doc] | [![][ibm-db2-icon]<br>Icona API**IBM DB2**][ibm-db2-doc] | [![][ibm-mq-icon]<br>Icona API**IBM MQ**][ibm-mq-doc] |
| [![][sap-icon]<br>Icona API**SAP**][sap-connector-doc] | [![][sftp-ssh-icon]<br>Icona API**SFTP-SSH**][sftp-ssh-doc] | [![][smtp-icon]<br>Icona API**SMTP**][smtp-doc] | [![Icona API**SQL <br>Server** ][sql-server-icon]<br>][sql-server-doc] | [![][x12-icon]<br>Icona API**X12**][x12-doc] |
||||||

Per altre informazioni, vedere gli argomenti seguenti:

* [Accesso alle risorse di rete virtuale di Azure dalle app per la logica di AzureAccess to Azure virtual network resources from Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Trigger e tipi di azione

I connettori possono fornire *trigger,* *azioni*o entrambi. Un *trigger* è il primo passaggio in qualsiasi app per la logica, in genere specificando l'evento che attiva il trigger e avvia l'esecuzione dell'app per la logica. Ad esempio, il connettore FTP dispone di un trigger che avvia l'app per la logica "quando un file viene aggiunto o modificato". Alcuni trigger controllano regolarmente l'evento o i dati specificati e quindi vengono attivati quando rilevano l'evento o i dati specificati. Altri trigger attendono ma si attivano immediatamente quando si verifica un evento specifico o quando sono disponibili nuovi dati. I trigger passano anche tutti i dati necessari all'app per la logica. L'app per la logica può leggere e usare i dati in tutto il flusso di lavoro. Ad esempio, il connettore Twitter ha un trigger, "Quando viene pubblicato un nuovo tweet", che passa il contenuto del tweet nel flusso di lavoro dell'app per la logica.

Dopo l'attivazione di un trigger, le app per la logica di Azure creano un'istanza dell'app per la logica e avviano l'esecuzione delle *azioni* nel flusso di lavoro dell'app per la logica. Le azioni sono i passaggi che seguono il trigger ed eseguono attività nel flusso di lavoro dell'app per la logica. Ad esempio, è possibile creare un'app per la logica che ottiene i dati dei clienti da un database SQL ed elaborare tali dati in azioni successive.

Ecco i tipi generali di trigger offerti dalle app per la logica di Azure:Here are the general kinds of triggers that Azure Logic Apps provides:

* Trigger di *ricorrenza:* questo trigger viene eseguito in base a una pianificazione specificata e non è strettamente associato a un determinato servizio o sistema.

* *Trigger di polling*: Questo trigger esegue regolarmente il polling di un servizio o di un sistema specifico in base alla pianificazione specificata, controllando la presenza di nuovi dati o se si è verificato un evento specifico. Se sono disponibili nuovi dati o si è verificato l'evento specifico, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

* *Push trigger*: Questo trigger attende e rimane in ascolto di nuovi dati o che si verifichi un evento. Quando sono disponibili nuovi dati o quando si verifica l'evento, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configurazione del connettore

I trigger e le azioni di ogni connettore forniscono le proprie proprietà da configurare. Molti connettori richiedono inoltre di creare una *connessione* al servizio o al sistema di destinazione e di fornire credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o un'azione nell'app per la logica. Ad esempio, devi autorizzare una connessione a un account Twitter per l'accesso ai dati o per pubblicare post per tuo conto.

Per i connettori che usano OAuth di Azure Active Directory (Azure AD), la creazione di una connessione implica l'accesso al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso viene crittografato e archiviato in modo sicuro in un archivio segreto di Azure.For connectors that use Azure Active Directory (Azure AD) OAuth, creating a connection means signing in the service, such as Office 365, Salesforce, or GitHub, where your access token is [encrypted](../security/fundamentals/encryption-overview.md) and securely stored in an Azure secret store. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro. Altre informazioni sulla crittografia in Azure .Learn more about [encryption in Azure](../security/fundamentals/encryption-overview.md).

Le connessioni possono accedere al servizio o al sistema di destinazione per tutto il tempo consentito dal servizio o dal sistema. Per i servizi che usano le connessioni OAuth di Azure AD, ad esempio Office 365 e Dynamics, le app per la logica di Azure aggiornano i token di accesso per un tempo indefinito. Altri servizi potrebbero avere limiti su quanto tempo Le app per la logica di Azure possono usare un token senza eseguire l'aggiornamento. In genere, alcune azioni invalidano tutti i token di accesso, ad esempio la modifica della password.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per *qualsiasi* API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure. Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Le app per la logica distribuite ed eseguite in un ambiente del servizio di [integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure.Logic apps that you deploy and run in an integration service environment (ISE) can directly access resources in an Azure virtual network. Se si dispone di connettori personalizzati che richiedono il gateway dati locale e tali connettori sono stati creati all'esterno di ISE, anche le app per la logica in ISE possono utilizzare tali connettori.
>
> I connettori personalizzati creati all'interno di isE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse a una rete virtuale di Azure che ospita ISE. Pertanto, le app per la logica in un ISE molto probabilmente non hanno bisogno del gateway dati durante la comunicazione con tali risorse.
>
> Per altre informazioni sulla creazione di ISE, vedere [Connettersi a reti virtuali](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)di Azure da App per la logica di Azure.For more information about creating ISEs, see Connect to Azure virtual networks from Azure Logic Apps .

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare [l'elenco completo](https://docs.microsoft.com/connectors) dei connettori
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare connettori personalizzati per app per la logica](https://docs.microsoft.com/connectors/custom-connectors/)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creare un'istanza del servizio Gestione API di Azure per gestire e pubblicare le proprie API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrare le app per la logica con le app per le API del servizio app"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Elaborare i messaggi in gruppi o come batch"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Valutare una condizione ed eseguire azioni diverse a seconda che la condizione sia vera o falsa"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Eseguire le stesse azioni su ogni elemento in una matrice"
[http-doc]: ./connectors-native-http.md "Chiamare endpoint HTTP o HTTPS dalle app per la logicaCall HTTP or HTTPS endpoints from your logic apps"
[http-request-doc]: ./connectors-native-reqres.md "Ricevere richieste HTTP nelle app per la logicaReceive HTTP requests in your logic apps"
[http-response-doc]: ./connectors-native-reqres.md "Rispondere alle richieste HTTP dalle app per la logicaRespond to HTTP requests from your logic apps"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chiamare gli endpoint REST dalle app per la logicaCall REST endpoints from your logic apps"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendere eventi specifici dagli endpoint HTTP o HTTPSWait for specific events from HTTP or HTTPS endpoints"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selezionare e filtrare matrici con l'azione di query"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Eseguire app per la logica in base a una pianificazioneRun logic apps based a schedule"
[schedule-delay-doc]: ./connectors-native-delay.md "Ritardare l'esecuzione dell'azione successiva"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Ritardare l'esecuzione dell'azione successiva"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Eseguire app per la logica in base a una pianificazione ricorrenteRun logic apps on a recurring schedule"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Eseguire app per la logica che devono gestire i dati in blocchi contiguiRun logic apps that need to handle data in contiguous chunks"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizza le azioni in gruppi, che ottengono un proprio stato dopo la fine delle azioni in gruppo"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizzare le azioni in casi a cui vengono assegnati valori univoci. Eseguire solo il case il cui valore corrisponde al risultato di un'espressione, un oggetto o un token. Se non esistono corrispondenze, eseguire il caso predefinito"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrestare o annullare un flusso di lavoro attivamente in esecuzione per l'app per la logica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ripetere le azioni fino a quando la condizione specificata è vera o viene modificato uno stato"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Eseguire operazioni sui dati, ad esempio il filtro di matrici o la creazione di tabelle CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Eseguire operazioni con variabili, ad esempio inizializzazione, set, incremento, decremento e accodare a variabile di stringa o matrice"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Connettersi a Azure Cosmos DB in modo da poter accedere a documenti e stored procedureConnect to Azure Cosmos DB so that you can access documents and stored procedures"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorare gli eventi pubblicati da una griglia di eventi, ad esempio quando cambiano le risorse di Azure o le risorse di terze partiMonitor events published by an Event Grid, for example, when Azure resources or third-party resources change"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi agli hub eventi di Azure in modo da poter ricevere e inviare eventi tra app per la logica e hub eventiConnect to Azure Event Hubs so that you can receive and send events between logic apps and Event Hubs"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Connettersi all'account di archiviazione di Azure in modo da poter creare, aggiornare, ottenere ed eliminare fileConnect to your Azure Storage account so that you can create, update, get, and delete files"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Connettersi all'account di archiviazione di Azure in modo da poter creare e gestire code e messaggi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Connettersi ad Azure SQL Data Warehouse in modo da poter visualizzare i datiConnect to Azure SQL Data Warehouse so that you can view your data"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Connettersi all'account di archiviazione di Azure in modo da poter creare, aggiornare ed eseguire query su tabelle e altro ancora"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Connettersi a BizTalk ServerBizTalk Server in modo da poter eseguire applicazioni basate su BizTalk side-by-side con le app per la logica di AzureConnect to your BizTalk Server so that you can run BizTalk-based applications side-by-side with Azure Logic Apps"
[box-doc]: ./connectors-create-api-box.md "Connettiti alla casella. Caricare, ottenere, eliminare, elencare i file e altro ancora"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Collegati a Dropbox. Caricare, ottenere, eliminare, elencare i file e altro ancora"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Connettersi a Dynamics CRM Online in modo da poter utilizzare i dati di CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Connettiti a Facebook. Pubblicare un post in una sequenza temporale, ottenere un feed di pagina e altro ancora"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftp-doc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[github-doc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Si connette a Google Calendar e può gestire il calendario"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Connettiti a GoogleDrive per poter lavorare con i tuoi dati"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Collegati a Fogli Google in modo da poter modificare i tuoi fogli"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Si connette a Google Tasks in modo da poter gestire le attività"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connettersi a 3270 app su mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, ottenere una tabella e altro ancora"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connettersi a IBM MQ in locale o in Azure per inviare e ricevere messaggi"
[instagram-doc]: ./connectors-create-api-instagram.md "Connettiti a Instagram. Attivare o agire sugli eventi"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Connettersi all'account MailChimp. Gestire e automatizzare la posta elettronica"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Connettersi al database MySQL locale in modo da poter leggere e scrivere dati"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account di Office 365 in modo da poter inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connettersi a Microsoft OneDrive personale in modo da poter caricare, eliminare, elencare file e altro ancora"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connettersi alla tua azienda Microsoft OneDrive in modo da poter caricare, eliminare, elencare i file e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle in modo da poter aggiungere, inserire, eliminare righe e altro ancora"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook in modo da poter gestire la posta elettronica, i calendari, i contatti e altro ancora"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Connettersi al database PostgreSQL in modo da poter leggere i dati dalle tabelle"
[project-online-doc]: ./connectors-create-api-projectonline.md "Connettersi a Microsoft Project Online in modo da poter gestire progetti, attività, risorse e altro ancora"
[rss-doc]: ./connectors-create-api-rss.md "Pubblicare e recuperare elementi del feed, attivare operazioni quando un nuovo elemento viene pubblicato in un feed RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connettersi all'account Salesforce. Gestire account, lead, opportunità e altro ancora"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire gli elenchi dei destinatari"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Connettersi all'account SFTP utilizzando SSH. Caricare, ottenere, eliminare file e altro ancora"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Connettersi al server SharePoint locale. Gestire documenti, voci di elenco e altro ancora"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Connettersi a SharePoint Online. Gestire documenti, voci di elenco e altro ancora"
[slack-doc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtp-doc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connettersi al database SQL di Azure o a SQL Server.Connect to Azure SQL Database or SQL Server. Creare, aggiornare, ottenere ed eliminare voci in una tabella di database SQLCreate, update, get, and delete ntries in a SQL database table"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Connettersi al database Teradata per leggere i dati dalle tabelle"
[trello-doc]: ./connectors-create-api-trello.md "Collegati a Trello. Gestisci i tuoi progetti e organizza qualsiasi cosa con chiunque"
[twilio-doc]: ./connectors-create-api-twilio.md "Collegati a Twilio. Inviare e ricevere messaggi, ottenere numeri disponibili, gestire i numeri di telefono in arrivo e altro ancora"
[twitter-doc]: ./connectors-create-api-twitter.md "Connettiti a Twitter. Ottieni timeline, pubblica tweet e altro ancora"
[yammer-doc]: ./connectors-create-api-yammer.md "Connettersi a Yammer. Pubblicare messaggi, ricevere nuovi messaggi e altro ancora"
[youtube-doc]: ./connectors-create-api-youtube.md "Connettiti a YouTube. Gestisci i tuoi video e canali"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificare e decodificare i messaggi che utilizzano il protocollo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificare e decodificare i messaggi che utilizzano il protocollo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodificare i messaggi che utilizzano il protocollo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificare i messaggi che utilizzano il protocollo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sui file flat di integrazione aziendale"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sui file flat di integrazione aziendale"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gestire i metadati per gli elementi dell'account di integrazioneManage metadata for integration account artifacts"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Trasformare JSON con modelli LiquidTransform JSON with Liquid templates"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificare e decodificare i messaggi che utilizzano il protocollo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodificare i messaggi che utilizzano il protocollo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificare i messaggi che utilizzano il protocollo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Trasformare i messaggi XMLTransform XML messages"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Convalidare i messaggi XMLValidate XML messages"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
