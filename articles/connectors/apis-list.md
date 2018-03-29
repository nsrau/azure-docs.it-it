---
title: Connettori per App per la logica di Azure | Microsoft Docs
description: Scegliere tra tutti i connettori Microsoft disponibili per compilare e creare app per la logica
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: estfan; ladocs
ms.openlocfilehash: a17d887e829252231e0f2e0bac137bd63a24e0d9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="connectors-list"></a>Elenco dei connettori
Per trovare i trigger e le azioni definiti dalla descrizione Swagger di ogni connettore e gli eventuali limiti dei connettori, vedere le [informazioni dettagliate sui connettori](/connectors/).

I connettori sono una parte essenziale della creazione di app per la logica. Usando questi connettori, è possibile espandere le applicazioni locali e cloud per eseguire varie operazioni con i dati creati e con quelli già disponibili. I connettori sono disponibili come azioni predefinite o come connettori gestiti.

**Azioni predefinite**: il motore di App per la logica offre azioni predefinite per la comunicazione con gli endpoint e l'esecuzione di attività. Ad esempio, è possibile usare queste azioni per chiamare endpoint HTTP, Funzioni di Azure e operazioni di Gestione API di Azure, nonché per modificare messaggi con variabili e operazioni sui dati.

**Connettori gestiti**: consentono di accedere alle API per vari servizi creando connessioni API ospitate e gestite dal servizio App per la logica. I connettori gestiti si suddividono nelle categorie seguenti.

* **Connettori standard**: disponibili automaticamente e inclusi quando si usano le app per la logica, ad esempio il bus di servizio, Power BI, OneDrive e altro ancora.

* **Connettori locali**: stabiliscono la connessione con applicazioni server locali usando il [gateway dati locale][gatewaydoc]. I connettori locali includono la connettività con applicazioni server come SharePoint Server, SQL Server, Oracle DB, condivisioni file e altre ancora.

* **Connettori dell'account di integrazione**: disponibili quando si acquista un account di integrazione. Usando questi connettori, è possibile trasformare e convalidare il codice XML, elaborare messaggi business-to-business con AS2 / X12 / EDIFACT e codificare e decodificare file flat. Se si usa BizTalk Server, questi connettori sono una scelta ideale per espandere i flussi di lavoro di BizTalk in Azure.  

    BizTalk Server include anche un [adapter per app per la logica](https://msdn.microsoft.com/library/mt787163.aspx) che consente di ricevere da un'app per la logica e inviare verso un'app per la logica.

* **Connettori aziendali**: include MQ e SAP. Disponibili a un costo aggiuntivo. 

Per altre informazioni sui costi, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/logic-apps/) e il [modello di determinazione prezzi](../logic-apps/logic-apps-pricing.md) per App per la logica. 

## <a name="popular-connectors"></a>Connettori più diffusi
Migliaia di applicazioni e milioni di esecuzioni elaborano correttamente dati e informazioni usando questi connettori. 

### <a name="built-in-actions"></a>Azioni predefinite
Il motore di App per la logica offre azioni che consentono di modificare i dati, comunicare tramite HTTP e controllare il flusso della definizione delle app per la logica. Di seguito sono riportate alcune di queste azioni.

| |  |  |  |
| --- | --- | --- | --- |
| [![Icona API][HTTPicon]<br/>**HTTP**][httpdoc] | Usare le app per la logica per comunicare con qualsiasi endpoint su HTTP.| [![Icona API][Azure-Functionsicon]<br/>**Funzioni di Azure**][azure-functionsdoc] | Creare funzioni che eseguono frammenti di codice C# o node.js personalizzati e quindi usare queste funzioni nelle app per la logica.  |
| [![Icona API][HTTP-Requesticon]<br/>**Richiesta**][HTTP-Requestdoc] | Offre un URL HTTPS chiamabile in genere usato come webhook in altre applicazioni. Quando riceve una richiesta per questo URL, l'app per la logica viene avviata. | [![Icona API][Recurrenceicon]<br/>**Pianificazione**][recurrencedoc] | Avviare le app per la logica in base a pianificazioni di ricorrenza semplici o complesse. Ad esempio, creare pianificazioni che prevedono dalla semplice ricorrenza ogni giorno alla ricorrenza ogni ora dell'ultimo venerdì di ogni mese tra le 9:00 e le 17:00. |
| [![Icona API][CallLogicApp-icon]<br/>**Chiamata<br/>di app per la logica**][nested-logic-appdoc] | Chiamare un'app per la logica annidata. Qualsiasi app per la logica con un trigger di richiesta può essere chiamata come app per la logica annidata.| [![Icona API][API/Web-Appicon]<br/>**App per le API**][api/web-appdoc] | Chiamare un'app per le API del servizio app. Il rendering delle app per le API con Swagger viene eseguito così come per le altre azioni di prima classe.|

