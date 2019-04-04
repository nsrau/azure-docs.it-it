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
ms.date: 08/23/2018
ms.openlocfilehash: 59c8effb4c5feae99755b7937f4796e8f11fde46
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895884"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

I connettori consentono di accedere rapidamente dall'App per la logica di Azure per gli eventi, dati e le azioni in altre App, servizi, i sistemi, protocolli e piattaforme. Utilizzando i connettori nelle App per la logica, si espandono le funzionalità per le app cloud e locali eseguire attività con i dati che creano e si dispone già di.

Mentre App per la logica offre [oltre 200 connettori](https://docs.microsoft.com/connectors), questo articolo descrive i connettori più popolari e più comuni che vengono usati con successo da migliaia di applicazioni e milioni di esecuzioni per l'elaborazione di dati e informazioni. Per trovare l'elenco completo dei connettori e informazioni di riferimento ogni connettore, come ad esempio trigger, azioni e i limiti, rivedere il connettore di fare riferimento a pagine nelle [panoramica dei connettori](https://docs.microsoft.com/connectors). Anche le informazioni sulle [trigger e azioni](#triggers-actions).

> [!NOTE]
> Per integrare con un servizio o l'API che non dispone di connettore, è possibile direttamente chiamare il servizio tramite un protocollo come HTTP o crearne una [connettore personalizzato](#custom).

I connettori sono disponibili come azioni e trigger predefiniti o come connettori gestiti:

* [**Incorporati**](#built-ins): Questi trigger e azioni predefinite sono "nativo" per le App per la logica di Azure e consentono di che creare App per la logica che Esegui in base a pianificazioni personalizzate, di comunicare con altri endpoint, ricevere e rispondere alle richieste e chiamare funzioni di Azure, Azure API App (app Web), le tue API gestito e pubblicato con gestione API di Azure e App per la logica annidata che possono ricevere le richieste. È inoltre possibile utilizzare azioni predefinite che consentono di organizzare e controllare il flusso di lavoro dell'applicazione logica e anche di lavorare con i dati.

* **Connettori gestiti**: Distribuite e gestite da Microsoft, questi connettori forniscono trigger e azioni per l'accesso di altri servizi e sistemi, ad esempio Office 365, archiviazione Blob di Azure, SQL Server, Salesforce e altro ancora. Alcuni connettori è necessario innanzitutto creare connessioni, che vengono gestite dall'App per la logica di Azure. I connettori gestiti sono organizzati in questi gruppi:

  |   |   |
  |---|---|
  | [**Connettori delle API gestiti**](#managed-api-connectors) | Consentono di creare app per la logica che usano i servizi, ad esempio archiviazione BLOB di Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e molti altri. | 
  | [**Connettori locali**](#on-premises-connectors) | Dopo aver installato e configurato il [gateway dati locale][gateway-doc], questi connettori consentono alle app per la logica di accedere a sistemi locali quali SQL Server, SharePoint Server, Oracle DB, condivisioni di file e altri. | 
  | [**Connettori dell'account di integrazione**](#integration-account-connectors) | Disponibili quando si crea e si paga un account di integrazione, questi connettori trasformano e convalidano XML, codificano e decodificano file flat ed elaborano messaggi business-to-business (B2B) con i protocolli AS2, EDIFACT e X12. | 
  | [**Connettori aziendali**](#enterprise-connectors) | Forniscono l'accesso a sistemi aziendali come SAP e IBM MQ a un costo aggiuntivo. |
  ||| 

  Ad esempio, se si usa Microsoft BizTalk Server, le App per la logica possono connettersi e comunicare con BizTalk Server tramite il [connettore BizTalk Server](#on-premises-connectors). 
  È quindi possibile estendere o eseguire operazioni di tipo BizTalk in App per la logica utilizzando [connettori dell'account di integrazione](#integration-account-connectors). 

> [!NOTE]
> Per l'elenco di connettori completo e le informazioni di riferimento per ogni connettore, ad esempio azioni e tutti i trigger definiti da una descrizione Swagger e qualsiasi limite, è possibile trovare l'elenco completo nella [Panoramica dei connettori](/connectors/). Per informazioni sui prezzi, vedere [Dettagli prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/) e [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Predefiniti

App per la logica offre trigger e azioni predefiniti che consentono di creare flussi di lavoro basati sulla pianificazione, aiutare le applicazioni logiche a comunicare con altre applicazioni e servizi, controllare il flusso di lavoro attraverso le app per la logica e gestire o manipolare i dati. 

|   |   |   |   | 
|---|---|---|---| 
| [![Icona API][schedule-icon]<br/>**Pianificazione**][recurrence-doc] | - Eseguire l'app per la logica su una pianificazione specificata, che varia da ricorrenze di base a ricorrenze complesse, con il pulsante **Ricorrenza**. <p>- Mettere in pausa l'app per la logica per una durata specificata con l'azione **Ritarda**. <p>- Mettere in pausa l'app per la logica fino alla data e all'ora specificata con l'azione **Ritarda fino a**. | [![Icona API][http-icon]<br/>**HTTP**][http-doc] | Comunicare con qualsiasi endpoint tramite HTTP con trigger e azioni per HTTP, HTTP + Swagger e HTTP + Webhook. | 
| [![Icona API][http-request-icon]<br/>**Richiesta**][http-request-doc] | - Rendere l'app per la logica richiamabile da altre app o servizi, attiva gli eventi delle risorse della griglia di eventi o attiva le risposte agli avvisi di Centro sicurezza con il pulsante **Richiesta**. <p>- Inviare risposte a un'app o a un servizio con l'azione **Risposta**. | [![Icona API][batch-icon]<br/>**Batch**][batch-doc] | - Elaborare i messaggi in batch con il trigger **Messaggi batch**. <p>- Richiamare le app per la logica con trigger di batch esistenti tramite l'azione **Invia messaggi al batch**. | 
| [![Icona API][azure-functions-icon]<br/>**Funzioni di Azure**][azure-functions-doc] | Richiamare le funzioni Azure che eseguono frammenti di codice personalizzati (C# o Node.js) dalle app per la logica. | [![Icona API][azure-api-management-icon]</br>**Gestione API di Azure**][azure-api-management-doc] | Richiamare trigger e azioni definite dalle proprie API che vengono gestite e pubblicate con Azure API Management. | 
| [![Icona API][azure-app-services-icon]<br/>**Servizi app di Azure**][azure-app-services-doc] | Chiamare l'App per le API Azure o le app Web, ospitato nel servizio App di Azure. I trigger e le azioni definiti da queste app appaiono come qualsiasi altro trigger e azione di prima classe quando Swagger è incluso. | [![Icona API][azure-logic-apps-icon]<br/>**App per la logica<br/>di Azure**][nested-logic-app-doc] | Chiamare altre App per la logica che iniziano con un trigger di richiesta. | 
||||| 

### <a name="control-workflow"></a>Controllare il flusso di lavoro

Ecco le azioni predefinite per strutturare e controllare le azioni nel flusso di lavoro dell'app per la logica:

|   |   |   |   | 
|---|---|---|---| 
| [![Icona predefinita][condition-icon]<br/>**Condizione**][condition-doc] | Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false. | [![Icona predefinita][for-each-icon]</br>**Per ognuno**][for-each-doc] | Eseguire le stesse azioni su ogni elemento in una matrice. | 
| [![Icona predefinita][scope-icon]<br/>**Ambito**][scope-doc] | Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito. | [![Icona predefinita][switch-icon]</br>**Commutatore**][switch-doc] | Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito. | 
| [![Icona predefinita][terminate-icon]<br/>**Termina**][terminate-doc] | Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva. | [![Icona predefinita][until-icon]<br/>**Fino a**][until-doc] | Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

Ecco le azioni predefinite per l'utilizzo degli output di dati e i relativi formati:  

|   |   | 
|---|---| 
| [![Icona predefinita][data-operations-icon]<br/>**Operazioni dati**][data-operations-doc] | Eseguire operazioni con i dati: <p>- **Comporre**: Creare un singolo output da più input con tipi diversi. <br>- **Crea tabella CSV**: Creare una tabella di valori delimitati da virgole (CSV) da una matrice di oggetti JSON. <br>- **Crea tabella HTML**: Creare una tabella HTML da una matrice di oggetti JSON. <br>- **Filtra matrice**: Creare una matrice da elementi che soddisfano i criteri in un'altra matrice. <br>- **Join**: Creare una stringa da tutti gli elementi in una matrice e separare gli elementi con il delimitatore specificato. <br>- **Analizzare JSON**: Creare token descrittivi da proprietà e i relativi valori nel contenuto JSON in modo che è possibile usare tali proprietà nel flusso di lavoro. <br>- **Selezionare**: Creare una matrice con gli oggetti JSON da trasformare gli elementi o valori in un'altra matrice ed eseguire il mapping di tali elementi per le proprietà specificate. | 
| ![Icona predefinita di][date-time-icon]<br/>**Data/Ora** | Eseguire operazioni con i timestamp: <p>- **Aggiungere ora**: Aggiungere il numero di unità specificato a un timestamp. <br>- **Converti fuso orario**: Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. <br>- **Ora corrente**: Restituisce il timestamp corrente come stringa. <br>- **Recupera ora futura**: Restituisce il timestamp corrente più le unità di tempo specificate. <br>- **Recupera ora precedente**: Restituisce il timestamp corrente meno le unità di tempo specificate. <br>- **Sottrarre dall'ora**: Sottrae un numero di unità di tempo da un timestamp. |
| [![Icona predefinita][variables-icon]<br/>**Variabili**][variables-doc] | Eseguire operazioni con le variabili: <p>- **Accoda a variabile di matrice**: Inserire un valore come l'ultimo elemento nella matrice archiviata da una variabile. <br>- **Accoda a variabile di stringa**: Inserire un valore come l'ultimo carattere in una stringa archiviata da una variabile. <br>- **Decrementa variabile**: Ridurre una variabile di un valore costante. <br>- **Incrementa variabile**: Aumentare una variabile da un valore costante. <br>- **Inizializza variabile**: Creare una variabile e dichiarare il tipo di dati e il valore iniziale. <br>- **Impostare la variabile**: Assegnare un valore diverso in una variabile esistente. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Connettori delle API gestiti

Di seguito sono riportati i connettori più popolari per automatizzare attività, processi e flussi di lavoro con questi servizi o sistemi:

|   |   |   |   | 
|---|---|---|---| 
| [![Icona API][azure-service-bus-icon]<br/>**Bus di servizio di Azure**][azure-service-bus-doc] | Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica. | [![Icona API][sql-server-icon]<br/>**SQL** Server][sql-server-doc] | Connettersi all'account e-mail di Office 365 per creare e gestire e-mail, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora. | 
| [![Icona API][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Connettersi all'account e-mail di Office 365 per creare e gestire e-mail, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora. | [![Icona API][azure-blob-storage-icon]<br/>**Archiviazione BLOB<br/>di Azure**][azure-blob-storage-doc] | Connettersi all'account di archiviazione in modo che sia possibile creare e gestire il contenuto del BLOB. | 
| [![Icona API][sftp-icon]<br/>**SFTP**][sftp-doc] | Connettersi ai server SFTP a cui è possibile accedere da Internet per lavorare con file e cartelle. | [![Icona API][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Connettersi a SharePoint Online in modo che sia possibile gestire i file, allegati, cartelle e altro ancora. | 
| [![Icona API][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Connettersi all'account di Dynamics 365 in modo che sia possibile creare e gestire i record, elementi e altro ancora. | [![Icona API][ftp-icon]<br/>**FTP**][ftp-doc] | Connettersi ai server FTP a cui è possibile accedere da Internet per lavorare con file e cartelle. | 
| [![Icona API][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Connettersi all'account Salesforce in modo che sia possibile creare e gestire elementi quali record, processi, oggetti e altro ancora. | [![Icona API][twitter-icon]<br/>**Twitter**][twitter-doc] | Connettersi all'account Twitter in modo che sia possibile gestire tweet, follower, la sequenza temporale e altro ancora. Salvare i tweet in Excel, SQL o SharePoint. | 
| [![Icona API][azure-event-hubs-icon]<br/>**Hub eventi di Azure**][azure-event-hubs-doc] | Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale. | [![Icona API][azure-event-grid-icon]<br/>**Griglia** eventi di Azure</br>][azure-event-grid-doc] | Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connettori locali 

Ecco alcuni connettori di uso comune che forniscono l'accesso ai dati e alle risorse nei sistemi locali. Prima di creare una connessione a un sistema locale, è necessario innanzitutto [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![Icona API][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![Icona API][file-system-icon]<br/>**File</br> system**][file-system-doc] | [![Icona API][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![Icona API][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![Icona API][mysql-icon]<br/>**MySQL** | 
| [![Icona API][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![Icona API][postgre-sql-icon]<br/>**PostgreSQL** | [![Icona API][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![Icona API][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![Icona API][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione 

Di seguito sono riportati i connettori per la creazione di soluzioni business-to-business (B2B) con le app per la logica quando si crea e si paga un [ account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), disponibile tramite l'Enterprise Integration Pack (EIP) di Azure. Con questo account, è possibile creare e archiviare artefatti B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via. Per usare questi artefatti, associare le app per la logica con l'account di integrazione. Se attualmente si usa BizTalk Server, questi connettori potrebbero sembrare già familiari.

|   |   |   |   | 
|---|---|---|---| 
| [![Icona API][as2-icon]<br/>**Decodifica</br> AS2**][as2-decode-doc] | [![Icona API][as2-icon]<br/>**Codifica</br> AS2**][as2-encode-doc] | [![Icona API][edifact-icon]<br/>**Decodifica</br> EDIFACT**][edifact-decode-doc] | [![Icona API][edifact-icon]<br/>**Codifica</br> EDIFACT**][edifact-encode-doc] | 
| [![Icona API][flat-file-decode-icon]<br/>**Decodifica</br> file flat**][flat-file-decode-doc] | [![Icona API][flat-file-encode-icon]<br/>**Codifica</br> file flat**][flat-file-encode-doc] | [![Icona API][integration-account-icon]<br/>**Account<br/> di integrazione**][integration-account-doc] | [![Icona API][liquid-icon]<br/>**Trasformazioni**</br>**Liquid**][json-liquid-transform-doc] | 
| [![Icona API][x12-icon]<br/>**Decodifica</br> X12**][x12-decode-doc] | [![Icona API][x12-icon]<br/>**Codifica</br> X12**][x12-encode-doc] | [![Icona API][xml-transform-icon]<br/>**Trasforma**</br>**XML**][xml-transform-doc] | [![Icona API][xml-validate-icon]<br/>**Convalida <br/>XML**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connettori aziendali

App per la logica può accedere a sistemi aziendali, come SAP e IBM MQ:

|   |   | 
|---|---| 
| [![Icona API][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![Icona API][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Trigger e azioni - altre informazioni

I connettori possono fornire *trigger*, *azioni*, o entrambi. Oggetto *trigger* è il primo passaggio in qualsiasi app per la logica, in genere che specifica l'evento che attiva il trigger e avvia l'esecuzione di app per la logica. Ad esempio, il connettore FTP include un trigger che avvia l'app per la logica "quando viene aggiunto o modificato un file". Alcuni trigger da controllare regolarmente l'evento specificato o dei dati e quindi vengono attivati quando viene rilevato l'evento specificato o dei dati. Altri trigger di attesa, ma vengono attivati immediatamente quando si verifica un evento specifico oppure quando sono disponibili nuovi dati. I trigger passare anche i dati necessari per l'app per la logica. App per la logica può leggere e usare tali dati in tutto il flusso di lavoro.
Ad esempio, il connettore Twitter supporta un trigger "Quando un nuovo tweet viene pubblicato", che passa i tweet del contenuto nel flusso di lavoro dell'app per la logica. 

Dopo che viene attivato un trigger, le App per la logica di Azure crea un'istanza dell'app per la logica e avvia l'esecuzione di *azioni* nel flusso di lavoro dell'app per la logica. Le azioni sono i passaggi che seguono il trigger ed eseguono attività nel flusso di lavoro dell'app per la logica. Ad esempio, è possibile creare un'app per la logica che ottiene i dati dei clienti da un database SQL ed elaborare tali dati in azioni successive. 

Ecco i tipi generali di trigger che fornisce le App per la logica di Azure:

* *Trigger di ricorrenza*: Questo trigger viene eseguito su una pianificazione specificata e non è strettamente associato a un determinato servizio o un sistema.

* *Trigger di poll*: Questo trigger esegue regolarmente il polling un servizio specifico o un sistema in base alla pianificazione specificata, il controllo per nuovi dati o se si è verificato un evento specifico. Se sono disponibili nuovi dati o si è verificato l'evento specifico, il trigger crea ed esegue una nuova istanza dell'app per la logica, che è ora possibile usare i dati che vengono passati come input.

* *Trigger di push*: Questo trigger attende e resta in ascolto per i nuovi dati o di un evento. Quando sono disponibili nuovi dati o quando si verifica l'evento, il trigger crea ed esegue nuova istanza dell'app per la logica, che è ora possibile usare i dati che vengono passati come input.

<a name="custom"></a>

## <a name="connector-configuration"></a>Configurazione del connettore

Trigger e azioni di ogni connettore forniscono le proprie proprietà per la configurazione. Molti connettori richiedono anche creare prima di tutto una *connessione* per il servizio di destinazione o il sistema e fornire le credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o azione nelle app per la logica. Ad esempio, è necessario autorizzare una connessione a un account Twitter per l'accesso ai dati o la registrazione per tuo conto. 

Per i connettori che usano OAuth, creazione di una connessione significa accedere al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso è crittografato e archiviato in modo sicuro in un archivio segreto di Azure. Gli altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, nome utente e password. I dettagli di questa configurazione di connessione vengono inoltre crittografati e archiviati in modo sicuro. 

Connessioni possono accedere al servizio di destinazione o di un sistema per fino a quando tale servizio o il sistema consente. Per i servizi che usano le connessioni OAuth di Azure Active Directory (AD), ad esempio Office 365 e Dynamics, App per la logica di Azure consente di aggiornare i token di accesso per un periodo illimitato. Altri servizi potrebbero imporre limiti su quanto tempo le App per la logica di Azure può usare un token senza aggiornamento. In genere, alcune azioni invalidano tutti i token di accesso, ad esempio la modifica della password.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per *qualsiasi* API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure.
Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Per sottoporre o votare idee per Azure Logic Apps e connettori, visitare il [sito di feedback degli utenti di App per la logica](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* Cercare l'[elenco completo di connettori](https://docs.microsoft.com/connectors)
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare connettori personalizzati per App per la logica](https://docs.microsoft.com/connectors/custom-connectors/)
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
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Learn about enterprise integration AS2 decode" (Informazioni su Enterprise Integration: decodifica AS2)
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Learn about enterprise integration AS2 encode" (Informazioni su Enterprise Integration: codifica AS2)
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
