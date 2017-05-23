---
title: Connettori per App per la logica di Azure | Microsoft Docs
description: Scegliere tra tutti i connettori gestiti da Microsoft disponibili per compilare e creare app per la logica
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/26/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e1e6ef74e7d14b94b2ed9d61c4d122bdb11786a6
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="connectors-list"></a>Elenco dei connettori
> [!TIP]
> La sezione [Elenco completo dalla A alla Z](#az) di questo argomento elenca tutti i connettori disponibili che possono essere usati nelle app per la logica. [Swagger per i connettori](/connectors/) elenca i dettagli Swagger per ogni connettore.

I connettori sono una parte essenziale della creazione di app per la logica. Usando questi connettori, è possibile espandere le applicazioni locali e cloud per eseguire operazioni diverse con i dati creati e con i dati già disponibili. I connettori sono disponibili nelle categorie seguenti: 

* **Connettori standard**: disponibili automaticamente e inclusi quando si usano le app per la logica, ad esempio bus di servizio, DropBox, GoogleDrive, Power BI, Oracle Database, OneDrive e altro ancora.

* **Connettori dell'account di integrazione**: disponibili quando si acquista un account di integrazione. Usando questi connettori, è possibile trasformare e convalidare il codice XML, elaborare messaggi business-to-business con AS2 / X12 / EDIFACT e codificare e decodificare file flat. Se si usa BizTalk Server, questi connettori sono una scelta ideale per espandere i flussi di lavoro di BizTalk in Azure.  

    BizTalk Server include anche un [adapter per app per la logica](https://msdn.microsoft.com/library/mt787163.aspx) che consente di ricevere da un'app per la logica e inviare verso un'app per la logica.

* **Connettori aziendali**: include MQ e SAP. Sono previsti anche costi aggiuntivi. 

Per altri dettagli sui costi, vedere [Prezzi di App per la logica ](https://azure.microsoft.com/pricing/details/logic-apps/) e [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md). 

## <a name="popular-connectors"></a>Connettori più diffusi
Migliaia di applicazioni e milioni di esecuzioni elaborano correttamente dati e informazioni usando questi connettori. La tabella seguente elenca i connettori più diffusi e alcuni connettori preferiti dagli utenti:

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Archiviazione BLOB<br/>di Azure**][AzureBlobStoragedoc] | Per automatizzare le attività relative all'account di archiviazione, è consigliabile usare questo connettore. Supporta le operazioni CRUD (Create, Read, Update, Delete). | [![API Icon][Azure-Functionsicon]<br/>**Funzioni di Azure**][azure-functionsdoc] | Creare funzioni che eseguono frammenti di codice C# o node.js personalizzati e quindi usare queste funzioni nelle app per la logica.  |
| [![API Icon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Questo è uno dei connettori più richiesti. Include trigger e azioni utili per automatizzare i flussi di lavoro con lead e altro ancora. | [![API Icon][Event-Hubs-icon]<br/>**Hub eventi**][event-hubs-doc] | Utilizzare e pubblicare eventi in un hub eventi. È ad esempio possibile ottenere output dall'app per la logica usando gli hub eventi e quindi inviarlo a un provider di analisi in tempo reale. |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | Se il server FTP è accessibile da Internet, è possibile automatizzare i flussi di lavoro per lavorare con file e cartelle. <br/><br/>SFTP è disponibile anche con il connettore SFTP. | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | Usare le app per la logica per comunicare con qualsiasi endpoint su HTTP. |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Numerosi trigger e molte azioni per usare la posta elettronica e gli eventi di Office 365 nei flussi di lavoro. <br/><br/>Questo connettore include un'azione di *messaggio di posta elettronica di approvazione* per approvare richieste di ferie, note spese e così via. <br/><br/>Gli utenti di Office 365 sono disponibili anche con il connettore Utenti di Office 365.| [![API Icon][HTTP-Requesticon]<br/>**Richiesta/risposta**][HTTP-Requestdoc] | Questo connettore fornisce un URL HTTPS. Quando riceve una richiesta per questo URL, l'app per la logica viene avviata. |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | È possibile accedere con facilità con l'account Salesforce per ottenere l'accesso a oggetti quali i lead e altro ancora. |  [![API Icon][Service-Busicon]<br/>**Bus di servizio**][Service-Busdoc] | È il connettore più diffuso per le app per la logica e include trigger e azioni per la messaggistica asincrona e per la pubblicazione/sottoscrizione con code, sottoscrizioni e argomenti. |
|  [![API Icon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Se si lavora con SharePoint e l'automazione potrebbe risultare utile, è consigliabile prendere in considerazione questo connettore. Può essere usato con SharePoint locale e SharePoint Online. | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Uno dei connettori più usati, consente di connettersi a un'istanza locale di SQL Server e a un database SQL di Azure. | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Accedere con facilità con un account Twitter e quindi avviare un flusso di lavoro quando viene inserito un nuovo tweet. Salvare quindi i tweet in un database SQL o in un elenco di SharePoint. | | | 

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione 

Enterprise Integration Pack (EIP) include connettori noti alla community di BizTalk Server. Quando si acquista un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), si ottengono anche i connettori seguenti: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**Decodifica</br> AS2**][as2decode] | [![API Icon][as2icon]<br/>**Codifica</br> AS2**][as2encode] | [![API Icon][x12icon]<br/>**Decodifica</br> EDIFACT**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**Codifica</br> EDIFACT**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**Codifica</br>file flat**][flatfiledoc] | [![API Icon][flatfileicon]<br/>**Decodifica</br>file flat**][flatfiledecodedoc] | [![API Icon][integrationaccounticon]<br/>**Integrazione<br/>account**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**Trasforma<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**Decodifica</br> X12**][x12decode] | [![API Icon][x12icon]<br/>**Codifica</br> X12**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**Convalida <br/>XML**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>Connettori aziendali

È possibile connettersi alle applicazioni aziendali all'interno delle app per la logica.

|  |  |
| --- | --- |
|![Icona API][MQicon]<br/>**MQ**|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>Elenco completo dalla A alla Z

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 Appointment Scheduling<br/><br/><a name="a"></a>Act!<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory<br/>Gestione API di Azure<br/>Servizi app di Azure<br/>Automazione di Azure<br/>[Archiviazione BLOB di Azure][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure Cosmos DB<br/>[Funzioni di Azure][azure-functionsdoc]<br/>[App per la logica di Azure][nested-logic-appdoc]<br/>AzureML<br/>Code di Azure<br/>Gestione risorse di Azure<br/>[Database SQL di Azure][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Benchmark Email<br/>Ricerca Bing<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Campfire<br/>Capsule CRM<br/>Chatter<br/>API Visione artificiale di Servizi cognitivi<br/>API Viso di Servizi cognitivi<br/>LUIS di Servizi cognitivi<br/>Analisi del testo di Servizi cognitivi<br/>Common Data Service<br/>[API/app Web personalizzate][api/web-appdoc]<br/><br/><a name="d"></a>Operazioni dati<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Hub eventi][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[File System][filesystemdoc]<br/>[File flat][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google Calendar<br/>Google Contacts<br/>Google Drive<br/>Google Sheets<br/>Google Tasks<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[Webhook HTTP][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>Account di integrazione<br/>Intercom | <a name="j"></a>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>Media<br/>Microsoft Translator<br/>MQ<br/>MSN Meteo<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Utenti di Office 365<br/>Office 365 Video<br/>OneDrive<br/>OneDrive for Business<br/>OneNote (Business)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook Customer<br/>Attività di Outlook<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[Richiesta/Risposta][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[Server applicazioni SAP][sapconnector]<br/>[Server messaggi SAP][sapconnector]<br/>[Pianificare][recurrencedoc]<br/>Scope<br/>SendGrid<br/>[Bus di servizio][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointdoc]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>Switch Case<br/>SurveyMonkey<br/><br/><a name="t"></a>Teradata<br/>Todoist<br/>Toodledo<br/>[Trasformare XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>Variabili<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[Convalida XML][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Per iniziare a usare le app per la logica di Azure prima di creare un account di Azure, vedere [Provare le app per la logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare immediatamente un'app per la logica di base temporanea. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## <a name="connectors-as-triggers-and-actions"></a>Connettori come trigger e azioni

Un **trigger** avvia o esegue un'istanza dell'app per la logica. Alcuni connettori forniscono trigger che possono inviare notifiche all'app quando si verifica un evento specifico. Il connettore FTP include ad esempio il trigger `OnUpdatedFile` che avvia l'app per la logica quando viene aggiornato un file. 

Le app per la logica includono i tipi di trigger seguenti:  

* *Trigger di polling*: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. 

    Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app per la logica con i dati come input. 

* *Trigger di push*: questi trigger restano in attesa dei dati su un endpoint o di un evento, quindi attivano una nuova istanza dell'app per la logica.

* *Trigger di ricorrenza*: questo trigger crea istanze di un'app per la logica in base a una pianificazione predefinita.

I connettori forniscono anche **azioni** che è possibile usare nel flusso di lavoro dell'applicazione. L'app per la logica ad esempio può cercare dati e quindi usarli in seguito nell'app per la logica. È in particolare possibile cercare dati dei clienti da un database SQL e quindi usare i dati dei clienti per creare un flusso di lavoro. 

> [!TIP]
> Per altri dettagli su trigger e azioni, vedere [Panoramica dei connettori](connectors-overview.md). 


## <a name="message-manipulation-actions"></a>Azioni di modifica dei messaggi

Le app per la logica includono azioni predefinite che consentono di modificare i dati del payload. Il connettore predefinito **Operazioni dati**: 

| | |
|---|---|
| **Componi** | È possibile creare o generare valori oppure oggetti da usare in seguito o durante la creazione del flusso di lavoro. È ad esempio possibile creare un oggetto JSON con valori da più passaggi oppure calcolare una costante a cui fare riferimento in seguito in un'esecuzione di app per la logica. |
| **Crea tabella CSV**<br/>**Crea tabella HTML** | È possibile trasformare un set di risultati di matrice in una tabella CSV o HTML. Aggiungere ad esempio l'azione "Elenca i record" di CRM e aggiungere un filtro per i record aggiunti oggi. Inviare quindi i risultati come tabella HTML in un messaggio di posta elettronica. |
| **Filtra matrice** (query) | È possibile filtrare un set di risultati per visualizzare le voci più rilevanti per l'utente. È ad esempio possibile cercare tutti i tweet con `#Azure` e quindi "filtrare" i tweet restituiti per poter visualizzare solo i risultati di tipo `Tweeted_by_followers > 50`. |
| **Join** | È possibile unire una matrice mediante un delimitatore. L'operazione di rilevamento delle frasi chiave restituisce una matrice di frasi chiave. È possibile "unirle" con `,` o un approccio simile. Invece di `["Some", "Phrase"]` si ottiene `"Some, Phrase"`. |
| **Analizza JSON** | È possibile escludere tramite analisi e accedere a valori da un oggetto JSON nella finestra di progettazione. Se ad esempio Funzioni di Azure restituisce un payload JSON, è possibile analizzarlo per accedere alle proprietà JSON in un passaggio successivo. L'azione convalida anche la corrispondenza di JSON con lo schema specificato in fase di runtime. | 
| **Selezionare** | Selezionare alcune proprietà di una matrice per un'elaborazione aggiuntiva. Se si elencano record da SQL e vengono restituite 15 colonne, selezionare solo alcune colonne per un'elaborazione aggiuntiva. L'output è una matrice che contiene solo le proprietà selezionate. |

## <a name="custom-connectors-and-azure-certification"></a>Connettori personalizzati e certificazione per Azure 

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API basate su REST come connettori personalizzati](../logic-apps/logic-apps-create-api-app.md). 

Se si vuole rendere pubbliche le app per le API personalizzate e renderle disponibile per l'uso in Azure, inviare le candidature al [programma Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti delle app per la logica di Azure, vedere il [forum sulle app per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento di App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi
* [Creare la prima app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)
* [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrare le app per la logica con le app per le API del servizio app"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[db2doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, recuperare una tabella e altro ancora"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Connettersi a Dynamics CRM Online per poter usare i dati di CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi a Hub eventi di Azure. Ricevere e inviare eventi tra app per la logica e hub eventi"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftpdoc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[httpdoc]: ./connectors-native-http.md "Effettuare chiamate HTTP con il connettore HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Aggiungere azioni per richieste e risposte HTTP alle app per la logica"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Effettuare chiamate HTTP con il connettore HTTP e Swagger"
[informixdoc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account Office 365. Inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle per aggiungere, inserire, eliminare righe e altro ancora"
[recurrencedoc]:  ./connectors-native-recurrence.md "Attivare azioni ricorrenti per le app per la logica"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Connettersi all'account Salesforce. Gestire account, lead, opportunità e altro ancora"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[service-busdoc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Connettersi a SharePoint Online. Gestire documenti, elementi elenco e altro ancora"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Connettersi a SQL Server o database SQL di Azure. Creare, aggiornare, recuperare ed eliminare voci in una tabella del database SQL"
[twitterdoc]: ./connectors-create-api-twitter.md "Connettersi a Twitter. Ottenere cronologie, pubblicare tweet e altro ancora"
[webhookdoc]: ./connectors-native-webhook.md "Aggiungere azioni e trigger webhook alle app per la logica"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Learn about enterprise integration AS2" (Informazioni su Enterprise Integration: AS2)
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Learn about enterprise integration X12" (Informazioni su Enterprise Integration: X12)
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file" (Informazioni su Enterprise Integration: Flat File)
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Learn about enterprise integration flat file" (Informazioni su Enterprise Integration: Flat File)
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Learn about enterprise integration XML validation" (Informazioni su Enterprise Integration: convalida XML)
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Learn about enterprise integration transforms" (Informazioni su Enterprise Integration: trasformazioni)
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Learn about enterprise integration AS2 decode" (Informazioni su Enterprise Integration: decodifica AS2)
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Learn about enterprise integration AS2 encode" (Informazioni su Enterprise Integration: codifica AS2)
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Learn about enterprise integration X12 decode" (Informazioni su Enterprise Integration: decodifica X12)
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Learn about enterprise integration X12 encode" (Informazioni su Enterprise Integration: codifica X12)
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Learn about enterprise integration EDIFACT decode" (Informazioni su Enterprise Integration: decodifica EDIFACT)
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Learn about enterprise integration EDIFACT encode" (Informazioni su Enterprise Integration: codifica EDIFACT)
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Cercare schemi, mappe, partner e altro ancora nell'account di integrazione"


[boxDoc]: ./connectors-create-api-box.md "Connettersi a Box. Caricare, recuperare, eliminare ed elencare i file e altro ancora"
[delaydoc]: ./connectors-native-delay.md "Eseguire azioni di ritardo"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Connettersi a Dropbox. Caricare, recuperare, eliminare ed elencare i file e altro ancora"
[facebookdoc]: ./connectors-create-api-facebook.md "Connettersi a Facebook. Pubblicare un post in un diario, ottenere il feed di una pagina e altro ancora"
[githubdoc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Connettersi a Google Drive per poter usare i dati"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Connettersi a Fogli Google per poter modificare i fogli di lavoro"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Connettersi a Google Tasks per poter gestire le attività"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Connects to Google Calendar and can manage calendar" (Connettersi a Google Calendar e gestire il calendario)
[http-responsedoc]: ./connectors-native-reqres.md "Aggiungere azioni per richieste e risposte HTTP alle app per la logica"
[instagramdoc]: ./connectors-create-api-instagram.md "Connettersi a Instagram. Attivare eventi o agire su di essi"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Connettersi all'account MailChimp. Gestire e automatizzare i messaggi di posta elettronica"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Connettersi a Microsoft Translator. Tradurre testo, rilevare le lingue e altro ancora" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Ottenere informazioni sui video, canali, elenchi dei video e URL di riproduzione per i video di Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Connettersi all'account Microsoft OneDrive personale. Caricare, eliminare ed elencare i file e altro ancora"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Connettersi all'account Microsoft OneDrive aziendale. Caricare, eliminare ed elencare i file e altro ancora"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook. Gestire posta elettronica, calendari, contatti e altro ancora"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Connettersi a Microsoft Project Online. Gestire progetti, attività, risorse a altro ancora"
[querydoc]: ./connectors-native-query.md "Selezionare e filtrare matrici con l'azione di query"
[rssdoc]: ./connectors-create-api-rss.md "Pubblicare e recuperare elementi di feed e attivare operazioni quando viene pubblicato un nuovo elemento in un feed RSS"
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire elenchi di destinatari"
[sftpdoc]: ./connectors-create-api-sftp.md "Connettersi all'account SFTP. Caricare, recuperare ed eliminare file e altro ancora"
[slackdoc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtpdoc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[trellodoc]: ./connectors-create-api-trello.md "Connettersi a Trello. Gestire i progetti e organizzare qualsiasi cosa con chiunque"
[twiliodoc]: ./connectors-create-api-twilio.md "Connettersi a Twilio. Inviare e ricevere messaggi, ottenere i numeri disponibili, gestire i numeri di telefono in arrivo e altro ancora"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Connettersi a Wunderlist. Gestire attività ed elenchi di attività, sincronizzare la propria vita e altro ancora"
[yammerdoc]: ./connectors-create-api-yammer.md "Connettersi a Yammer. Pubblicare messaggi, ottenerne di nuovi e altro ancora"
[youtubedoc]: ./connectors-create-api-youtube.md "Connettersi a YouTube. Gestire video e canali"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png