### <a name="standard-connectors"></a>Connettori Standard
La tabella seguente elenca i connettori più diffusi e alcuni connettori preferiti dagli utenti:

| |  |  |  |
| --- | --- | --- | --- |
| [![Icona API][AzureBlobStorageicon]<br/>**BLOB di Azure<br/>Archiviazione**][AzureBlobStoragedoc] | Per automatizzare le attività relative all'account di archiviazione, è consigliabile usare questo connettore. Supporta le operazioni CRUD (Create, Read, Update, Delete). | [![Icona API][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Uno dei connettori più richiesti. Include trigger e azioni utili per automatizzare i flussi di lavoro con lead e altro ancora. |
| [![Icona API][Event-Hubs-icon]<br/>**Hub eventi**][event-hubs-doc] | Utilizzare e pubblicare eventi in un hub eventi. È ad esempio possibile ottenere output dall'app per la logica usando gli hub eventi e quindi inviare l'output a un provider di analisi in tempo reale. | [![Icona API][FTPicon]<br/>**FTP**][FTPdoc] | Se il server FTP è accessibile da Internet, è possibile automatizzare i flussi di lavoro per lavorare con file e cartelle. <br/><br/>SFTP è disponibile anche con il connettore SFTP. |
| [![Icona API][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Numerosi trigger e molte azioni per usare la posta elettronica e gli eventi di Office 365 nei flussi di lavoro. <br/><br/>Questo connettore include un'azione di *messaggio di posta elettronica di approvazione* per approvare richieste di ferie, note spese e così via. <br/><br/>Gli utenti di Office 365 sono disponibili anche con il connettore Utenti di Office 365.| [![Icona API][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | È possibile accedere con facilità con l'account Salesforce per ottenere l'accesso a oggetti quali i lead e altro ancora. | 
| [![Icona API][Service-Busicon]<br/>**Bus di servizio**][Service-Busdoc] | È il connettore più diffuso per le app per la logica e include trigger e azioni per la messaggistica asincrona e per la pubblicazione/sottoscrizione con code, sottoscrizioni e argomenti. |  [![Icona API][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Se si lavora con SharePoint e l'automazione potrebbe risultare utile, è consigliabile prendere in considerazione questo connettore. Può essere usato con SharePoint locale e SharePoint Online. |
| [![Icona API][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Uno dei connettori più usati, consente di connettersi a un'istanza locale di SQL Server e a un database SQL di Azure. | [![Icona API][Twittericon]<br/>**Twitter**][Twitterdoc] | Accedere con facilità con un account Twitter e quindi avviare un flusso di lavoro quando viene inserito un nuovo tweet. Salvare quindi i tweet in un database SQL o in un elenco di SharePoint. | 

### <a name="on-premises-connectors"></a>Connettori locali 

I connettori locali consentono di accedere ai dati nei server locali.  Per creare una connessione con un server locale è necessario un [gateway dati locale][gatewaydoc] che offra un canale di comunicazione sicuro senza che sia necessario configurare l'infrastruttura di rete.  Di seguito sono riportati alcuni connettori.

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icona API][db2icon]<br/>**DB2**][db2doc] | [![Icona API][oracle-DB-icon]<br/>**Oracle DB**][oracle-db-doc] | [![Icona API][sharepointicon]<br/>**SharePoint</br> Server**][sharepointserver] | [![Icona API][filesystem-icon]<br/>**File</br> system**][filesystemdoc] |
[![Icona API][sql-servericon]<br/>**SQL</br> Server**][sql-serverdoc] | ![Icona API][Biztalk-Servericon]<br/>**BizTalk</br> Server**| |

### <a name="integration-account-connectors"></a>Connettori dell'account di integrazione 

Enterprise Integration Pack (EIP) include connettori noti alla community di BizTalk Server. Quando si acquista un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), si ottengono anche i connettori seguenti: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icona API][as2icon]<br/>**Decodifica</br> AS2**][as2decode] | [![Icona API][as2icon]<br/>**Codifica</br> AS2**][as2encode] | [![Icona API][x12icon]<br/>**Decodifica</br> EDIFACT**][EDIFACTdecode] | [![Icona API][x12icon]<br/>**Codifica</br> EDIFACT**][EDIFACTencode] |
[![Icona API][flatfileicon]<br/>**Codifica</br> file flat**][flatfiledoc] | [![Icona API][flatfiledecodeicon]<br/>**Decodifica</br> file flat**][flatfiledecodedoc] | [![Icona API][integrationaccounticon]<br/>**Account<br/> di integrazione**][integrationaccountdoc] | [![Icona API][xmltransformicon]<br/>**Trasformazione<br/>XML**][xmltransformdoc] |
| [![Icona API][x12icon]<br/>**Decodifica</br> X12**][x12decode] | [![Icona API][x12icon]<br/>**Codifica</br> X12**][x12encode] | [![Icona API][xmlvalidateicon]<br/>**Convalida <br/>XML**][xmlvalidatedoc] | [![Icona API][liquidicon]<br/>**Trasformazioni <br/>JSON**][JSONliquidtransformdoc] |

### <a name="enterprise-connectors"></a>Connettori aziendali

È possibile connettersi alle applicazioni aziendali all'interno delle app per la logica.

|  |  |
| --- | --- |
|[![Icona API][MQicon]<br/>**MQ**][mqdoc]|[![Icona API][SAPicon]<br/>**SAP**][sapconnector]|

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
| **Select** | Selezionare alcune proprietà di una matrice per un'elaborazione aggiuntiva. Se si elencano record da SQL e vengono restituite 15 colonne, selezionare solo alcune colonne per un'elaborazione aggiuntiva. L'output è una matrice che contiene solo le proprietà selezionate. |

## <a name="custom-connectors-and-azure-certification"></a>Connettori personalizzati e certificazione per Azure 

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API basate su REST](../logic-apps/logic-apps-create-api-app.md). È anche possibile creare [connettori personalizzati](../logic-apps/custom-connector-overview.md) e renderli disponibili per qualsiasi app per la logica nella sottoscrizione.

