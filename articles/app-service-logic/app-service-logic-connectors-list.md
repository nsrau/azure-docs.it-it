<properties
	pageTitle="Elenco di connettori e applicazioni API | Microsoft Azure"
	description="Informazioni sui connettori e le app per le API nel servizio app di Azure; architettura dei microservizi"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/09/2015"
	ms.author="mandia"/>


# Elenco di connettori e app per le API nel servizio app di Microsoft Azure
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft.

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi del servizio app di Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Connettori Standard
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft, disponibili in Connettori Standard:

Nome | Descrizione
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Usare questo connettore per creare un cluster Hadoop in Azure e inviare diversi processi Hadoop e molto altro.
[Bus di servizio di Azure](app-service-logic-connector-azureservicebus.md) | È possibile inviare messaggi da code e messaggi del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio.
[BLOB di Archiviazione di Azure](app-service-logic-connector-azurestorageblob.md) | Si connette all'archivio BLOB e può ottenere, eliminare, elencare file e molto altro. 
[Box](app-service-logic-connector-box.md) | Si connette a Box e può caricare, ottenere, eliminare ed elencare file ed eseguire altre attività sui file.
[Chatter](app-service-logic-connector-chatter.md) | Si connette a Chatter e può inviare e cercare messaggi e anche recuperare nuovi messaggi.
[Dropbox](app-service-logic-connector-dropbox.md) | Si connette a Dropbox e può ottenere, eliminare, elencare file ed eseguire altre attività sui file.
[Facebook](app-service-logic-connector-facebook.md) | Si connette a Facebook e può pubblicare messaggi, foto e molto altro. È anche possibile ottenere messaggi e commenti, ottenere informazioni sulle preferenze dell'utente, inclusi libri e film.
[File](app-service-logic-connector-file.md) | Usando questo connettore, è possibile connettersi al file system o rete locale e completare varie attività sui file, come ad esempio, caricare, eliminare, elencare file e molto altro.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Si connette a un server FTP/FTPS ed esegue diverse attività FTP, incluso caricare, ottenere, eliminare file e molto altro.
[HTTP](app-service-logic-connector-http.md) | Il listener HTTP apre un endpoint che funge da server HTTP e resta in ascolto delle richieste HTTP o HTTPS in ingresso. L'azione HTTP non richiede un'app per le API ed è supportata in modo nativo all'interno delle app per la logica.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Il connettore Office 365 può inviare e ricevere messaggi di posta elettronica, gestire il calendario e gestire i contatti usando l'account Office 365.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Si connette al Microsoft OneDrive personale e può caricare, eliminare, elencare file e molto altro.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Si connette a Microsoft SharePoint Server locale o SharePoint Online per gestire documenti ed elementi elenco. Sono supportati diversi metodi di autenticazione, ad esempio le credenziali predefinite, OAuth 2.0, l'autenticazione di Windows e autenticazione basata su moduli.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Si connette a un database di SQL Server locale o a un database SQL di Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Si connette a Yammer per pubblicare messaggi e ottenerne nuovi.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Si connette a un server POP3 per recuperare i messaggi di posta elettronica con allegati.
[QuickBooks](app-service-logic-connector-quickbooks.md) | È possibile completare attività diverse, come ad esempio creare, aggiornare ed eseguire query su diverse entità di Intuit QuickBooks, ad esempio clienti, articoli, fatture e così via.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Si connette a SFTP e può caricare, ottenere, eliminare file e molto altro.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Si connette a un server SMTP e può inviare messaggi di posta elettronica con allegati.
[Slack](app-service-logic-connector-slack.md) | Si connette a Slack e invia messaggi ai canali Slack.
[Salesforce](app-service-logic-connector-salesforce.md) | Si connette all'account Salesforce personale e gestisce gli account, lead, opportunità e molto altro.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Si connette a SugarCRM Online e può creare, aggiornare, leggere e molto altro con diversi tipi di moduli come account, contatti, e così via.
[Twilio](app-service-logic-connector-twilio.md) | Si connette a Twilio e può inviare e ottenere messaggi, ottenere numeri disponibili, gestendo i numeri di telefono in ingresso e molto altro.
[Twitter](app-service-logic-connector-twitter.md) | Si connette a Twitter e può ottenere le sequenze temporali, pubblicare tweet e molto altro.
[Wait](app-service-logic-connector-wait.md) | Usare questo connettore per ritardare l'esecuzione dell'app. È possibile ritardare l'applicazione per una durata specifica o fino a una determinata occorrenza in un momento specifico.


## Connettori Premium
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft disponibili nei connettori Premium:

