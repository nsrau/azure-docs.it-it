<properties 
	pageTitle="Connettori dati delle app per le API di Microsoft Azure | Microservizio app per le API" 
	description="Informazioni su come creare app per le API per i connettori dati di Microsoft Azure e aggiungere l'app per le API all'app per la logica; microservizi" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="deonhe"/>


# Connettori dati nel servizio app di Microsoft Azure

> [AZURE.NOTE]Questo argomento verrà ritirato. Per vedere tutte le app per le API e tutti i connettori predefiniti, fare riferimento all'[Elenco di connettori e app per le API](app-service-logic-connectors-list.md).


## Cos'è un connettore app per le API dei servizi app e dati
I connettori dei servizi app e dati sono "app per le API" che possono connettersi a numerose applicazioni di servizi dati ed eseguire diverse *Azioni* per conto dell'utente autenticato. La maggior parte di questi connettori può anche essere configurata con un *Trigger*. I trigger sono eventi (simili agli eventi in .NET Framework) che possono essere configurati in alcuni connettori per avviare un flusso di lavoro.

Ad esempio, un'istanza del connettore Dropbox può essere configurata con un *nuovo file* in cui il trigger verrà eseguito ogni volta che un nuovo file verrà aggiunto all'account Dropbox da monitorare. Questo trigger può quindi essere configurato per avviare un'*Azione* che *otterrà* il file e lo caricherà in un elenco SharePoint locale.


Ecco una breve panoramica di ogni connettore dei servizi app e dati disponibile nella raccolta di Azure:

<table>
<tr>
<th> Nome</th>
<th> Descrizione</th>
<th> Trigger</th>
<th>Actions</th>

<tr>
<td>Servizi multimediali di Azure
<td>Il connettore Servizi multimediali di Azure consente di creare flussi di lavoro multimediali end-to-end con codifica, creazione di pacchetti e distribuzione flessibili e scalabili. È anche possibile caricare, archiviare, codificare e trasmettere in sicurezza contenuti video o audio per la distribuzione su richiesta e in modalità streaming live a vari tipi di endpoint TV, PC e dispositivi mobili.
<td>Nessuno
<td>Nessuno 
</tr>

<tr>
<td>Bus di servizio di Azure
<td>Il connettore Bus di servizio di Azure consente di inviare messaggi dalle entità del bus di servizio, ad esempio code e argomenti, e di ricevere messaggi dalle entità del bus di servizio, ad esempio code e sottoscrizioni.
<td>Nuovi messaggi
<td>Invio di un messaggio
</tr>

<tr>
<td>Box
<td>Il connettore Box consente di connettersi a Box e di eseguire diverse azioni sui file. 
<td>Nuovi file aggiunti
<td><li>Caricamento file
<li>Acquisizione file
<li>Eliminazione file
<li>Elenco file
</tr>

<tr>
<td>DB2
<td>DB2 Connector consente di connettersi a un database IBM DB2, in una macchina virtuale locale e di Azure in esecuzione su un sistema operativo Windows. Può associare le operazioni API Web e API OData ai comandi Informix Structured Query Language. Quando si usa localmente, è possibile usare VPN o ExpressRoute di Azure. Questo connettore include anche il client Microsoft per DRDA per connettersi a un server Informix mediante una rete TCP/IP.
<td>Nessuno
<td><li>Selezione tabella
<li>Inserimento
<li>Aggiorna
<li>Elimina
<li>Istruzione personalizzata
</tr>

<tr>
<td>Dropbox
<td>Il connettore Dropbox consente di connettersi a Dropbox e di eseguire diverse azioni, ad esempio il caricamento, l'acquisizione e l'eliminazione di un file e la creazione dell'elenco dei file, e un trigger per recuperare i file.
<td>Nuovi file aggiunti
<td><li>Caricamento file
<li>Acquisizione file
<li>Eliminazione file
<li>Elenco file
</tr>

<tr>
<td>HDInsight
<td>Un connettore HDInsight consente di creare un cluster Hadoop in Azure e di inviare diversi processi Hadoop, ad esempio Hive, Pig, MapReduce e Streaming MapReduce. Con questo connettore, è possibile avviare un cluster, inviare un processo e attendere il completamento del processo.
<td>Nessuno
<td><li>Creazione di un cluster
		<li>Attesa della creazione di un cluster
		<li>Invio di un processo Pig
		<li>Invio di un processo Hive
		<li>Invio di un processo MapReduce
		<li>Attesa del completamento di un processo
		<li>Eliminazione di un cluster