Se si vuole rendere pubbliche le app per le API personalizzate in modo che siano disponibili per l'uso in Azure, è quindi possibile [inviare i connettori per la certificazione di Microsoft](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Ottenere aiuto

Per porre domande, fornire risposte e ottenere informazioni sulle attività degli altri utenti delle app per la logica di Azure, vedere il [forum sulle app per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Per contribuire al miglioramento di App per la logica di Azure e dei connettori, votare o inviare idee al [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

Se manca un argomento sui connettori o qualche dettaglio che si ritiene sia importante, aggiungerlo agli argomenti esistenti o scrivere un argomento personalizzato. La documentazione è open source e ospitata su GitHub. Introduzione al [repository GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Passaggi successivi
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)
* [Monitorare le app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"
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
[mqdoc]: ./connectors-create-api-mq.md "Connettersi a MQ in locale o ad Azure e inviare e ricevere messaggi"
[recurrencedoc]:  ./connectors-native-recurrence.md "Attivare azioni ricorrenti per le app per la logica"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Connettersi all'account Salesforce. Gestire account, lead, opportunità e altro ancora"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[service-busdoc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Connettersi a SharePoint Online. Gestire documenti, elementi elenco e altro ancora"
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Connettersi al server locale di SharePoint. Gestire documenti, elementi elenco e altro ancora"
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
[JSONliquidtransformdoc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Informazioni sulle trasformazioni JSON con Liquid"


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
[AppServices-icon]: ./media/apis-list/AppServices.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
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
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
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
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
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
[liquidicon]: ./media/apis-list/liquidtransform.png
