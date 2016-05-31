<properties
	pageTitle="Elenco di connettori gestiti da Microsoft | Servizio app di Microsoft Azure"
	description="Ottenere un elenco completo dei connettori gestiti da Microsoft che è possibile usare per creare app per la logica nel servizio app di Azure"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/19/2016"
	ms.author="deonhe"/>

# Elenco di connettori gestiti

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione schema 2014-12-01-preview, fare clic sull'[elenco dei connettori](../app-service-logic/app-service-logic-connectors-list.md).

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi di Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Per iniziare a usare le app per la logica prima di creare un account di Azure, passare alla pagina [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Selezionare un'icona per informazioni su come usare rapidamente i connettori per la creazione di app in grado di chiamare i servizi corrispondenti. Questi connettori possono essere usati per creare app per la logica, PowerApp e flussi.

|Connettori||||
|-----------|-----------|-----------|-----------|
|[![Icona API][blobicon]<br/>**BLOB di Azure**][azureblobdoc]|[![Icona API][bingsearchicon]<br/>**Ricerca di Bing**][bingsearchdoc]|[![Icona API][boxicon]<br/>**Box**][boxDoc]|[![Icona API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![Icona API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Icona API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icona API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icona API][githubicon]<br/>**GitHub**][githubdoc]|
[![Icona API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|[![Icona API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Icona API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Icona API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|
|[![Icona API][office365icon]<br/>**Office 365**<br/>**Utenti**][office365usersdoc]|[![Icona API][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|[![Icona API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icona API][onedriveicon]<br/>**OneDrive<br/>for Business**][onedriveforbusinessdoc]|
|[![Icona API][outlookicon]<br/>**Outlook**][outlookdoc]|[![Icona API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Icona API][rssicon]<br/>**RSS**][rssdoc]|[![Icona API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|
|[![Icona API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|[![Icona API][servicebusicon]<br/>**Bus di servizio**][servicebusdoc]|[![Icona API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Icona API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|
|[![Icona API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Icona API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icona API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Icona API][trelloicon]<br/>**Trello**][trellodoc]|
[![Icona API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Icona API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icona API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![Icona API][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Se sono state create app per la logica con lo schema 2014-12-01-preview, si noterà che i connettori di integrazione aziendale, ad esempio quelli per BizTalk, non sono elencati sopra. Siamo consapevoli dell'importanza di queste API e ci stiamo impegnando per renderle disponibili quanto prima. Anche se non sono ancora state specificate date precise per la disponibilità, si tratta di una delle nostre priorità. Nel frattempo è possibile accedere ad [API v1 e API BizTalk dalle app per la logica](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Grazie per la comprensione. Non rimane che attendere.


### Connettori come trigger
Vari connettori forniscono trigger che possono inviare notifiche all'app quando si verifica un evento specifico. Ad esempio, il connettore FTP include il trigger OnUpdatedFile. È possibile creare un'app per la logica, una PowerApp o un flusso che resta in ascolto del trigger ed esegue un'azione ogni volta che il trigger viene attivato.

Sono disponibili due tipi di trigger:

* Trigger di polling: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app con i dati come input. Per evitare che gli stessi dati vengano usati più volte, il trigger può eseguire la pulizia dei dati letti e passati all'app.
* Trigger di push: tali trigger restano in ascolto di dati su un endpoint oppure di un evento, quindi attivano una nuova istanza dell'app. Il connettore Twitter ne è un esempio.


### Connettori come azioni
I connettori possono anche essere usati come azioni all'interno delle app. Le azioni sono utili per cercare i dati da usare nell'esecuzione dell'app. Può essere necessario ad esempio cercare in un database SQL dati su un cliente quando si elabora un ordine. Oppure può essere necessario scrivere, aggiornare o eliminare dati in una tabella di destinazione. A tale scopo, usare le azioni fornite dai connettori. Le azioni vengono mappate alle operazioni definite nei metadati Swagger.


[Novità](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) [Introduzione a PowerApps](../power-apps/powerapps-get-started-azure-portal.md) [Eseguire la migrazione delle app per la logica esistenti alla versione dello schema più recente](connectors-schema-migration.md)

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Connettersi al BLOB di Azure per gestire file nel contenitore BLOB."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Ricerca Bing per web, immagini, notizie e video."
[boxDoc]: ./connectors-create-api-box.md "Si connette a Box e può caricare, ottenere, eliminare ed elencare file ed eseguire altre attività sui file."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Connettersi a Dynamics CRM Online ed eseguire operazioni con i dati di CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Si connette a Dropbox e può ottenere, eliminare, elencare file ed eseguire altre attività sui file."
[exceldoc]: ./connectors-create-api-excel.md "Connettersi a Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Connettersi a Facebook per pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via."
[ftpdoc]: ./connectors-create-api-ftp.md "Si connette a un server FTP/FTPS ed esegue diverse attività FTP, incluso caricare, ottenere, eliminare file e molto altro."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Connettersi a GoogleDrive e interagire con i dati."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Il connettore Office 365 può inviare e ricevere messaggi di posta elettronica, gestire il calendario e gestire i contatti usando l'account Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Si connette al Microsoft OneDrive personale e può caricare, eliminare, elencare file e molto altro."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Si connette alla versione di Microsoft OneDrive per le aziende e carica, elimina, elenca i file e altro ancora."
[outlookdoc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook e accedere alla posta elettronica e altro ancora."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Si connette a Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "Il connettore RSS consente agli utenti di pubblicare e recuperare elementi di feed. Consente anche agli utenti di attivare operazioni quando viene pubblicato un nuovo elemento nel feed."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Si connette all'account Salesforce personale e gestisce account, clienti potenziali, opportunità e molto altro."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Si connette a Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "È possibile inviare messaggi da code e messaggi del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Si connette a SharePoint Online per gestire documenti ed elementi elenco."
[slackdoc]: ./connectors-create-api-slack.md "Si connette a Slack e invia messaggi ai canali Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Si connette a SFTP e può caricare, ottenere, eliminare file e molto altro."
[githubdoc]: ./connectors-create-api-github.md "Si connette a GitHub e può tenere traccia dei problemi."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Inviare messaggi di posta elettronica migliori."
[smtpdoc]: ./connectors-create-api-smtp.md "Si connette a un server SMTP e può inviare messaggi di posta elettronica con allegati."
[sqldoc]: ./connectors-create-api-sqlazure.md "Connettersi al database di SQL Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL."
[trellodoc]: ./connectors-create-api-trello.md "Trello consente di organizzare visivamente qualsiasi cosa con chiunque in modo flessibile e gratuito."
[twiliodoc]: ./connectors-create-api-twilio.md "Si connette a Twilio e può inviare e ottenere messaggi, ottenere numeri disponibili, gestendo i numeri di telefono in ingresso e molto altro."
[twitterdoc]: ./connectors-create-api-twitter.md "Si connette a Twitter e può ottenere le sequenze temporali, pubblicare tweet e molto altro."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Consente di sincronizza la propria vita."
[yammerdoc]: ./connectors-create-api-yammer.md "Si connette a Yammer per pubblicare messaggi e ottenerne nuovi."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0525_2016-->