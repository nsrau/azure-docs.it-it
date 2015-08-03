<properties 
	pageTitle="Elenco di connettori e app per le API | Azure" 
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
	ms.date="07/12/2015" 
	ms.author="mandia"/>


# Elenco di connettori e app per le API nel servizio app di Microsoft Azure
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft.

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi del servizio app di Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Connettori Standard
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft, disponibili in Connettori Standard:

Nome | Descrizione
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Usare questo connettore per creare un cluster Hadoop in Azure e inviare diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Con questo connettore, è anche possibile avviare un cluster, inviare un processo e attendere il completamento del processo.
[Bus di servizio di Azure](app-service-logic-connector-azureservicebus.md) | Usando questo connettore è possibile inviare messaggi da entità del bus di servizio, come code e argomenti, e ricevere messaggi da entità del bus di servizio, come code e sottoscrizioni.
[BLOB di Archiviazione di Azure](app-service-logic-connector-azurestorageblob.md) | Si connette all'archivio BLOB e può caricare, ottenere ed eliminare BLOB, elencare BLOB nel contenitore, eseguire snapshot di BLOB, copiare BLOB e usare un trigger per recuperare BLOB.
Processi Web di Azure | Si connette a Processi Web.
[Box](app-service-logic-connector-box.md) | Si connette a Box e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.
[Chatter](app-service-logic-connector-chatter.md) | Si connette a Chatter e può inviare e cercare messaggi, nonché usare un trigger per recuperare nuovi messaggi.
[Dropbox](app-service-logic-connector-dropbox.md) | Si connette a Dropbox e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare i file.
[Facebook](app-service-logic-connector-facebook.md) | Si connette a Facebook e può pubblicare messaggi, video, foto, offerte e così via. È inoltre possibile recuperare messaggi, commenti, eventi, feed dell'utente, informazioni sulle preferenze dell'utente relative, ad esempio, a libri, giochi, film e così via. Questo connettore include anche un trigger per recuperare nuovi messaggi pubblicati in una pagina.
[File](app-service-logic-connector-file.md) | Consente di inviare o caricare file in un file system o in rete e di ricevere o scaricare file da un file system o da una rete. Usando questo connettore, è possibile connettersi al file server locale e caricare, scaricare, eliminare, elencare file, nonché usare un trigger per recuperare file.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Si connette a un server FTP/FTPS e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.
[HTTP](app-service-logic-connector-http.md) | Il listener HTTP apre un endpoint che funge da server HTTP e resta in ascolto delle richieste HTTP in ingresso. È possibile eseguire i metodi POST, GET, DELETE e PUT. L'azione HTTP non richiede un'app per le API ed è supportata in modo nativo all'interno delle app per la logica. Può usare il protocollo HTTP o HTTPS.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Il connettore Office 365 può inviare e ricevere messaggi di posta elettronica, gestire il calendario e gestire i contatti usando l'account Office 365. Consente anche di inviare, ricevere e ottenere messaggi di posta elettronica, creare ed eliminare eventi del calendario e creare, aggiornare, ottenere ed eliminare contatti.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive è un servizio di hosting di file. Il connettore OneDrive si connette all'account Microsoft OneDrive personale e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Si connette a Microsoft SharePoint Server locale o SharePoint Online per gestire documenti ed elementi elenco. È inoltre possibile creare, aggiornare, ottenere ed eliminare documenti ed elementi elenco. Sono supportati diversi metodi di autenticazione, ad esempio le credenziali predefinite, OAuth 2.0, l'autenticazione di Windows e autenticazione basata su moduli.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Si connette a un database di SQL Server locale o a un database SQL di Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Si connette a Yammer. Include un'azione di pubblicazione dei messaggi e un trigger per recuperare nuovi messaggi.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Si connette a un server POP3 e include un trigger per recuperare i messaggi di posta elettronica con allegati.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Usando questo connettore è possibile creare, aggiornare, leggere, eliminare ed eseguire query su diverse entità di Intuit QuickBooks, ad esempio clienti, articoli, fatture e così via.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Si connette a SFTP e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare i file.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Si connette a un server SMTP e può inviare messaggi di posta elettronica con allegati.
[Slack](app-service-logic-connector-slack.md) | Si connette a Slack e invia messaggi ai canali Slack.
[Salesforce](app-service-logic-connector-salesforce.md) | Il connettore Salesforce gestisce diverse entità, ad esempio account, lead, opportunità, casi e così via nell'account Salesforce. È anche possibile creare, aggiornare, inserire ed eliminare varie entità, nonché eseguire query su di esse.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Si connette a SugarCRM Online e può creare, aggiornare, leggere ed eliminare diversi tipi di moduli come account, contatti, prodotti e così via.
[Twilio](app-service-logic-connector-twilio.md) | Si connette a Twilio e può inviare, ottenere ed elencare messaggi, elencare utilizzi, ottenere numeri verdi disponibili, ottenere numeri di telefoni cellulari disponibili, ottenere numeri locali disponibili, nonché elencare, ottenere e aggiungere numeri di telefono in ingresso.
[Twitter](app-service-logic-connector-twitter.md) | Si connette a Twitter e può ottenere la sequenza temporale dell'utente, cercare tweet, ottenere follower, ottenere amici, cercare utenti, ottenere la sequenza temporale della home page, ottenere la tempistica delle citazioni, pubblicare tweet, pubblicare tweet per l'utente e inviare messaggi diretti. Il connettore Twitter usa anche trigger, ad esempio Get Tweets by Keyword, Get Tweets by User Handle e Get Tweets by Hashtag.
[Wait](app-service-logic-connector-wait.md) | Usare questo connettore per ritardare l'esecuzione dell'app. È possibile ritardare l'applicazione per una durata specifica o fino a una determinata occorrenza in un momento specifico.


