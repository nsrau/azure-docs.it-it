<properties 
	pageTitle="Elenco di connettori e app per API | Azure" 
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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Elenco di connettori e app per le API nel servizio app di Microsoft Azure
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft.

Per informazioni sui prezzi e un elenco di ciò che è incluso in ogni livello di servizio, vedere le [Informazioni sui prezzi di Azure App Service](http://azure.microsoft.com/pricing/details/app-service/).


## Connettori Standard
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft, disponibili in Connettori Standard:

<table border="1">
<tr bgcolor="FAF9F9">
        <th>Nome</th>
        <th>Descrizione</th>
</tr>

<tr>
<td colspan="2"><strong>Connettori per i servizi app e dati</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">Connettori per i servizi app e dati</a> fornisce altre informazioni dettagliate su questi connettori.</td>
</tr>

<tr>
<td>HDInsight di Azure</td>
<td>HDInsight di Azure distribuisce ed implementa il provisioning di cluster Apache Hadoop nel cloud e fornisce un framework software progettato per gestire, analizzare e creare report sui on Big Data. Questo connettore crea un cluster Hadoop in Azure e invia diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Con questo connettore, è possibile avviare un cluster, inviare un processo e attendere il completamento del processo.</td>
</tr>

<tr>
<td>Bus di servizio di Azure</td>
<td>Bus di servizio di Azure è un sistema di messaggistica generico, basato sul cloud per la connessione di qualsiasi dispositivo. Consente lo scambio di messaggi a regime di controllo libero per migliorare la scalabilità e l'affidabilità. Usando questo connettore è possibile inviare messaggi da entità del bus di servizio come code e argomenti e ricevere messaggi da entità del bus di servizio quali code e sottoscrizioni.</td>
</tr>

<tr>
<td>BLOB di Archiviazione di Azure</td>
<td>L'archiviazione BLOB di Azure archivia quantità elevate di dati non strutturati, come dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo. Usando questo connettore è possibile connettersi all'archiviazione BLOB e caricare, ottenere, eliminare BLOB, elencare BLOB nel contenitore, eseguire snapshot di BLOB, copiare BLOB e usare un trigger per recuperare BLOB.</td>
</tr>

<tr>
<td>Box</td>
<td>Box è un servizio di condivisione di file. Il connettore Box si connette a Box e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox è un servizio di hosting di file. Usando questo connettore è possibile connettersi a Dropbox e caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>Office 365 Connector può inviare e ricevere messaggi di posta elettronica, gestire il calendario e gestire i contatti usando l'account Office 365. Consente anche di inviare, ricevere e ottenere i messaggi di posta elettronica, creare ed eliminare gli eventi del calendario e creare, aggiornare, ottenere ed eliminare contatti.</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive è un servizio di hosting di file. Il connettore OneDrive si connette all'account Microsoft OneDrive personale e può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>Microsoft SharePoint Connector si connette a Microsoft SharePoint Server locale o SharePoint Online e gestisce documenti ed elementi elenco. È inoltre possibile creare, aggiornare, ottenere ed eliminare documenti ed elementi elenco. Sono supportati diversi metodi di autenticazione, ad esempio le credenziali predefinite, OAuth 2.0, l'autenticazione di Windows e autenticazione basata su moduli.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>Microsoft SQL Connector si connette a un database SQL Server locale o a un database SQL di Azure. È possibile creare, aggiornare, ottenere ed eliminare le voci in una tabella di database SQL.</td>
</tr>

<tr>
<td colspan="2"><strong>Connettori per social network</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">Connettori per social network</a> fornisce informazioni più dettagliate su questi connettori.</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter è un social network aziendale. Il connettore Chatter si connette a Chatter e può inviare e cercare messaggi, nonché usare un trigger per recuperare nuovi messaggi.</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook è un servizio di social networking. Il connettore Facebook si connette a Facebook e può pubblicare messaggi, video, foto, offerte e così via. È inoltre possibile recuperare messaggi, commenti, eventi, feed dell'utente, informazioni sulle preferenze dell'utente relative, ad esempio, a libri, giochi, film e così via. Questo connettore include anche un trigger per recuperare nuovi messaggi pubblicati in una pagina.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer è un social network aziendale. Il connettore Yammer si connette a Yammer, include un'azione di pubblicazione dei messaggi e un trigger per recuperare nuovi messaggi.</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio è un servizio SaaS orientato alle comunicazioni. Twilio Connector si connette a Twilio e può inviare, ottenere ed elencare messaggi, elencare utilizzi, ottenere numeri verdi disponibili, ottenere numeri di telefoni cellulari disponibili, ottenere numeri locali disponibili, elencare e ottenere numeri di telefono in ingresso e aggiungere numeri di telefono.</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter è un servizio di social networking online che consente agli utenti di inviare e leggere messaggi brevi di 140 caratteri. Twitter Connector si connette a Twitter e può ottenere la sequenza temporale dell’utente, cercare tweet, ottenere follower, ottenere amici, cercare utenti, ottenere la sequenza temporale della home page, ottenere la tempistica delle citazioni, pubblicare tweet, pubblicare tweet per l’utente e inviare messaggi diretti. Il connettore Twitter usa anche trigger, ad esempio Get Tweets by Keyword, Get Tweets by User Handle e Get Tweets by Hashtag.</td>
</tr>

<tr>
<td colspan="2"><strong>Connettori per protocolli</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">Connettori per protocolli</a> fornisce informazioni più dettagliate su questi connettori.</td>
</tr>

<tr>
<td>File</td>
<td>File Connector consente di inviare o caricare file in un file system o in rete e di ricezione o scaricare file da un file system o da una rete. Usando questo connettore, è possibile connettersi al server di file locale e caricare, scaricare, eliminare, elenca file, nonché usare un trigger per recuperare file.</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>Il protocollo FTP (File Transfer Protocol) è un diffuso protocollo di rete che consente di trasferire file da un host a altro. FTP Connector può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare file.</td>
</tr>

<tr>
<td>Listener HTTP</td>
<td>Il listener HTTP apre un endpoint che funge da server HTTP e resta in ascolto delle richieste HTTP in ingresso.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (Post Office Protocol) è il protocollo usato da un client di posta elettronica per recuperare la posta elettronica da un server di posta. Il POP3 Connector si connette a un server POP3 e include un trigger per recuperare i messaggi di posta elettronica con allegati.</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (SSH File Transfer Protocol) è un protocollo comune usato per trasferire i file in modo sicuro. Il SFTP Connector può caricare, ottenere, eliminare ed elencare file, nonché usare un trigger per recuperare i file.</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (Simple Mail Transfer Protocol) è il protocollo usato tra un client di posta elettronica e un server di posta elettronica per inviare messaggi di posta. SMTP Connector si connette a un server SMTP e invia messaggi di posta elettronica con allegati.</td>
</tr>

<tr>
<td colspan="2"><strong>Connettori aziendali</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">Connettori aziendali</a> fornisce informazioni più dettagliate su questi connettori.</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo sviluppa software per l'automazione marketing che fornisce servizi di inbound marketing, social marketing, CRM e altri servizi correlati. Marketo Connector si connette a Marketo e può creare e aggiornare clienti potenziali, ottenere modifiche dei clienti potenziali, pianificare una campagna, richiedere una campagna, ottenere clienti potenziali, ottenere informazioni su campagne/elenchi, aggiungere clienti potenziali a un elenco e rimuovere clienti potenziali da un elenco.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>Usando il connettore QuickBooks è possibile creare, aggiornare, leggere, eliminare ed effettuare query su diverse entità di Intuit QuickBooks, come clienti, articoli, fatture e così via.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>Salesforce Connector gestisce diverse entità come account, clienti potenziali, opportunità, casi e così via nell'account Salesforce. È anche possibile creare, aggiornare, inserire, effettuare query ed eliminare entità diverse.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>Il connettore SugarCRM si connette a SugarCRM Online e può creare, aggiornare, leggere ed eliminare diversi tipi di moduli come account, contatti, prodotti e così via.</td>
</tr>

</table>


## App per le API EAI BizTalk
Nella tabella seguente sono elencati tutte le app per le API disponibili create da Microsoft, disponibili in EAI Services per BizTalk:

	Name  | Description
------------- | -------------
BizTalk Flat File Encoder | Assicura l'interoperabilità tra i dati di file flat (come Excel e csv) e i dati XML. Questa app per le API può convertire un'istanza di file flat in XML e viceversa.
BizTalk Transform Service | L'app per le API di BizTalk Transform converte i dati da un formato in un altro formato. È anche possibile caricare una mappa esistente (file .trfm), visualizzare la rappresentazione grafica della mappa che mostra i collegamenti tra gli schemi di origine e di destinazione e usare la funzionalità 'Test' con un contenuto XML di input di esempio. Sono anche disponibili diverse funzioni integrate, tra cui manipolazioni di stringhe, operazioni Conditional Assignment, espressioni aritmetiche, formattatori di data e ora, cicli e così via.
BizTalk XML Validator | L'app per le API Validator convalida i dati XML rispetto a schemi XML predefiniti. È possibile usare gli schemi esistenti o generare schemi basati su un'istanza di file flat, su un'istanza JSON o su connettori esistenti.
BizTalk XPath Extractor | L'app per le API Extractor cerca ed estrae i dati dal contenuto XML in base a un'espressione XPath specificata.

Vedere [Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md) per altre informazioni e dettagli su queste app per le API.


## Connettori e app per le API EDI di BizTalk
Nella tabella seguente sono elencati tutti i connettori e le app per le API disponibili create da Microsoft, disponibili in EDI Services per BizTalk:

	Name  | Description
------------- | -------------
AS2 Connector | AS2 Connector può ricevere e inviare messaggi usando il protocollo di trasporto AS2 nelle comunicazioni business-to-business. I dati vengono trasportati in modo sicuro e affidabile tramite Internet mediante la crittografia e certificati digitali.
BizTalk EDIFACT | L'app per le API EDIFACT riceve e invia messaggi usando il protocollo EDIFACT nelle comunicazioni business-to-business.
BizTalk X12 | L'app per le API X12 riceve e invia messaggi usando il protocollo X12 nelle comunicazioni business-to-business.
BizTalk Trading Partner Management | L'app per le API di BizTalk Trading Partner Management definisce e mantiene le relazioni business-to-business tramite partner, contratti nonché schemi e certificati usati in contratti. Queste relazioni vengono applicate usando app per le API EDIFACT, AS2 e X12.

Vedere [connettori Business-to-Business](app-service-logic-b2b-connectors.md) per altre informazioni e dettagli su queste app per le API.


## App per le API BizTalk Rules
Nella tabella seguente sono elencati tutte le app per le API disponibili create da Microsoft, disponibili in BizTalk Rules:

	Name  | Description
------------- | -------------
BizTalk Rules | BizTalk Rules definisce e controlla la struttura, il funzionamento e la strategia di un'organizzazione. I criteri di business possono essere aggiornati senza ricompilare e ridistribuire le applicazioni associate.

Vedere[ Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md) per altre informazioni e dettagli su questa app per le API.


## Connettori Premium
Nella tabella seguente sono elencati tutti i connettori disponibili e le app per le API create da Microsoft disponibili in Connettori Premium:

	Name  | Description
------------- | -------------
DB2 Connector | DB2 Connector si connette a un database IBM DB2, in una macchina virtuale locale e di Azure in esecuzione su un sistema operativo Windows. Può associare le operazioni API Web e API OData ai comandi Informix Structured Query Language. Le azioni includono la tabella select, insert, update, delete e l'istruzione personalizzata<br/><br/>[Connettori per i servizi app e dati](app-service-logic-data-connectors.md)
Informix | Il connettore Informix si connette a un database IBM Informix locale e in una macchina virtuale locale e di Azure in esecuzione un sistema operativo Windows. Può associare le operazioni API Web e API OData ai comandi Informix Structured Query Language. Le azioni includono la tabella select, insert, update, delete e l'istruzione personalizzata <br/><br/>[Connettori per i servizi app e dati](app-service-logic-data-connectors.md)
MQ | Il connettore MQ si connette al Server IBM WebSphere MQ, in una macchina virtuale locale e di Azure in esecuzione su un sistema operativo Windows.<br/><br/><strong>Nota</strong> attualmente non può essere usato con app per la logica.<br/><br/>[Connettori per i servizi app e dati](app-service-logic-data-connectors.md)
Oracle Database | Il connettore di Database Oracle si connette al database Oracle locale e può creare, aggiornare, ottenere ed eliminare voci in una tabella di database. <br/><br/>[Connettori per i servizi app e dati](app-service-logic-data-connectors.md)
SAP | Il connettore SAP si connette a un server SAP locale e chiama RFC, BAPI e tRFC; anche, invia IDOC.<br/><br/>[Connettori aziendali](app-service-logic-enterprise-connectors.md)


## Creare connettori e app per le API personalizzati
Vedere [Creare i connettori con le API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


## Altri connettori

[Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md)<br/> [Connettori Enterprise](app-service-logic-enterprise-connectors.md)<br/> [Connettori di Business-to-Business](app-service-logic-b2b-connectors.md)<br/> [Connettori per social network](app-service-logic-social-connectors.md)<br/> [Connettori per protocolli](app-service-logic-protocol-connectors.md)<br/> [Connettori per i servizi app e dati](app-service-logic-data-connectors.md)<br/><br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54--> 