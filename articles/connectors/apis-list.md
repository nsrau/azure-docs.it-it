<properties
	pageTitle="Elenco di API gestite da Microsoft | Servizio app di Microsoft Azure"
	description="Ottenere un elenco completo delle API gestite da Microsoft che è possibile usare per creare app per la logica in Servizio app di Azure"
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
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="deonhe"/>

# Elenco di API gestite

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione schema 2014-12-01-preview, fare clic sull'[elenco dei connettori](../app-service-logic/app-service-logic-connectors-list.md).

Selezionare un'icona per informazioni su come usare rapidamente le API per la creazione di app in grado di chiamare i servizi corrispondenti. Queste API possono essere usate per creare app per la logica o un'app di PowerApps o entrambi i tipi.

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi di Azure App Service](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Per iniziare a usare le app per la logica prima di creare un account di Azure, passare alla pagina [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic). È possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

|API||||
|-----------|-----------|-----------|-----------|
|[![Icona API][blobicon]<br/>**BLOB di Azure**][azureblobdoc]|[![Icona API][bingsearchicon]<br/>**Ricerca di Bing**][bingsearchdoc]|[![Icona API][boxicon]<br/>**Box**][boxDoc]|[![Icona API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|
|[![Icona API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Icona API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icona API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icona API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Icona API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Icona API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Icona API][office365icon]<br/>**Office 365**<br/>**Utenti**][office365usersdoc]|[![Icona API][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|
|[![Icona API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icona API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Icona API][servicebusicon]<br/>**Bus di servizio**][servicebusdoc]|[![Icona API][sftpicon]<br/>**SFTP**][sftpdoc]|
|[![Icona API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Icona API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Icona API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icona API][sqlicon]<br/>**SQL Azure**][sqldoc]|
|[![Icona API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Icona API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icona API][yammericon]<br/>**Yammer**][yammerdoc] | |


### Trigger di API
Varie API forniscono i trigger che inviano notifiche all'app quando si verificano eventi specifici. Ad esempio, l'API di FTP ha il trigger OnUpdatedFile. È possibile creare un'app per la logica o un'app di PowerApps che resta in ascolto di tale trigger e intraprende un'azione ogni volta che il trigger viene attivato.

Sono disponibili due tipi di trigger:

* Trigger di polling: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app con i dati come input. Per evitare che gli stessi dati vengano usati più volte, il trigger può eseguire la pulizia dei dati letti e passati all'app.
* Trigger di push: tali trigger restano in ascolto di dati su un endpoint oppure di un evento, quindi attivano una nuova istanza dell'app. L'API di Twitter ne è un esempio.


### Azioni di API
Le API possono essere usate anche come azioni nell'ambito delle app. Le azioni sono utili per cercare i dati da usare nell'esecuzione dell'app. Può essere necessario ad esempio cercare in un database SQL dati su un cliente quando si elabora un ordine. Oppure può essere necessario scrivere, aggiornare o eliminare dati in una tabella di destinazione. A tale scopo, usare le azioni fornite dalle API. Le azioni vengono mappate alle operazioni definite nei metadati Swagger.


[Novità](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) [Introduzione alle app di PowerApps](../power-apps/powerapps-get-started-azure-portal.md)

<!--API Documentation-->
[azureblobdoc]: ./create-api-azureblobstorage.md "Connettersi al BLOB di Azure per gestire file nel contenitore BLOB."
[bingsearchDoc]: ./create-api-bingsearch.md "Ricerca Bing per web, immagini, notizie e video."
[boxDoc]: ./create-api-box.md "Si connette a Box e può caricare, ottenere, eliminare ed elencare file ed eseguire altre attività sui file."
[crmonlinedoc]: ./create-api-crmonline.md "Connettersi a Dynamics CRM Online ed eseguire operazioni con i dati di CRM Online."
[dropboxdoc]: ./create-api-dropbox.md "Si connette a Dropbox e può ottenere, eliminare, elencare file ed eseguire altre attività sui file."
[exceldoc]: ./create-api-excel.md "Connettersi a Excel."
[facebookdoc]: ./create-api-facebook.md "Connettersi a Facebook per pubblicare un post in una sequenza temporale, recuperare il feed di una pagina e così via."
[ftpdoc]: ./create-api-ftp.md "Si connette a un server FTP/FTPS ed esegue diverse attività FTP, incluso caricare, ottenere, eliminare file e molto altro."
[googledrivedoc]: ./create-api-googledrive.md "Connettersi a GoogleDrive e interagire con i dati."
[microsofttranslatordoc]: ./create-api-microsofttranslator.md
[office365outlookdoc]: ./create-api-office365-outlook.md "Il connettore Office 365 può inviare e ricevere messaggi di posta elettronica, gestire il calendario e gestire i contatti usando l'account Office 365."
[officeunifieddoc]: ./create-api-bingsearch.md
[office365usersdoc]: ./create-api-office365-users.md
[office365videodoc]: ./create-api-office365-video.md
[onedrivedoc]: ./create-api-onedrive.md "Si connette al Microsoft OneDrive personale e può caricare, eliminare, elencare file e molto altro."
[salesforcedoc]: ./create-api-salesforce.md "Si connette all'account Salesforce personale e gestisce account, clienti potenziali, opportunità e molto altro."
[servicebusdoc]: ./create-api-servicebus.md "È possibile inviare messaggi da code e messaggi del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio."
[sharepointdoc]: ./create-api-sharepointonline.md "Si connette a SharePoint Online per gestire documenti ed elementi elenco."
[slackdoc]: ./create-api-slack.md "Si connette a Slack e invia messaggi ai canali Slack."
[sftpdoc]: ./create-api-sftp.md "Si connette a SFTP e può caricare, ottenere, eliminare file e molto altro."
[smtpdoc]: ./create-api-smtp.md "Si connette a un server SMTP e può inviare messaggi di posta elettronica con allegati."
[sqldoc]: ./create-api-sqlazure.md "Connettersi al database di SQL Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL."
[twiliodoc]: ./create-api-twilio.md "Si connette a Twilio e può inviare e ottenere messaggi, ottenere numeri disponibili, gestendo i numeri di telefono in ingresso e molto altro."
[twitterdoc]: ./create-api-twitter.md "Si connette a Twitter e può ottenere le sequenze temporali, pubblicare tweet e molto altro."
[yammerdoc]: ./create-api-yammer.md "Si connette a Yammer per pubblicare messaggi e ottenerne nuovi."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0302_2016-->