<li>Invio di un processo MapReduce
<li>Invio di un processo Streaming MapReduce
</tr>

<tr>
<td>Informix
<td>Il connettore Informix si connette a un database IBM Informix locale e in una macchina virtuale locale e di Azure in esecuzione un sistema operativo Windows. Può associare le operazioni API Web e API OData ai comandi Informix Structured Query Language. Quando si usa localmente, è possibile usare VPN o ExpressRoute di Azure. Questo connettore include anche un client Microsoft per DRDA per connettersi a un server Informix mediante una rete TCP/IP.
<td>Nessuno
<td><li>Selezione tabella
<li>Inserimento
<li>Aggiorna
<li>Elimina
<li>Istruzione personalizzata
</tr>

<tr>
<td>Microsoft SQL
<td>Un connettore Microsoft SQL consente di creare e modificare voci in tabelle di database di Microsoft SQL Server e SQL di Azure.
<td>Dati di polling
<td><li>Inserimento nella tabella
	<li>Aggiornamento della tabella
	<li>Selezione dalla tabella
	<li>Eliminazione dalla tabella
	<li>Chiamata a una stored procedure
</tr>


<tr>
<td>MQ
<td>MQ Connector si connette a un database IBM WebSphere MQ Server versione 8, in una macchina virtuale locale e di Azure in esecuzione su un sistema operativo Windows. Quando si usa localmente, è possibile usare VPN o ExpressRoute di Azure. Il connettore include anche il client Microsoft per MQ.<br/><br/><strong>Nota</strong> Attualmente non può essere usato con App per la logica.
<td>Nessuno
<td>Nessuno
</tr>

<tr>
<td>Office 365
<td>Un connettore Office 365 consente di inviare e ricevere messaggi di posta elettronica e di gestire il calendario e i contatti.
<td>Nuovo messaggio
<td>	<li>Invio di un messaggio
		<li>Risposta a un messaggio
		<li>Invio di un evento
		<li>Aggiunta di un contatto
</tr>

<tr>
<td>OneDrive
<td>Il connettore OneDrive consente di connettersi all'account di archiviazione Microsoft OneDrive personale e di eseguire diverse azioni, ad esempio il caricamento, l'acquisizione, l'eliminazione di file e la creazione di un elenco di file.
<td>Nuovo file
<td><li>File: caricamento, eliminazione, creazione di un elenco, download
</tr>

<tr>
<td>Oracle
<td>Un connettore Oracle Database consente di creare e modificare voci in una tabella di database Oracle.
<td>Nuovi dati, basati su una query
<td><li>Tabella: inserimento, aggiornamento, selezione, eliminazione
<li>Chiamata a una stored procedure
</tr>

<tr>
<td>SharePoint Online
<td>Un connettore Microsoft SharePoint consente di creare e modificare documenti ed elementi elenco in SharePoint Server o Microsoft SharePoint Online.
<td><li>Nuovo documento
<li>Nuovo elemento elenco
<td><li>Libreria documenti: caricare, eliminare e ottenere
<li>Elenco: Inserisci elemento

</tr>

<tr>
<td>SharePoint Server
<td>Il connettore SharePoint Server consente di gestire documenti ed elementi elenco nel server SharePoint. Sono supportate le credenziali predefinite, l'autenticazione Windows e l'autenticazione basata su moduli. È necessario fornire una stringa di connessione per il bus di servizio e installare l'agente listener locale prima di connettersi al server.
<td><li>Nuovo documento
<li>Nuovo elemento elenco
<td><li>Libreria documenti: caricare, eliminare e ottenere
<li>Elenco: Inserisci elemento
</tr>
</table>


## Perché usare i connettori?

I connettori accelerano lo sviluppo delle app e consentono anche a chi non è uno sviluppatore di creare applicazioni aziendali completamente funzionali senza dover imparare un linguaggio di programmazione o scrivere alcun codice.

Dopo aver illustrato le funzionalità dei connettori dei servizi app e dati, verranno esaminati alcuni semplici casi di utilizzo per questi connettori.