## Connettori Premium
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft disponibili nei connettori Premium:

Nome | Descrizione
------------- | -------------
[Connettore AS2](app-service-logic-connector-as2.md) | AS2 Connector può ricevere e inviare messaggi usando il protocollo di trasporto AS2 nelle comunicazioni business-to-business. I dati vengono trasportati in modo sicuro e affidabile tramite Internet mediante la crittografia e certificati digitali.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | L'app per le API EDIFACT riceve e invia messaggi usando il protocollo EDIFACT nelle comunicazioni business-to-business.
[BizTalk X12](app-service-logic-connector-x12.md) | L'app per le API X12 riceve e invia messaggi usando il protocollo X12 nelle comunicazioni business-to-business.
[BizTalk Trading Partner Management](app-service-logic-connector-tpm.md) | L'app per le API di BizTalk Trading Partner Management definisce e mantiene le relazioni business-to-business tramite partner, contratti nonché schemi e certificati usati in contratti. Queste relazioni vengono applicate usando app per le API EDIFACT, AS2 e X12.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Un codificatore e decodificatore che consente l'interoperabilità di app tra i dati JSON e XML. Può convertire una determinata istanza JSON in XML e viceversa.
[BizTalk Rules](app-service-logic-use-biztalk-rules.md) | BizTalk Rules definisce e controlla la struttura, il funzionamento e la strategia di un'organizzazione. I criteri di business possono essere aggiornati senza ricompilare e ridistribuire le applicazioni associate.
DB2 Connector | Si connette a un database IBM DB2, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language. <br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate<br/><br/>Questo connettore include anche il client Microsoft per DRDA per la connessione a un server Informix con una rete TCP/IP.
Informix | Si connette a un database IBM Informix, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Può mappare le operazioni API Web e API OData ai comandi Informix Structured Query Language.<br/><br/>Nessun trigger. Le azioni includono la selezione, l'inserimento, l'aggiornamento e l'eliminazione di tabelle, nonché istruzioni personalizzate.<br/><br/>Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Questo connettore include anche un client Microsoft per DRDA per la connessione a un server Informix mediante una rete TCP/IP.
MQ | Si connette a un database IBM WebSphere MQ Server versione 8, in locale e in una macchina virtuale di Azure che esegue un sistema operativo Windows. Se l'uso è in locale, è possibile usare VPN o Azure ExpressRoute. Il connettore include anche il client Microsoft per MQ.<br/><br/>Nessun trigger. Nessuna azione.<br/><br/><strong>Nota</strong> Non può essere usato attualmente con app per la logica.
[Oracle Database](app-service-logic-connector-oracle.md) | Il connettore Oracle Database si connette al database Oracle locale e può creare, aggiornare, ottenere ed eliminare voci in una tabella di database.
[SAP](app-service-logic-connector-sap.md) | Il connettore SAP si connette a un server SAP locale e chiama RFC, BAPI e tRFC oltre a inviare IDOC.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Assicura l'interoperabilità tra i dati di file flat (come Excel e csv) e i dati XML. Questa app per le API può convertire un'istanza di file flat in XML e viceversa.
[BizTalk Transform Service](app-service-logic-transform-xml-documents.md) | L'app per le API BizTalk Transform converte i dati da un formato in un altro formato. È anche possibile caricare una mappa esistente (file .trfm), visualizzare la rappresentazione grafica della mappa che mostra i collegamenti tra gli schemi di origine e di destinazione e usare la funzionalità 'Test' con un contenuto XML di input di esempio. Sono anche disponibili diverse funzioni incorporate, tra cui manipolazioni di stringhe, assegnazioni condizionali, espressioni aritmetiche, formattatori di data e ora, cicli e così via.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | L'app per le API Validator convalida i dati XML rispetto a schemi XML predefiniti. È possibile usare gli schemi esistenti o generare schemi basati su un'istanza di file flat, su un'istanza JSON o su connettori esistenti.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | L'app per le API Extractor cerca ed estrae dati dal contenuto XML in base a un'espressione XPath specificata.

