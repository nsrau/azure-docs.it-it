<properties 
   pageTitle="Utilizzo dei connettori" 
   description="Utilizzo dei connettori" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#Connettori#
I connettori sono app per le API che consentono di connettersi ai dati e ai servizi in esecuzione nel cloud oppure in locale. I connettori consentono di accedere ai dati con un'ampia gamma di trigger predefiniti e azioni cui è possibile accedere facilmente nelle app per la logica e altro ancora.

Azure App Service fornisce una serie di connettori predefiniti:

##Connettori Standard##
* [Connettore del Bus di servizio di Azure]
* [Connettore BLOB di archiviazione di Azure]
* Connettore WebJobs di Azure
* [Connettore Box]
* [Connettore Chatter]
* [Connettore DropBox]
* [Connettore Facebook]
* [Connettore File]
* [Connettore FTP]
* [Connettore HDInsight]
* [Connettore HTTP]
* [Connettore Office 365]
* [Connettore OneDrive]
* [Connettore Oracle]
* [Connettore POP3]
* [Connettore QuickBooks]
* [Connettore Salesforce]
* [Connettore SFTP]
* [Connettore Sharepoint]
* [Connettore Slack]
* [Connettore SMTP]
* [Connettore SQL]
* [Connettore SugarCRM]
* [Connettore Twilio]
* [Connettore Twitter]
* Connettore Wait
* [Connettore Yammer]

##Connettori Premium e app per le API##
* AS2 Connector
* BizTalk EDIFACT
* BizTalk Flat File Encoder
* BizTalk Transform Service
* BizTalk Rules
* BizTalk X12
* BizTalk XPath Extractor
* BizTalk XML Validator
* DB2 Connector
* Informix Connector
* Oracle Database Connector
* MQ Connector
* [SAP connector]
 
Per altre informazioni, vedere i riferimenti ai connettori e alle API [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Connettori e app per la logica##
Le app per la logica sono costituite da trigger e da azioni. Alcuni connettori possono essere usati come trigger per creare un'istanza di un flusso di lavoro in base a un evento o alla disponibilità di alcuni dati. I connettori vengono anche usati come azioni per la lettura e scrittura dei dati o per eseguire altre azioni supportate dal connettore.

###Connettori come trigger###
Diversi connettori forniscono i trigger per le app per la logica. Tali trigger sono di due tipi:

1. Trigger di polling: questi trigger eseguiranno il polling del servizio che interessa a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, verrà eseguita una nuova istanza dell'app per la logica con i dati come input. Il trigger può eseguire altre attività, come la pulizia dei dati letti e passati all'app per la logica, per prevenirne l'utilizzo ripetuto. Esempi di tali connettori sono File, SQL e Archiviazione di Azure.
2. Trigger di push: tali trigger restano in attesa di dati su un endpoint oppure che si verifichi un evento che attivi una nuova istanza dell'app per la logica. Esempi di tali connettori sono i listener HTTP e Twitter.

###Connettori come azioni###
È anche possibile usare i connettori come azioni come parte dell'app per la logica. Le azioni sono utili per cercare nell'app per la logica i dati da usare nell'esecuzione; ad esempio, può essere necessario cercare in un database SQL altre informazioni riguardo un cliente quando si elabora un ordine. In alternativa, può essere necessario scrivere, aggiornare o eliminare i dati in una destinazione, per cui è possibile usare le azioni fornite dai connettori. Le azioni eseguono il mapping a operazioni nelle app per le API (come definito dai relativi metadati Swagger).


<!-- Links -->

[Connettore Box]: app-service-logic-connector-box.md
[Connettore Facebook]: app-service-logic-connector-facebook.md
[Connettore Salesforce]: app-service-logic-connector-salesforce.md
[Connettore Twitter]: app-service-logic-connector-twitter.md
[SAP connector]: app-service-logic-connector-sap.md
[Connettore FTP]: app-service-logic-connector-ftp.md
[Connettore HTTP]: app-service-logic-connector-http.md
[Connettore BLOB di archiviazione di Azure]: app-service-logic-connector-azurestorageblob.md
[Connettore Office 365]: app-service-logic-connector-office365.md
[Connettore Sharepoint]: app-service-logic-connector-sharepoint.md
[Connettore SugarCRM]: app-service-logic-connector-sugarcrm.md
[Connettore QuickBooks]: app-service-logic-connector-quickbooks.md
[Connettore Yammer]: app-service-logic-connector-yammer.md
[Connettore Twilio]: app-service-logic-connector-twilio.md
[Connettore SMTP]: app-service-logic-connector-smtp.md
[Connettore SFTP]: app-service-logic-connector-sftp.md
[Connettore POP3]: app-service-logic-connector-pop3.md
[Connettore DropBox]: app-service-logic-connector-dropbox.md
[Connettore Chatter]: app-service-logic-connector-chatter.md
[Connettore HDInsight]: app-service-logic-connector-hdinsight.md
[Connettore del Bus di servizio di Azure]: app-service-logic-connector-azureservicebus.md
[Connettore Oracle]: app-service-logic-connector-oracle.md
[Connettore SQL]: app-service-logic-connector-sql.md
[Connettore OneDrive]: app-service-logic-connector-onedrive.md
[Connettore File]: app-service-logic-connector-file.md
[Connettore Slack]: app-service-logic-connector-slack.md


<!--HONumber=54-->