### Monitoraggio di Dropbox e aggiornamento di SharePoint
Si supponga che la propria società sia un'impresa edile che riceve file di grandi dimensioni contenenti progetti. Questi file in genere sono troppo grandi per essere gestiti tramite posta elettronica. La società configura quindi un account Dropbox e chiede ai clienti di rilasciare i progetti in Dropbox. A questo punto si potrebbe chiedere a ogni dipendente di controllare continuamente le cartelle Dropbox per verificare se sono presenti nuovi progetti e poi di caricarli sul server SharePoint, ma esiste sicuramente un modo migliore. Fortunatamente Microsoft ha rilasciato di recente il connettore per i servizi app e dati per Dropbox, Sharepoint e altri servizi dati. Si possono facilmente creare connettori per Dropbox e SharePoint, aggiungerli a un'app per la logica e configurarli in modo che carichino ogni nuovo file dall'account Dropbox all'elenco SharePoint. Poiché il connettore Dropbox ha un *nuovo messaggio*, è possibile usarlo per notificare all'app per la logica che è disponibile un nuovo file. Il connettore Dropbox può quindi scaricare il file. Il connettore Sharepoint può quindi essere configurato in modo da caricare il file in un elenco SharePoint con l'azione *upload* di SharePoint. Tutto questo è possibile senza scrivere una singola riga di codice.

Di seguito sono riportati i requisiti iniziali.

## Creare un connettore
Le app per le API per i connettori possono essere create usando il portale di Azure

### Creare un connettore SharePoint nel portale di Microsoft Azure

1. Nel portale di Azure selezionare **NUOVO **> **Web e dispositivi mobili** > **Azure Marketplace**
2. **Cercare** il connettore o selezionarlo nell'elenco. Una volta selezionato, si apre un nuovo pannello o finestra. Selezionare **Crea**. 
3. Immettere le seguenti proprietà per il connettore: 
	<table>
    <tr><th>Proprietà</th> <th>Descrizione</th> </tr>
    <tr><td>Nome</td> <td>Immettere un nome per l'app per le API, ad esempio RulesDiscountTaxCode o APIAppValidateXML.</td> </tr>
    <tr><td>Piano di servizio app</td> <td>Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.</th> </td>
    <tr><td>Piano tariffario</td> <td>Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure.</td> </tr>
    <tr><td>Gruppo di risorse</td> <td>Creare un nuovo gruppo di risorse o usare un gruppo esistente. L'articolo Uso dei gruppi di risorse illustra questa proprietà.</td> </tr>
    <tr><td>Sottoscrizione</td> <td>Proprietà di sola lettura che elenca la sottoscrizione corrente.</td> </tr>
    <tr><td>Location</td> <td>La località geografica che ospita il servizio di Azure. </td></tr>
    <tr><td>Aggiungi a schermata iniziale</td> <td>Selezionare questa opzione per aggiungere l'app per le API alla schermata iniziale (home page).</td></tr>
</table>
4. Selezionare **Crea**. Il connettore verrà creato. Il completamento dell'operazione può richiedere tempo. Durante la creazione del connettore verrà visualizzata la schermata iniziale. Usare la voce di menu Notifiche sulla sinistra per monitorare lo stato del connettore.

Dopo aver creato il primo connettore, è possibile usarlo per compilare un'app per la logica, mobile o Web.


### Accedere a un connettore con le API REST

[Accedere ai connettori con le API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Aggiungere il connettore a un'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare questi connettori. Ad esempio, è possibile creare un'app per la *logica* combinando uno o più connettori *logicamente* in un'unica app.

Per usare i connettori nell'app per l'app per la *logica*, è sufficiente selezionare un connettore preconfigurato nell'elenco, aggiungerlo al flusso di lavoro di progettazione e apportare le modifiche necessarie alla configurazione.

Per seguire questa procedura, è necessaria un'app Web, un'app mobile o un'app per la logica. Vedere <> per i passaggi specifici. Una volta che l'applicazione è disponibile, aggiungere i connettori. Ecco come:

Usare i passaggi seguenti per aggiungere un connettore a un'app per la logica:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare la propria app per la logica, per dispositivi mobili o Web. 

	Se si sta creando una nuova app, cercare un'app per la logica, un'app mobile o un'app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.
3. Nella **Raccolta** selezionare il connettore. Verrà aggiunto all'app.
4. Configurare il connettore:
5. Le proprietà di ogni connettore sono specifiche del servizio e dell'ambiente a cui si connette. Immettere i dettagli per le proprietà. Tenere presente che alcune proprietà sono facoltative.
6. Selezionare **OK** per salvare le modifiche.


## Sicurezza
I connettori usano OAuth oppure nomi utente e password.


## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md) | [Siti Web e app Web nel servizio app di Azure](../app-service-web/app-service-web-overview.md) |



## Altri connettori

[Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> [Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=July15_HO4-->