Nome | Descrizione
------------- | -------------
[Connettore AS2](app-service-logic-connector-as2.md) | Può ricevere e inviare messaggi usando il protocollo di trasporto AS2. I dati vengono trasportati in modo sicuro e affidabile usando la crittografia e i certificati digitali.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Riceve e invia messaggi usando il protocollo EDIFACT in comunicazioni business-to-business.
[BizTalk X12](app-service-logic-connector-x12.md) | Riceve e invia messaggi usando il protocollo X12 in comunicazioni business-to-business.
[BizTalk Trading Partner Management](app-service-logic-connector-tpm.md) | Definisce e mantiene le relazioni business-to-business usando partner, contratti nonché schemi e certificati usati in contratti. Queste relazioni vengono applicate usando app per le API EDIFACT, AS2 e X12.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Un codificatore e decodificatore che consente l'interoperabilità di app tra i dati JSON e XML. Può convertire una determinata istanza JSON in XML e viceversa.
[BizTalk Rules](app-service-logic-use-biztalk-rules.md) | Usa le regole BizTalk per definire e controllare la logica di business all'interno di un'organizzazione. I criteri di business possono essere aggiornati senza ricompilare e ridistribuire le applicazioni associate.
DB2 Connector | Si connette a un database IBM DB2, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language. <br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate<br/><br/>Questo connettore include anche il client Microsoft per DRDA per la connessione a un server Informix con una rete TCP/IP.
Informix | Si connette a un database IBM Informix, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language.<br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate.<br/><br/>Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Questo connettore include anche un client Microsoft per DRDA per la connessione a un server Informix mediante una rete TCP/IP.
MQ | Si connette a un database IBM WebSphere MQ Server versione 8, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Il connettore include anche il client Microsoft per MQ.<br/><br/>Nessun trigger. Nessuna azione.<br/><br/>**Nota** Non può essere usato attualmente con app per la logica.
[Oracle Database](app-service-logic-connector-oracle.md) | Si connette al database Oracle locale e può creare, aggiornare, ottenere ed eliminare voci in una tabella di database.
[SAP](app-service-logic-connector-sap.md) | Si connette a un server SAP locale e chiama RFC, BAPI e tRFC oltre a inviare IDOC.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Assicura l'interoperabilità tra i dati di file flat (come Excel e csv) e i dati XML. Questa app per le API può convertire un'istanza di file flat in XML e viceversa.
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | Converte i dati da un formato a un altro. È anche possibile caricare una mappa esistente (file con estensione trfm), visualizzare i collegamenti tra gli schemi di origine e di destinazione e usare la funzionalità 'Test' con un contenuto XML di input di esempio. Sono anche disponibili diverse funzioni incorporate, tra cui manipolazioni di stringhe, assegnazioni condizionali e molto altro.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | Convalida i dati XML rispetto a schemi XML predefiniti. È possibile usare gli schemi esistenti o generare schemi basati su un'istanza di file flat, su un'istanza JSON o su connettori esistenti.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Cerca ed estrae dati dal contenuto XML in base a un'espressione XPath specificata.

## Connettori come trigger
Diversi connettori forniscono i trigger per le app per la logica. Tali trigger sono di due tipi:

1. Trigger di polling: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app per la logica con i dati come input. Per evitare che gli stessi dati vengano usati più volte, il trigger può eseguire pulire i dati letti e passati all'app per la logica. Esempi di tali connettori sono File, SQL e Archiviazione di Azure.
2. Trigger di push: tali trigger restano in ascolto di dati su un endpoint oppure di un evento, quindi attivano una nuova istanza dell'app per la logica. Esempi di tali connettori sono il listener HTTP e Twitter.

## Connettori come azioni
È anche possibile usare i connettori come azioni nell'ambito dell'app per la logica. Le azioni sono utili per cercare nell'app per la logica i dati da usare nell'esecuzione. Può essere necessario ad esempio cercare in un database SQL altre informazioni su un cliente quando si elabora un ordine. In alternativa, potrebbe essere necessario scrivere, aggiornare o eliminare dati in una destinazione. A tale scopo, usare le azioni fornite dai connettori. Le azioni vengono mappate a operazioni nelle app per le API (come definito dai relativi metadati Swagger).

## Creare connettori e app per le API personalizzati
[Informazioni di riferimento su connettori e app per le API](http://aka.ms/appservicesconnectorreference) [Trigger delle app per le API del servizio app di Azure ](../app-service-api/app-service-api-dotnet-triggers.md) [ Informazioni di riferimento sulle app per la logica](https://msdn.microsoft.com/library/azure/dn948510.aspx)


## Altre informazioni su connettori e app per le API

[Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md) [Uso di gestione connessione ibrida nel servizio app di Azure](app-service-logic-hybrid-connection-manager.md) [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md)

<!---HONumber=August15_HO7-->