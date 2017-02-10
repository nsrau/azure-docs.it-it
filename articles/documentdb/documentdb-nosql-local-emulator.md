---
title: Eseguire lo sviluppo in locale con l&quot;emulatore di DocumentDB | Documentazione Microsoft
description: "Usare l&quot;emulatore di DocumentDB, è possibile sviluppare e testare gratuitamente l&quot;applicazione in locale, senza creare una sottoscrizione di Azure."
services: documentdb
documentationcenter: 
keywords: Emulatore di DocumentDB
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6c5bf8907a5f69e45e7b62fb466bdc53460e9029
ms.openlocfilehash: 86a5911e99e7631b09604afcb0f53ed2887b576b


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Usare l'emulatore di DocumentDB per sviluppo e test

[**Scaricare l'emulatore**](https://aka.ms/documentdb-emulator)

L'emulatore di Azure DocumentDB fornisce un ambiente locale che emula il servizio Azure DocumentDB a fini di sviluppo. Usando l'emulatore di DocumentDB, è possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore DocumentDB, è possibile iniziare a usare un account DocumentDB di Azure nel cloud.

È consigliabile guardare prima di tutto il video seguente, in cui Kirill Gavrylyuk illustra come iniziare a usare l'emulatore di DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>Requisiti di sistema dell'emulatore di DocumentDB
L'emulatore di DocumentDB presenta i requisiti hardware e software seguenti:

* Requisiti software
  * Windows Server 2012 R2, Windows Server 2016 o Windows Server 10
*   Requisiti hardware minimi
  * 2 GB di RAM
  * 10 GB di spazio su disco disponibile

## <a name="installing-the-documentdb-emulator"></a>Installazione dell'emulatore di DocumentDB
È possibile scaricare e installare l'emulatore di DocumentDB dall'[Area download Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Per installare, configurare ed eseguire l'emulatore di DocumentDB, è necessario avere i privilegi di amministratore nel computer.

## <a name="checking-for-documentdb-emulator-updates"></a>Ricerca di aggiornamenti per l'emulatore di DocumentDB
L'emulatore di DocumentDB include Esplora dati di Azure DocumentDB predefinito per esplorare i dati archiviati in DocumentDB, creare nuove raccolte e sapere quando un nuovo aggiornamento è disponibile per il download. 

> [!NOTE]
> I dati creati in una versione dell'emulatore di DocumentDB non sono necessariamente accessibili quando si usa una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, si consiglia di archiviare i dati in un account di Azure DocumentDB invece che nell'emulatore di DocumentDB. 

## <a name="how-the-documentdb-emulator-works"></a>Come funziona l'emulatore di DocumentDB
L'emulatore di DocumentDB garantisce un'emulazione estremamente fedele del servizio DocumentDB. Supporta le stesse funzionalità di Azure DocumentDB, incluso il supporto per la creazione e l'esecuzione di query su documenti JSON, il provisioning e la scalabilità delle raccolte e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di DocumentDB e distribuirle in Azure su scala globale semplicemente apportando una singola modifica di configurazione all'endpoint di connessione per DocumentDB.

Anche se è stata creata un'emulazione locale estremamente fedele del servizio DocumentDB effettivo, l'implementazione dell'emulatore di DocumentDB è diversa da quella del servizio. L'emulatore di DocumentDB, ad esempio, usa i componenti del sistema operativo standard, ad esempio il file system locale per la persistenza e lo stack di protocolli HTTPS per la connettività. Di conseguenza alcune funzionalità basate sull'infrastruttura di Azure, ad esempio la replica globale, la latenza in millisecondi a cifra singola per le operazioni di lettura/scrittura e i livelli di coerenza regolabili, non sono disponibili nell'emulatore di DocumentDB.


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>Autenticazione delle richieste nell'emulatore di DocumentDB
Come per Azure DocumentDB nel cloud, tutte le richieste effettuate nell'emulatore di DocumentDB devono essere autenticate. L'emulatore di DocumentDB supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione della chiave master. Questo account e questa chiave sono le uniche credenziali consentite per l'uso con l'emulatore di DocumentDB. Sono:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La chiave master supportata dall'emulatore di DocumentDB deve essere usata solo con l'emulatore. Non è possibile usare l'account di archiviazione DocumentDB e la chiave con l'emulatore di DocumentDB. 

Proprio come il servizio Azure DocumentDB, anche l'emulatore di DocumentDB supporta solo la comunicazione sicura tramite SSL.

## <a name="start-and-initialize-the-documentdb-emulator"></a>Avviare e inizializzare l'emulatore di DocumentDB

Per avviare l'emulatore di DocumentDB di Azure, selezionare il pulsante Start o premere WINDOWS sulla tastiera. Iniziare a digitare **emulatore di DocumentDB** e selezionare l'emulatore nell'elenco di applicazioni. 

![Selezionare il pulsante Start o premere il tasto WINDOWS, iniziare a digitare **emulatore di DocumentDB** e selezionare l'emulatore nell'elenco di applicazioni](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows. Per impostazione predefinita, l'emulatore di DocumentDB viene eseguito nel computer locale ("localhost") in ascolto sulla porta 8081.

![Notifica della barra delle applicazioni dell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

Per impostazione predefinita, l'emulatore di DocumentDB viene installato nella directory `C:\Program Files\DocumentDB Emulator`. È anche possibile avviare e arrestare l'emulatore dalla riga di comando. Per altre informazioni, vedere le [informazioni di riferimento sullo strumento da riga di comando](#command-line).

## <a name="start-the-local-emulator-data-explorer"></a>Avviare l'emulatore locale in Esplora dati

Quando l'emulatore locale viene avviato, apre automaticamente Esplora dati di DocumentDB nel browser. L'indirizzo visualizzato sarà [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Se si chiude lo strumento di esplorazione e lo si vuole riaprire in seguito, è possibile aprire l'URL nel browser o avviarlo dall'emulatore di DocumentDB usando l'icona dell'area di notifica di Windows, come illustrato sotto.

![Utilità di avvio di Esplora dati dell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>Sviluppo con l'emulatore di DocumentDB
Quando l'emulatore di DocumentDB è in esecuzione sul desktop, è possibile usare qualsiasi [DocumentDB SDK](documentdb-sdk-dotnet.md) supportato o l'[API REST di DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) per interagire con l'emulatore. L'emulatore di DocumentDB include anche l'utilità Esplora dati predefinita che consente di creare raccolte, visualizzare e modificare documenti senza scrivere codice. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> Quando ci si connette all'emulatore, è necessario impostare EnableEndpointDiscovery = false nella configurazione della connessione.

Se si usa il [supporto del protocollo DocumentDB per MongoDB](documentdb-protocol-mongodb.md), usare la stringa di connessione seguente:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

È possibile usare gli strumenti esistenti, ad esempio [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio), per connettersi all'emulatore di DocumentDB. È anche possibile eseguire la migrazione dei dati tra l'emulatore di DocumentDB e il servizio Azure DocumentDB usando lo [strumento per la migrazione dei dati di DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>Esportare il certificato DocumentDB dell'emulatore di DocumentDB

I linguaggi e il runtime .NET usano l'archivio certificati Windows per connettersi in modo sicuro all'emulatore locale di DocumentDB. Gli altri linguaggi hanno il proprio metodo di gestione e uso dei certificati. Java usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mentre Python usa i [wrapper per i socket](https://docs.python.org/2/library/ssl.html).

Per ottenere un certificato da usare con i linguaggi e i runtime che non si integrano con l'archivio certificati Windows, sarà necessario esportarlo usando Gestione certificati di Windows. Per avviarlo, eseguire certlm.msc o seguire le istruzioni dettagliate nel post "[Esportare i certificati dell'emulatore di DocumentDB](./documentdb-nosql-local-emulator-export-ssl-certificates.md)". Quando Gestione certificati è in esecuzione, aprire i certificati personali, come illustrato sotto, ed esportare il certificato con il nome descrittivo "DocumentDBEmulatorCertificate" come file Codificato Base 64 X.509 (.CER).

![Certificato SSL dell'emulatore locale di DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

Per importare il certificato X.509 nell'archivio certificati Java, seguire le istruzioni nel post "[Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)".  Dopo l'importazione del certificato nell'archivio cacerts, le applicazioni Java e MongoDB, potranno connettersi all'emulatore locale di DocumentDB.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>Informazioni di riferimento sullo strumento da riga di comando dell'emulatore di DocumentDB
Dal percorso di installazione è possibile usare la riga di comando per avviare e arrestare l'emulatore, configurare le opzioni ed eseguire altre operazioni.

### <a name="command-line-syntax"></a>Sintassi della riga di comando

    DocumentDB.Emulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

Per visualizzare l'elenco di opzioni, digitare `DocumentDB.Emulator.exe /?` al prompt dei comandi.

<table>
<tr>
  <td><strong>Opzione</strong></td>
  <td><strong>Descrizione</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argomenti</strong></td>
</tr>
<tr>
  <td>[Nessun argomento]</td>
  <td>Avvia l'emulatore di DocumentDB con le impostazioni predefinite.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Arresta l'emulatore di DocumentDB.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>Guida</td>
  <td>Visualizza l'elenco di argomenti della riga di comando.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>Specifica il percorso in cui archiviare i file di dati</td>
  <td>DocumentDB.Emulator.exe /datapath=&lt;percorsodati&gt;</td>
  <td>&lt;percorsodati&gt;: percorso accessibile</td>
</tr>
<tr>
  <td>Porta</td>
  <td>Specifica il numero di porta da usare per l'emulatore.  Il valore predefinito è 8081.</td>
  <td>DocumentDB.Emulator.exe /port=&lt;porta&gt;</td>
  <td>&lt;porta&gt;: numero di porta singolo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Specifica il numero di porta da usare per l'API di compatibilità MongoDB. Il valore predefinito è 10250.</td>
  <td>DocumentDB.Emulator.exe /mongoport=&lt;portamongo&gt;</td>
  <td>&lt;portaMongo&gt;: numero di porta singolo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Specifica le porte da usare per la connettività diretta. I valori predefiniti sono 10251, 10252, 10253 e 10254.</td>
  <td>DocumentDB.Emulator.exe /directports:&lt;portedirette&gt;</td>
  <td>&lt;portedirette&gt;: elenco delimitato da virgole di 4 porte.</td>
</tr>
<tr>
  <td>Chiave</td>
  <td>Chiave di autorizzazione per l'emulatore. La chiave deve essere la codifica Base 64 di un vettore a 64 byte.</td>
  <td>DocumentDB.Emulator.exe /key:&lt;chiave&gt;</td>
  <td>&lt;chiave&gt;: la chiave deve essere la codifica Base 64 di un vettore a 64 byte.</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>Specifica che il comportamento di limitazione delle richieste è abilitato.</td>
  <td>DocumentDB.Emulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>Specifica che il comportamento di limitazione delle richieste è disabilitato.</td>
  <td>DocumentDB.Emulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>Differenze tra l'emulatore di DocumentDB e Azure DocumentDB 
Poiché l'emulatore di DocumentDB fornisce un ambiente emulato eseguito in una workstation di sviluppo locale, esistono alcune differenze a livello di funzionalità tra l'emulatore e un account Azure DocumentDB nel cloud:

* L'emulatore di DocumentDB supporta solo un singolo account fisso e una chiave master nota.  La rigenerazione della chiave non è possibile nell'emulatore di DocumentDB.
* L'emulatore di DocumentDB non è un servizio scalabile e non supporta un numero elevato di raccolte.
* L'emulatore di DocumentDB non simula [livelli di coerenza di DocumentDB](documentdb-consistency-levels.md) diversi.
* L'emulatore di DocumentDB non simula la [replica tra più aree](documentdb-distribute-data-globally.md).
* L'emulatore di DocumentDB non supporta gli override della quota del servizio disponibili nel servizio Azure DocumentDB, ad esempio i limiti di dimensioni dei documenti e lo spazio di archiviazione per le raccolte aumentato.
* Poiché la copia dell'emulatore di DocumentDB potrebbe non essere aggiornata con le modifiche più recenti del servizio Azure DocumentDB, vedere [Capacity Planner di DocumentDB](https://www.documentdb.com/capacityplanner) per stimare con precisione le esigenze di produttività (UR) dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su DocumentDB, vedere [Introduzione ad Azure DocumentDB](documentdb-introduction.md).
* Per avviare lo sviluppo nell'emulatore di DocumentDB, scaricare uno degli [SDK di DocumentDB supportati](documentdb-sdk-dotnet.md).



<!--HONumber=Dec16_HO3-->


