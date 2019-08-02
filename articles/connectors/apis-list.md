---
title: Connettori per App per la logica di Azure
description: È possibile automatizzare i flussi di lavoro con i connettori per App per la logica di Azure, inclusi connettori integrati, gestiti, locali, account di integrazione e aziendali
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 126c3fb348b1d53769a818ae4b21fcdbbef65615
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517229"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

I connettori consentono di accedere rapidamente da App per la logica di Azure a eventi, dati e azioni tra app, servizi, sistemi, protocolli e piattaforme diversi. Usando i connettori delle app per la logica è possibile espandere le capacità delle applicazioni locali e cloud per eseguire attività con i dati creati e già disponibili.

Sebbene app per la logica offra [centinaia di connettori](https://docs.microsoft.com/connectors), in questo articolo vengono descritti i connettori più diffusi e comunemente usati da migliaia di app e milioni di esecuzioni per l'elaborazione di dati e informazioni. Per trovare l'elenco completo dei connettori e le informazioni di riferimento di ogni connettore, ad esempio trigger, azioni e limiti, vedere le pagine di riferimento del connettore in [Panoramica dei connettori](https://docs.microsoft.com/connectors). Altre informazioni sui [trigger e le azioni](#triggers-actions), il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica e [i dettagli prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica. 

> [!NOTE]
> Per l'integrazione con un servizio o un'API che non dispone di connettore, è possibile chiamare direttamente il servizio tramite un protocollo, ad esempio HTTP, oppure creare un [connettore personalizzato](#custom).

I connettori sono disponibili come trigger e azioni predefiniti oppure come connettori gestiti:

<a name="built-in"></a>

* Componenti [**predefiniti**](#built-ins): Questi trigger e azioni predefiniti sono "nativi" per le app per la logica di Azure e consentono di creare app per la logica che vengono eseguite in pianificazioni personalizzate, comunicano con altri endpoint, ricevono e rispondono alle richieste e chiamano funzioni di Azure, app per le API di Azure (app Web), API personalizzate gestito e pubblicato con gestione API di Azure e app per la logica annidate che possono ricevere richieste. È inoltre possibile utilizzare azioni predefinite che consentono di organizzare e controllare il flusso di lavoro dell'applicazione logica e anche di lavorare con i dati.

  > [!NOTE]
  > Le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure.
  > Quando si usa ISE, i trigger e le azioni predefiniti che visualizzano l'etichetta **principale** vengono eseguiti nello stesso ISE delle app per la logica. Le app per la logica, i trigger predefiniti e le azioni predefinite eseguite in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo.
  >
  > Per altre informazioni sulla creazione di ISEs, vedere [connettersi alle reti virtuali di Azure da app](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)per la logica di Azure. 
  > Per altre informazioni sui prezzi, vedere [modello di determinazione prezzi per app](../logic-apps/logic-apps-pricing.md)per la logica.

<a name="managed-connectors"></a>

* **Connettori gestiti**: Distribuiti e gestiti da Microsoft, questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, tra cui Office 365, archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora. Alcuni connettori supportano specificamente scenari di comunicazione business-to-business (B2B) e richiedono un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica. Prima di usare determinati connettori, potrebbe essere necessario creare prima le connessioni, gestite da app per la logica di Azure. 

  Ad esempio, se si usa Microsoft BizTalk Server, le app per la logica possono connettersi e comunicare con i BizTalk Server usando il [BizTalk Server on-premises Connector](#on-premises-connectors). 
  È quindi possibile estendere o eseguire operazioni di tipo BizTalk in App per la logica utilizzando [connettori dell'account di integrazione](#integration-account-connectors).

  I connettori sono classificati come standard o Enterprise. 
  I [connettori aziendali](#enterprise-connectors) forniscono l'accesso ai sistemi aziendali, ad esempio SAP, IBM mq e IBM 3270, per un costo aggiuntivo. Per determinare se un connettore è standard o Enterprise, vedere i dettagli tecnici nella pagina di riferimento di ogni connettore in [Panoramica dei connettori](https://docs.microsoft.com/connectors). 

  È anche possibile identificare i connettori usando queste categorie, sebbene alcuni connettori possano incrociare più categorie. 
  Ad esempio, SAP è un connettore aziendale e un connettore locale:

  |   |   |
  |---|---|
  | [**Connettori delle API gestiti**](#managed-api-connectors) | Consentono di creare app per la logica che usano i servizi, ad esempio archiviazione BLOB di Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e molti altri. |
  | [**Connettori locali**](#on-premises-connectors) | Dopo aver installato e configurato il [gateway dati locale][gateway-doc], questi connettori consentono alle app per la logica di accedere ai sistemi locali, ad esempio SQL Server, SharePoint Server, Oracle DB, condivisioni file e altro ancora. |
  | [**Connettori dell'account di integrazione**](#integration-account-connectors) | Disponibili quando si crea e si paga un account di integrazione, questi connettori trasformano e convalidano XML, codificano e decodificano file flat ed elaborano messaggi business-to-business (B2B) con i protocolli AS2, EDIFACT e X12. |
  |||

  > [!NOTE]
  > Le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure. Quando si usa un ISE, i connettori standard ed Enterprise che visualizzano l'etichetta **ISE** vengono eseguiti nello stesso ISE delle app per la logica. I connettori che non visualizzano l'etichetta ISE vengono eseguiti nel servizio app per la logica globale.
  >
  > Per i sistemi locali connessi a una rete virtuale di Azure, inserire ISE nella rete in modo che le app per la logica possano accedere direttamente a tali sistemi usando un connettore con un'etichetta **ISE** , un'azione http o un [connettore personalizzato](#custom). Le app per la logica e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo. 
  >
  > Per altre informazioni sulla creazione di ISEs, vedere [connettersi alle reti virtuali di Azure da app](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)per la logica di Azure.
  > Per altre informazioni sui prezzi, vedere [modello di determinazione prezzi per app](../logic-apps/logic-apps-pricing.md)per la logica.

  Per l'elenco completo dei connettori e per le informazioni di riferimento di ogni connettore, ad esempio azioni e tutti i trigger, definiti da una descrizione di OpenAPI (in precedenza spavalderia), oltre a qualsiasi limite, è possibile trovare l'elenco completo nella [Panoramica dei connettori](/connectors/). Per informazioni sui prezzi, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica e [Dettagli prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica. 

<a name="built-ins"></a>

## <a name="built-ins"></a>Predefiniti

App per la logica offre trigger e azioni predefiniti che consentono di creare flussi di lavoro basati sulla pianificazione, aiutare le applicazioni logiche a comunicare con altre applicazioni e servizi, controllare il flusso di lavoro attraverso le app per la logica e gestire o manipolare i dati.

|   |   |   |   | 
|---|---|---|---| 
| [![Icona][schedule-icon]<br/>API**pianificazione**][recurrence-doc] | - Eseguire l'app per la logica su una pianificazione specificata, che varia da ricorrenze di base a ricorrenze complesse, con il pulsante **Ricorrenza**. <p>- Mettere in pausa l'app per la logica per una durata specificata con l'azione **Ritarda**. <p>- Mettere in pausa l'app per la logica fino alla data e all'ora specificata con l'azione **Ritarda fino a**. | [![Icona][http-icon]<br/>API**http**][http-doc] | Comunicare con qualsiasi endpoint tramite HTTP con trigger e azioni per HTTP, HTTP + Swagger e HTTP + Webhook. | 
| [![**Richiesta** icona][http-request-icon]<br/>API][http-request-doc] | - Rendere l'app per la logica richiamabile da altre app o servizi, attiva gli eventi delle risorse della griglia di eventi o attiva le risposte agli avvisi di Centro sicurezza con il pulsante **Richiesta**. <p>- Inviare risposte a un'app o a un servizio con l'azione **Risposta**. | [![Icona][batch-icon]<br/>API**batch**][batch-doc] | - Elaborare i messaggi in batch con il trigger **Messaggi batch**. <p>- Richiamare le app per la logica con trigger di batch esistenti tramite l'azione **Invia messaggi al batch**. | 
| [![Icona][azure-functions-icon]<br/>API**funzioni di Azure**][azure-functions-doc] | Richiamare le funzioni Azure che eseguono frammenti di codice personalizzati (C# o Node.js) dalle app per la logica. | [![Icona][azure-api-management-icon]</br>API**gestione API di Azure**][azure-api-management-doc] | Richiamare trigger e azioni definite dalle proprie API che vengono gestite e pubblicate con Azure API Management. | 
| [![Icona][azure-app-services-icon]<br/>API**app Azure Services**][azure-app-services-doc] | Chiamare l'App per le API Azure o le app Web, ospitato nel servizio App di Azure. I trigger e le azioni definiti da queste app appaiono come qualsiasi altro trigger e azione di prima classe quando Swagger è incluso. | [![Icona][azure-logic-apps-icon]<br/>API app per la**logica di Azure<br/>** ][nested-logic-app-doc] | Chiamare altre App per la logica che iniziano con un trigger di richiesta. | 
||||| 

### <a name="control-workflow"></a>Controllare il flusso di lavoro

App per la logica offre azioni predefinite per la strutturazione e il controllo delle azioni nel flusso di lavoro dell'app per la logica:

|   |   |   |   | 
|---|---|---|---| 
| [![][condition-icon]<br/>**Condizione** icona predefinita][condition-doc] | Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false. | [![Icona][for-each-icon]</br>predefinita**per ogni**][for-each-doc] | Eseguire le stesse azioni su ogni elemento in una matrice. | 
| [![][scope-icon]<br/>**Ambito** dell'icona predefinito][scope-doc] | Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito. | [![][switch-icon]</br>**Opzione** icona predefinita][switch-doc] | Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito. | 
| [![][terminate-icon]<br/>**Terminazione** icona incorporata][terminate-doc] | Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva. | [![Icona][until-icon]<br/>predefinita**fino a**][until-doc] | Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

App per la logica offre azioni predefinite per l'uso degli output dei dati e dei relativi formati:  

|   |   | 
|---|---| 
| [![][data-operations-icon]<br/>**Operazioni dati** icona predefinite][data-operations-doc] | Eseguire operazioni con i dati: <p>- **Comporre**: Creare un singolo output da più input con tipi diversi. <br>- **Crea tabella CSV**: Creare una tabella con valori delimitati da virgole (CSV) da una matrice con oggetti JSON. <br>- **Crea tabella HTML**: Creare una tabella HTML da una matrice con oggetti JSON. <br>- **Filtra matrice**: Creare una matrice da elementi di un'altra matrice che soddisfino i criteri. <br>- **Join**: Creare una stringa da tutti gli elementi di una matrice e separare gli elementi con il delimitatore specificato. <br>- **Analizza JSON**: Creare token semplici da proprietà e i relativi valori nel contenuto JSON, in modo da poter usare tali proprietà nel flusso di lavoro. <br>- **Selezionare**: Creare una matrice con oggetti JSON trasformando gli elementi o i valori in un'altra matrice ed eseguendo il mapping di tali elementi alle proprietà specificate. | 
| ![Icona predefinita][date-time-icon]<br/>**Data Ora** | Eseguire operazioni con i timestamp: <p>- **Aggiungi a tempo**: Aggiungere il numero di unità specificato a un timestamp. <br>- **Converti fuso orario**: Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. <br>- **Ora corrente**: Restituisce il timestamp corrente come stringa. <br>- **Ottenere ora futura**: Restituisce il timestamp corrente più le unità di tempo specificate. <br>- **Ottenere l'ora precedente**: Restituisce il timestamp corrente meno le unità di tempo specificate. <br>- **Sottrai dal tempo**: Sottrae un numero di unità di tempo da un timestamp. |
| [![][variables-icon]<br/>**Variabili** di icona predefinite][variables-doc] | Eseguire operazioni con le variabili: <p>- **Accoda a variabile di matrice**: Inserire un valore come ultimo elemento in una matrice archiviata da una variabile. <br>- **Accoda a variabile stringa**: Inserire un valore come ultimo carattere in una stringa archiviata da una variabile. <br>- **Variabile**di decremento: Ridurre una variabile in base a un valore costante. <br>- **Variabile di incremento**: Aumentare una variabile in base a un valore costante. <br>- **Inizializza variabile**: Creare una variabile e dichiararne il tipo di dati e il valore iniziale. <br>- **Imposta variabile**: Assegnare un valore diverso a una variabile esistente. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Connettori delle API gestiti

App per la logica fornisce questi comuni connettori standard per l'automazione di attività, processi e flussi di lavoro con questi servizi o sistemi.

|   |   |   |   | 
|---|---|---|---| 
| [![Icona][azure-service-bus-icon]<br/>API**bus di servizio di Azure**][azure-service-bus-doc] | Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica. | [![Icona][sql-server-icon]<br/>API**SQL Server**][sql-server-doc] | Connettersi all'istanza locale di SQL Server o a un database SQL di Azure nel cloud per gestire record oppure eseguire stored procedure o query. | 
| [![Icona][office-365-outlook-icon]<br/>API**Office 365<br/>Outlook**][office-365-outlook-doc] | Connettersi all'account e-mail di Office 365 per creare e gestire e-mail, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora. | [![Icona][azure-blob-storage-icon]<br/>API**archiviazione BLOB<br/>di Azure**][azure-blob-storage-doc] | Connettersi all'account di archiviazione in modo che sia possibile creare e gestire il contenuto del BLOB. | 
| [![Icona][sftp-icon]<br/>API**SFTP**][sftp-doc] | Connettersi ai server SFTP a cui è possibile accedere da Internet per lavorare con file e cartelle. | [![Icona][sharepoint-online-icon]<br/>API**SharePoint<br/>online**][sharepoint-online-doc] | Connettersi a SharePoint Online in modo che sia possibile gestire i file, allegati, cartelle e altro ancora. | 
| [![Icona][dynamics-365-icon]<br/>API**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Connettersi all'account di Dynamics 365 in modo che sia possibile creare e gestire i record, elementi e altro ancora. | [![Icona][ftp-icon]<br/>API**FTP**][ftp-doc] | Connettersi ai server FTP a cui è possibile accedere da Internet per lavorare con file e cartelle. | 
| [![Icona][salesforce-icon]<br/>API**Salesforce**][salesforce-doc] | Connettersi all'account Salesforce in modo che sia possibile creare e gestire elementi quali record, processi, oggetti e altro ancora. | [![Icona][twitter-icon]<br/>API**Twitter**][twitter-doc] | Connettersi all'account Twitter in modo che sia possibile gestire tweet, follower, la sequenza temporale e altro ancora. Salvare i tweet in Excel, SQL o SharePoint. | 
| [![Icona][azure-event-hubs-icon]<br/>API**Hub eventi di Azure**][azure-event-hubs-doc] | Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale. | [![Icona][azure-event-grid-icon]<br/>API**griglia** di**eventi**</br>di Azure][azure-event-grid-doc] | Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connettori locali 

Di seguito sono riportati alcuni connettori standard usati comunemente dalle app per la logica per accedere ai dati e alle risorse nei sistemi locali. Prima di poter creare una connessione a un sistema locale, è necessario prima [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Icona API][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![Icona][file-system-icon]<br/>API**file</br> System**][file-system-doc] | [![Icona][ibm-db2-icon]<br/>API**IBM DB2**][ibm-db2-doc] | [![Icona][ibm-informix-icon]<br/>API**IBM** Informix</br>][ibm-informix-doc] | ![Icona API][mysql-icon]<br/>**MySQL** | 
| [![Icona][oracle-db-icon]<br/>API**Oracle DB**][oracle-db-doc] | ![Icona API][postgre-sql-icon]<br/>**PostgreSQL** | [![Icona][sharepoint-server-icon]<br/>API**SharePoint</br> server**][sharepoint-server-doc] | [![Icona][sql-server-icon]<br/>API**SQL</br> server**][sql-server-doc] | ![Icona API][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione

App per la logica offre connettori standard per la creazione di soluzioni business to business (B2B) con le app per la logica quando si crea e si paga un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), disponibile tramite il Enterprise Integration Pack (PEI) in Azure. Con questo account, è possibile creare e archiviare artefatti B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via. Per usare questi artefatti, associare le app per la logica con l'account di integrazione. Se attualmente si usa BizTalk Server, questi connettori potrebbero sembrare già familiari.

|   |   |   |   | 
|---|---|---|---| 
| [![Icona][as2-icon]<br/>API**decodifica</br> AS2**][as2-doc] | [![Icona][as2-icon]<br/>API**codifica</br> AS2**][as2-doc] | [![Icona][edifact-icon]<br/>API**decodifica</br> EDIFACT**][edifact-decode-doc] | [![Icona][edifact-icon]<br/>API**codifica</br> EDIFACT**][edifact-encode-doc] | 
| [![Icona][flat-file-decode-icon]<br/>API**decodifica file</br> Flat**][flat-file-decode-doc] | [![Icona][flat-file-encode-icon]<br/>API**codifica file</br> Flat**][flat-file-encode-doc] | [![Icona][integration-account-icon]<br/>API**account<br/>di integrazione**][integration-account-doc] | [![Icona][liquid-icon]<br/>API trasformazioni**Liquid**</br>][json-liquid-transform-doc] | 
| [![Icona][x12-icon]<br/>API**decodifica</br> X12**][x12-decode-doc] | [![Icona][x12-icon]<br/>API**codifica</br> X12**][x12-encode-doc] | [![Icona][xml-transform-icon]<br/>API trasformazioni**XML**</br>][xml-transform-doc] | [![Icona][xml-validate-icon]<br/>API**convalida <br/>XML**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connettori aziendali

App per la logica fornisce questi connettori aziendali per l'accesso ai sistemi aziendali, ad esempio SAP e IBM MQ:

|   |   |   | 
|---|---|---| 
| [![Icona][ibm-3270-icon]<br/>API**IBM 3270**][ibm-3270-doc] | [![Icona][ibm-mq-icon]<br/>API**IBM mq**][ibm-mq-doc] | [![Icona][sap-icon]<br/>API**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Trigger e azioni-altre informazioni

I connettori possono fornire *trigger*, *azioni*o entrambi. Un *trigger* è il primo passaggio di qualsiasi app per la logica, in genere specificando l'evento che attiva il trigger e avvia l'esecuzione dell'app per la logica. Ad esempio, il connettore FTP ha un trigger che avvia l'app per la logica "quando viene aggiunto o modificato un file". Alcuni trigger verificano periodicamente l'evento o i dati specificati e quindi vengono generati quando rilevano l'evento o i dati specificati. Altri trigger attendono ma vengono generati immediatamente quando si verifica un evento specifico o quando sono disponibili nuovi dati. I trigger passano anche tutti i dati necessari all'app per la logica. L'app per la logica può leggere e usare i dati in tutto il flusso di lavoro.
Ad esempio, il connettore Twitter ha un trigger "quando viene pubblicato un nuovo Tweet", che passa il contenuto del Tweet al flusso di lavoro dell'app per la logica. 

Quando viene attivato un trigger, app per la logica di Azure crea un'istanza dell'app per la logica e inizia a eseguire le *azioni* nel flusso di lavoro dell'app per la logica. Le azioni sono i passaggi che seguono il trigger ed eseguono attività nel flusso di lavoro dell'app per la logica. Ad esempio, è possibile creare un'app per la logica che ottiene i dati del cliente da un database SQL ed elabora tali dati in azioni successive. 

Di seguito sono riportati i tipi generali di trigger offerti da app per la logica di Azure:

* *Trigger*ricorrenza: Questo trigger viene eseguito in base a una pianificazione specificata e non è strettamente associato a un particolare servizio o sistema.

* *Trigger*di polling: Questo trigger esegue regolarmente il polling di un servizio o di un sistema specifico in base alla pianificazione specificata, verificando la presenza di nuovi dati o se si è verificato un evento specifico. Se sono disponibili nuovi dati o si è verificato l'evento specifico, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

* *Trigger di push*: Questo trigger attende e ascolta i nuovi dati o si verifica un evento. Quando sono disponibili nuovi dati o quando si verifica l'evento, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

<a name="custom"></a>

## <a name="connector-configuration"></a>Configurazione connettore

I trigger e le azioni di ciascun connettore forniscono le proprie proprietà da configurare. Molti connettori richiedono anche la prima volta che si crea una *connessione* al servizio o al sistema di destinazione e si forniscono le credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o un'azione nell'app per la logica. Ad esempio, è necessario autorizzare una connessione a un account Twitter per accedere ai dati o pubblicare per conto dell'utente. 

Per i connettori che usano OAuth, la creazione di una connessione significa accedere al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso è crittografato e archiviato in modo sicuro in un archivio segreto di Azure. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro. 

Le connessioni possono accedere al servizio o al sistema di destinazione per il periodo di tempo consentito da tale servizio o sistema. Per i servizi che usano le connessioni OAuth Azure Active Directory (AD), ad esempio Office 365 e Dynamics, app per la logica di Azure aggiorna i token di accesso per un periodo illimitato. Altri servizi potrebbero avere limiti per quanto tempo le app per la logica di Azure possono usare un token senza aggiornamento. In genere, alcune azioni invalidano tutti i token di accesso, ad esempio la modifica della password.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per *qualsiasi* API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure.
Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure.
> Se sono presenti connettori personalizzati che richiedono il gateway dati locale e i connettori sono stati creati al di fuori di un ISE, le app per la logica in un ISE possono anche usare tali connettori.
>
> I connettori personalizzati creati in un ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse a una rete virtuale di Azure che ospita ISE. Quindi, le app per la logica in un ISE probabilmente non necessitano del gateway dati durante la comunicazione con tali risorse.
>
> Per altre informazioni sulla creazione di ISEs, vedere [connettersi alle reti virtuali di Azure da app](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)per la logica di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Cercare l'[elenco completo di connettori](https://docs.microsoft.com/connectors)
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare connettori personalizzati per app per la logica](https://docs.microsoft.com/connectors/custom-connectors/)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creare un'istanza del servizio Gestione API di Azure per gestire e pubblicare le proprie API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrare le app per la logica con le app per le API del servizio app"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Elaborare i messaggi in gruppi o come batch"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false"
[delay-doc]: ./connectors-native-delay.md "Eseguire azioni di ritardo"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Eseguire le stesse azioni su ogni elemento in una matrice"
[http-doc]: ./connectors-native-http.md "Effettuare chiamate HTTP con il connettore HTTP"
[http-request-doc]: ./connectors-native-reqres.md "Aggiungere azioni per richieste e risposte HTTP alle app per la logica"
[http-response-doc]: ./connectors-native-reqres.md "Aggiungere azioni per richieste e risposte HTTP alle app per la logica"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Effettuare chiamate HTTP con il connettore HTTP e Swagger"
[http-webook-doc]: ./connectors-native-webhook.md "Aggiungere azioni e trigger webhook HTTP alle app per la logica"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[query-doc]: ./connectors-native-query.md "Selezionare e filtrare matrici con l'azione di query"
[recurrence-doc]:  ./connectors-native-recurrence.md "Attivare azioni ricorrenti per le app per la logica"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizza le azioni in gruppi, che ottengono un proprio stato dopo la fine delle azioni in gruppo" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizzare le azioni in case, a cui vengono assegnati valori univoci. Eseguire solo il case il cui valore corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrestare o annullare un flusso di lavoro attivamente in esecuzione per l'app per la logica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Eseguire operazioni sui dati, ad esempio il filtro di matrici o la creazione di tabelle CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Eseguire operazioni con variabili, ad esempio inizializzazione, set, incremento, decremento e accodare a variabile di stringa o matrice"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi a Hub eventi di Azure. Ricevere e inviare eventi tra app per la logica e hub eventi"
[box-doc]: ./connectors-create-api-box.md "Connettersi a Box. Caricare, recuperare, eliminare ed elencare i file e altro ancora"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Connettersi a Dropbox. Caricare, recuperare, eliminare ed elencare i file e altro ancora"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Connettersi a Dynamics CRM Online per poter usare i dati di CRM Online"
[facebook-doc]: ./connectors-create-api-facebook.md "Connettersi a Facebook. Pubblicare un post in un diario, ottenere il feed di una pagina e altro ancora"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftp-doc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[github-doc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Connects to Google Calendar and can manage calendar" (Connettersi a Google Calendar e gestire il calendario)
[google-drive-doc]: ./connectors-create-api-googledrive.md "Connettersi a Google Drive per poter usare i dati"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Connettersi a Fogli Google per modificare i fogli di lavoro"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Connettersi a Google Tasks per poter gestire le attività"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connetti a app 3270 in mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, recuperare una tabella e altro ancora"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connettersi a IBM MQ in locale o in Azure per inviare e ricevere messaggi"
[instagram-doc]: ./connectors-create-api-instagram.md "Connettersi a Instagram. Attivare eventi o agire su di essi"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Connettersi all'account MailChimp. Gestire e automatizzare i messaggi di posta elettronica"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Connettersi a Microsoft Translator. Tradurre testo, rilevare le lingue e altro ancora" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account Office 365. Inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Ottenere informazioni sui video, canali, elenchi dei video e URL di riproduzione per i video di Office 365"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connettersi all'account Microsoft OneDrive personale. Caricare, eliminare ed elencare i file e altro ancora"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connettersi all'account Microsoft OneDrive aziendale. Caricare, eliminare ed elencare i file e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle per aggiungere, inserire, eliminare righe e altro ancora"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook. Gestire posta elettronica, calendari, contatti e altro ancora"
[project-online-doc]: ./connectors-create-api-projectonline.md "Connettersi a Microsoft Project Online. Gestire progetti, attività, risorse a altro ancora"
[rss-doc]: ./connectors-create-api-rss.md "Pubblicare e recuperare elementi di feed e attivare operazioni quando viene pubblicato un nuovo elemento in un feed RSS"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connettersi all'account Salesforce. Gestire account, lead, opportunità e altro ancora"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire elenchi di destinatari"
[sftp-doc]: ./connectors-create-api-sftp.md "Connettersi all'account SFTP. Caricare, recuperare ed eliminare file e altro ancora"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Connettersi al server locale di SharePoint. Gestire documenti, elementi elenco e altro ancora"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Connettersi a SharePoint Online. Gestire documenti, elementi elenco e altro ancora"
[slack-doc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtp-doc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connettersi a SQL Server o database SQL di Azure. Creare, aggiornare, recuperare ed eliminare voci in una tabella del database SQL"
[trello-doc]: ./connectors-create-api-trello.md "Connettersi a Trello. Gestire i progetti e organizzare qualsiasi cosa con chiunque"
[twilio-doc]: ./connectors-create-api-twilio.md "Connettersi a Twilio. Inviare e ricevere messaggi, ottenere i numeri disponibili, gestire i numeri di telefono in arrivo e altro ancora"
[twitter-doc]: ./connectors-create-api-twitter.md "Connettersi a Twitter. Ottenere cronologie, pubblicare tweet e altro ancora"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Connettersi a Wunderlist. Gestire attività ed elenchi di attività, sincronizzare la propria vita e altro ancora"
[yammer-doc]: ./connectors-create-api-yammer.md "Connettersi a Yammer. Pubblicare messaggi, ottenerne di nuovi e altro ancora"
[youtube-doc]: ./connectors-create-api-youtube.md "Connettersi a YouTube. Gestire video e canali"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Learn about enterprise integration AS2" (Informazioni su Enterprise Integration: AS2)
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Learn about enterprise integration EDIFACT decode" (Informazioni su Enterprise Integration: decodifica EDIFACT)
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Learn about enterprise integration EDIFACT encode" (Informazioni su Enterprise Integration: codifica EDIFACT)
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file" (Informazioni su Enterprise Integration: Flat File)
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file" (Informazioni su Enterprise Integration: Flat File)
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Cercare schemi, mappe, partner e altro ancora nell'account di integrazione"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Informazioni sulle trasformazioni JSON con Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Learn about enterprise integration X12" (Informazioni su Enterprise Integration: X12)
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Learn about enterprise integration X12 decode" (Informazioni su Enterprise Integration: decodifica X12)
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Learn about enterprise integration X12 encode" (Informazioni su Enterprise Integration: codifica X12)
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Learn about enterprise integration transforms" (Informazioni su Enterprise Integration: trasformazioni)
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Learn about enterprise integration XML validation" (Informazioni su Enterprise Integration: convalida XML)

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
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
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
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
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
[sftp-icon]: ./media/apis-list/sftp.png
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
[wunderlist-icon]: ./media/apis-list/wunderlist.png
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
