---
title: Eseguire lo sviluppo in locale con l&quot;emulatore di Azure Cosmos DB | Microsoft Docs
description: "Usare l&quot;emulatore di Azure Cosmos DB, è possibile sviluppare e testare gratuitamente l&quot;applicazione in locale, senza creare una sottoscrizione di Azure."
services: cosmosdb
documentationcenter: 
keywords: Emulatore di Azure Cosmos DB
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmosdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 517819bfa17cb90a6f940200620e90ad352929d2
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Usare l'emulatore di Azure Cosmos DB per sviluppo e test locali

<table>
<tr>
  <td><strong>File binari</strong></td>
  <td>[Scarica MSI](https://aka.ms/documentdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Hub docker](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Origine docker</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
L'emulatore di Azure Cosmos DB fornisce un ambiente locale che emula il servizio Azure Cosmos DB a fini di sviluppo. Usando l'emulatore di Azure Cosmos DB, è possibile sviluppare e testare l'applicazione in locale, senza creare una sottoscrizione di Azure né sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore di Azure Cosmos DB, è possibile iniziare a usare l'account Azure Cosmos DB nel cloud.

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Installazione dell'emulatore
> * Esecuzione dell'emulatore in Docker per Windows
> * Autenticazione delle richieste
> * Uso di Esplora dati nell'emulatore
> * Esportazione di certificati SSL
> * Chiamata dell'emulatore dalla riga di comando
> * Raccolta dei file di traccia

È consigliabile guardare prima di tutto il video seguente, in cui Kirill Gavrylyuk illustra come iniziare a usare l'emulatore di Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="system-requirements"></a>Requisiti di sistema
L'emulatore di Azure Cosmos DB presenta i requisiti hardware e software seguenti:

* Requisiti software
  * Windows Server 2012 R2, Windows Server 2016 o Windows Server 10
*    Requisiti hardware minimi
  *    2 GB di RAM
  *    10 GB di spazio su disco disponibile

## <a name="installation"></a>Installare
È possibile scaricare e installare l'emulatore di Azure Cosmos DB dall'[Area download Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Per installare, configurare ed eseguire l'emulatore di Azure Cosmos DB, è necessario avere i privilegi di amministratore nel computer.

## <a name="running-on-docker-for-windows"></a>Esecuzione in Docker per Windows

L'emulatore di Azure Cosmos DB può essere eseguito in Docker per Windows. L'emulatore non funziona su Docker per Oracle Linux.

Dopo aver installato [Docker per Windows](https://www.docker.com/docker-windows) è possibile caricare l'immagine dell'emulatore da Docker Hub eseguendo il comando seguente dalla shell preferita: cmd.exe, PowerShell e così via.

```      
docker pull microsoft/azure-documentdb-emulator 
```
Per avviare l'immagine, eseguire i comandi seguenti.

``` 
md %LOCALAPPDATA%\DocumentDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\DocumentDBEmulatorCert:c:\DocumentDBEmulator\DocumentDBEmulatorCert -P -t -i microsoft/azure-documentdb-emulator 
```

La risposta sarà simile a quanto riportato di seguito:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Se si chiude la shell interattiva una volta avviato l'emulatore, il contenitore dell'emulatore verrà arrestato.

Usare l'endpoint e la chiave master dalla risposta nel client e importare il certificato SSL nell'host. Per importare il certificato SSL, eseguire le operazioni seguenti da un prompt dei comandi di amministratore:

```
cd %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
```

## <a name="checking-for-updates"></a>Preparazione per gli aggiornamenti
L'emulatore di Azure Cosmos DB include Esplora dati di Azure Cosmos DB predefinito per esplorare i dati archiviati in Azure Cosmos DB, creare nuove raccolte e sapere quando un nuovo aggiornamento è disponibile per il download. 

> [!NOTE]
> I dati creati in una versione dell'emulatore di Azure Cosmos DB non sono necessariamente accessibili quando si usa una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, si consiglia di archiviare i dati in un account di Azure Cosmos DB invece che nell'emulatore di Azure Cosmos DB. 

## <a name="how-the-emulator-works"></a>Come funziona l'emulatore
L'emulatore Azure Cosmos DB offre un'emulazione ultra fedele del servizio Azure Cosmos DB. Supporta le stesse funzionalità di Azure Cosmos DB, incluso il supporto per la creazione e l'esecuzione di query su documenti JSON, il provisioning e la scalabilità delle raccolte e l'esecuzione di stored procedure e trigger. È possibile sviluppare e testare le applicazioni usando l'emulatore di Azure Cosmos DB e distribuirle in Azure su scala globale semplicemente apportando una singola modifica di configurazione all'endpoint di connessione per Azure Cosmos DB.

Anche se è stata creata un'emulazione locale estremamente fedele del servizio Azure Cosmos DB effettivo, l'implementazione dell'emulatore di Azure Cosmos DB è diversa da quella del servizio. L'emulatore di Azure Cosmos DB, ad esempio, usa i componenti del sistema operativo standard, ad esempio il file system locale per la persistenza e lo stack di protocolli HTTPS per la connettività. Di conseguenza alcune funzionalità basate sull'infrastruttura di Azure, ad esempio la replica globale, la latenza in millisecondi a cifra singola per le operazioni di lettura/scrittura e i livelli di coerenza regolabili, non sono disponibili nell'emulatore di Azure Cosmos DB.


## <a name="authenticating-requests"></a>Autenticazione delle richieste
Come per Azure Document nel cloud, tutte le richieste effettuate nell'emulatore di Azure Cosmos DB devono essere autenticate. L'emulatore di Azure Cosmos DB supporta un singolo account fisso e una chiave di autenticazione nota per l'autenticazione della chiave master. Questo account e questa chiave sono le uniche credenziali consentite per l'uso con l'emulatore di Azure Cosmos DB. Sono:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La chiave master supportata dall'emulatore di Azure Cosmos DB deve essere usata solo con l'emulatore. È possibile utilizzare l'account Azure Cosmos DB di produzione e la chiave con l'emulatore di Azure Cosmos DB. 

Proprio come il servizio Azure Cosmos DB, anche l'emulatore di Azure Cosmos DB supporta solo la comunicazione sicura tramite SSL.

## <a name="start-and-initialize-the-emulator"></a>Avviare e inizializzare l'emulatore

Per avviare l'emulatore di Azure Cosmos DB, selezionare il pulsante Start o premere WINDOWS sulla tastiera. Iniziare a digitare **emulatore di Azure Cosmos DB**e selezionare l'emulatore nell'elenco di applicazioni. 

![Selezionare il pulsante Start o premere il tasto WINDOWS, iniziare a digitare **emulatore di Azure Cosmos DB** e selezionare l'emulatore nell'elenco di applicazioni](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows. Per impostazione predefinita, l'emulatore di Azure Cosmos DB viene eseguito nel computer locale ("localhost") in ascolto sulla porta 8081.

![Notifica della barra delle applicazioni dell'emulatore locale di Azure Cosmos DB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

Per impostazione predefinita, l'emulatore di Azure Cosmos DB viene installato nella directory `C:\Program Files\Azure Cosmos DB Emulator`. È anche possibile avviare e arrestare l'emulatore dalla riga di comando. Per altre informazioni, vedere le [informazioni di riferimento sullo strumento da riga di comando](#command-line).

## <a name="start-data-explorer"></a>Avviare Esplora dati

Quando l'emulatore di Azure Cosmos DB viene avviato, apre automaticamente Esplora dati di Azure Cosmos DB nel browser. L'indirizzo visualizzato sarà [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Se si chiude lo strumento di esplorazione e lo si vuole riaprire in seguito, è possibile aprire l'URL nel browser o avviarlo dall'emulatore di Azure Cosmos DB usando l'icona dell'area di notifica di Windows, come illustrato sotto.

![Utilità di avvio di Esplora dati dell'emulatore locale di Azure Cosmos DB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-emulator"></a>Sviluppo con l'emulatore
Quando l'emulatore di Azure Cosmos DB è in esecuzione sul desktop, è possibile usare qualsiasi [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) supportato o l'[API REST di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781481.aspx) per interagire con l'emulatore. L'emulatore di Azure Cosmos DB include anche l'utilità Esplora dati predefinita che consente di creare raccolte, visualizzare e modificare documenti senza scrivere codice. 

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Se si usa il [supporto del protocollo Azure Cosmos DB per MongoDB](documentdb-protocol-mongodb.md), usare la stringa di connessione seguente:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

È possibile usare gli strumenti esistenti, ad esempio [Azure Cosmos DB Studio](https://github.com/mingaliu/Azure Cosmos DBStudio), per connettersi all'emulatore di Azure Cosmos DB. È anche possibile eseguire la migrazione dei dati tra l'emulatore di Azure Cosmos DB e il servizio Azure Cosmos DB usando lo [strumento per la migrazione dei dati di Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Usando l'emulatore di Azure Cosmos DB, per impostazione predefinita è possibile creare fino a 25 raccolte con partizione singola o una raccolta partizionata. Per altre informazioni sulla modifica di questo valore, vedere la sezione su come [impostare il valore PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Esportare il certificato SSL

I linguaggi e il runtime .NET usano l'archivio certificati Windows per connettersi in modo sicuro all'emulatore locale di Azure Cosmos DB. Gli altri linguaggi hanno il proprio metodo di gestione e uso dei certificati. Java usa il proprio [archivio certificati](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mentre Python usa i [wrapper per i socket](https://docs.python.org/2/library/ssl.html).

Per ottenere un certificato da usare con i linguaggi e i runtime che non si integrano con l'archivio certificati Windows, sarà necessario esportarlo usando Gestione certificati di Windows. Per avviarlo, eseguire certlm.msc o seguire le istruzioni dettagliate nel post [Export the Azure Cosmos DB Emulator Certificates](./documentdb-nosql-local-emulator-export-ssl-certificates.md) (Esportare i certificati dell'emulatore di Azure Cosmos DB). Quando Gestione certificati è in esecuzione, aprire i certificati personali, come illustrato sotto, ed esportare il certificato con il nome descrittivo "Azure Cosmos DBEmulatorCertificate" come file Codificato Base 64 X.509 (.CER).

![Certificato SSL dell'emulatore locale di Azure Cosmos DB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

Per importare il certificato X.509 nell'archivio certificati Java, seguire le istruzioni disponibili in [Aggiunta di un certificato all'archivio certificati CA Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Dopo l'importazione del certificato nell'archivio cacerts, le applicazioni Java e MongoDB, potranno connettersi all'emulatore di Azure Cosmos DB.

Quando ci si connette all'emulatore da Python e Node.js SDK, la verifica SSL è disabilitata.

## <a id="command-line"></a>Riferimenti allo strumento da riga di comando
Dal percorso di installazione è possibile usare la riga di comando per avviare e arrestare l'emulatore, configurare le opzioni ed eseguire altre operazioni.

### <a name="command-line-syntax"></a>Sintassi della riga di comando

    DocumentDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

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
  <td>Avvia l'emulatore di Azure Cosmos DB con le impostazioni predefinite.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Help]</td>
  <td>Visualizza l'elenco di argomenti della riga di comando supportati.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Arresta l'emulatore di Azure Cosmos DB.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Specifica il percorso in cui archiviare i file di dati. Il valore predefinito è %LocalAppdata%\DocumentDBEmulator.</td>
  <td>DocumentDB.Emulator.exe /DataPath=&lt;percorsodati&gt;</td>
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
  <td>EnableRateLimiting</td>
  <td>Specifica che il comportamento di limitazione della frequenza è abilitato.</td>
  <td>DocumentDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Specifica che il comportamento di limitazione della frequenza è disabilitato.</td>
  <td>DocumentDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Non mostra l'interfaccia utente dell'emulatore.</td>
  <td>DocumentDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Non mostra Esplora documenti all'avvio.</td>
  <td>DocumentDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Specifica il numero massimo di raccolte partizionate. Per altre informazioni vedere [Modificare il numero di raccolte](#set-partitioncount).</td>
  <td>DocumentDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: numero massimo di raccolte con partizione singola consentite. Il valore predefinito è 25. Il valore massimo consentito è 250.</td>
</tr>
</table>

## <a name="differences-between-the-azure-cosmos-db-emulator-and-azure-cosmos-db"></a>Differenze tra l'emulatore di Azure Cosmos DB e Azure Cosmos DB 
Poiché l'emulatore di Azure Cosmos DB fornisce un ambiente emulato eseguito in una workstation di sviluppo locale, esistono alcune differenze a livello di funzionalità tra l'emulatore e un account Azure Cosmos DB nel cloud:

* L'emulatore di Azure Cosmos DB supporta solo un singolo account fisso e una chiave master nota.  La rigenerazione della chiave non è possibile nell'emulatore di Azure Cosmos DB.
* L'emulatore di Azure Cosmos DB non è un servizio scalabile e non supporta un numero elevato di raccolte.
* L'emulatore di Azure Cosmos DB non simula [livelli di coerenza di Azure Cosmos DB](documentdb-consistency-levels.md) diversi.
* L'emulatore di Azure Cosmos DB non simula la [replica tra più aree](documentdb-distribute-data-globally.md).
* L'emulatore di Azure Cosmos DB non supporta gli override della quota del servizio disponibili nel servizio Azure Cosmos DB, ad esempio i limiti di dimensioni dei documenti e lo spazio di archiviazione per le raccolte aumentato.
* Poiché la copia dell'emulatore di Azure Cosmos DB potrebbe non essere aggiornata con le modifiche più recenti del servizio Azure Cosmos DB, vedere [Capacity Planner di Azure Cosmos DB](https://www.documentdb.com/capacityplanner) per stimare con precisione le esigenze di produttività (UR) dell'applicazione.

## <a id="set-partitioncount"></a>Modificare il numero di raccolte

Per impostazione predefinita, è possibile creare fino a 25 raccolte con partizione singola o una raccolta partizionata usando l'emulatore di Azure Cosmos DB. Modificando il valore **PartitionCount**, è possibile creare fino a 250 raccolte a partizione singola o 10 raccolte partizionate o qualsiasi combinazione delle due che non contenga più di 250 raccolte a partizione singola (dove una raccolta partizionata = 25 raccolte a partizione singola).

Se si tenta di creare una raccolta dopo il superamento di questi limiti, l'emulatore genera un'eccezione ServiceUnavailable con il messaggio seguente.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Per modificare il numero di raccolte disponibili nell'emulatore di Azure Cosmos DB, procedere come segue:

1. Eliminare tutti i dati locali dell'emulatore di Azure Cosmos DB facendo clic sull'icona dell'**emulatore di Azure Cosmos DB**  sull'area di notifica e quindi su **Reset data** (Reimposta dati).
2. Eliminare tutti i dati dell'emulatore dalla cartella C:\Users\nome_utente\AppData\Local\DocumentDBEmulator.
3. Chiudere tutte le istanze aperte facendo clic con il pulsante destro del mouse sull'icona dell'**emulatore di Azure Cosmos DB** sull'area di notifica, quindi fare clic su **Esci**. La chiusura di tutte le istanze può richiedere qualche minuto.
4. Installare la versione più recente dell'[emulatore di Azure Cosmos DB](https://aka.ms/documentdb-emulator).
5. Avviare l'emulatore con il flag PartitionCount impostando un valore <= 250. Ad esempio: `C:\Program Files\DocumentDB Emulator>DocumentDB.Emulator.exe /PartitionCount=100`.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Usa i suggerimenti seguenti per aiutare a risolvere i problemi dell'emulatore di Azure Cosmos DB:

- In caso di arresto anomalo dell'emulatore di Azure Cosmos DB, raccogliere file di dump dalla cartella c:\Users\nome_utente\AppData\Local\CrashDumps, comprimerli e allegarli a un messaggio di posta elettronica da inviare all'indirizzo [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

- In caso di arresti anomali del sistema in DocumentDB.StartupEntryPoint.exe, eseguire il comando seguente da un prompt dei comandi di amministratore:`lodctr /R` 

- In caso di problemi di connettività, [raccogliere i file di traccia](#trace-files), comprimerli e allegarli a un messaggio di posta elettronica da inviare all'indirizzo [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a id="trace-files"></a>Raccogliere i file di traccia

Per raccogliere le tracce di debug, eseguire i comandi seguenti da un prompt dei comandi amministrativi:

1. `cd /d "%ProgramFiles%\DocumentDB Emulator"`
2. `DocumentDB.Emulator.exe /shutdown`. Verificare l'area di notifica per assicurarsi che il programma sia stato arrestato. Potrebbe essere necessario qualche minuto. È anche possibile fare semplicemente clic su **Esci** nell'interfaccia utente dell'emulatore di DocumentDB.
3. `DocumentDB.Emulator.exe /starttraces`
4. `DocumentDB.Emulator.exe`
5. Riprodurre il problema. Se Esplora dati non funziona, è sufficiente attendere qualche secondo per l'apertura del browser e il rilevamento dell'errore.
5. `DocumentDB.Emulator.exe /stoptraces`
6. Passare a `%ProgramFiles%\DocumentDB Emulator` e individuare il file docdbemulator_000001.etl.
7. Inviare il file con estensione etl insieme alla procedura per la riproduzione all'indirizzo [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) per il debug.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Installazione dell'emulatore locale
> * Esecuzione dell'emulatore in Docker per Windows
> * Autenticazione delle richieste
> * Uso di Esplora dati nell'emulatore
> * Esportazione dei certificati SSL
> * Chiamata dell'emulatore dalla riga di comando
> * Raccolta dei file di traccia

In questa esercitazione, si è appresso come usare l'emulatore locale per sviluppo locale libero. È ora possibile passare all'esercitazione successiva e apprendere come esportare i certificati SSL dell'emulatore. 

> [!div class="nextstepaction"]
> [Esportare i certificati dell'emulatore Azure Cosmos DB](documentdb-nosql-local-emulator-export-ssl-certificates.md)