## Connettori come trigger
Diversi connettori forniscono i trigger per le app per la logica. Tali trigger sono di due tipi:

1. Trigger di polling: questi trigger eseguono il polling del servizio a una frequenza specificata per verificare la disponibilità di nuovi dati. Quando sono disponibili nuovi dati, viene eseguita una nuova istanza dell'app per la logica con i dati come input. Per evitare che gli stessi dati vengano usati più volte, il trigger può eseguire pulire i dati letti e passati all'app per la logica. Esempi di tali connettori sono File, SQL e Archiviazione di Azure.
2. Trigger di push: tali trigger restano in ascolto di dati su un endpoint oppure di un evento, quindi attivano una nuova istanza dell'app per la logica. Esempi di tali connettori sono il listener HTTP e Twitter.

## Connettori come azioni
È anche possibile usare i connettori come azioni nell'ambito dell'app per la logica. Le azioni sono utili per cercare nell'app per la logica i dati da usare nell'esecuzione. Può essere necessario ad esempio cercare in un database SQL altre informazioni su un cliente quando si elabora un ordine. In alternativa, potrebbe essere necessario scrivere, aggiornare o eliminare dati in una destinazione. A tale scopo, usare le azioni fornite dai connettori. Le azioni vengono mappate a operazioni nelle app per le API (come definito dai relativi metadati Swagger).

## Creare connettori e app per le API personalizzati
[Informazioni di riferimento su connettori e app per le API](http://aka.ms/appservicesconnectorreference)<br/> [Trigger delle app per le API del servizio app di Azure](../app-service-api/app-service-api-dotnet-triggers.md)<br/> [Informazioni di riferimento sulle app per la logica](https://msdn.microsoft.com/library/azure/dn948510.aspx)


## Altre informazioni su connettori e app per le API

[Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Uso di Gestione connessione ibrida nel servizio app di Azure](app-service-logic-hybrid-connection-manager.md)<br/> [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md)

<!---HONumber=July15_HO4-->