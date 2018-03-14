---
title: Usare l'emulatore di archiviazione di Azure per sviluppo e test | Microsoft Docs
description: "L'emulatore di archiviazione di Azure offre un ambiente di sviluppo locale gratuito per sviluppare e testare applicazioni di Archiviazione di Azure. Informazioni sulle modalità di autenticazione delle richieste, su come connettersi all'emulatore dall'applicazione e su come usare lo strumento da riga di comando."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: tamram
ms.openlocfilehash: 13aee7bbbe58c0a4183eddc0881aaed8cbebd956
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Usare l'emulatore di archiviazione di Azure per sviluppo e test

L'emulatore di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. Usando l'emulatore di archiviazione, è possibile testare l'applicazione con i servizi di archiviazione in locale, senza creare una sottoscrizione di Azure o sostenere alcun costo. Quando si è soddisfatti della modalità di funzionamento dell'applicazione nell'emulatore, è possibile passare all'utilizzo di un account di archiviazione di Azure nel cloud.

## <a name="get-the-storage-emulator"></a>Ottenere l'emulatore di archiviazione
L'emulatore di archiviazione è disponibile come parte di [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). È anche possibile installare l'emulatore di archiviazione usando il [programma di installazione autonomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download diretto). Per installare l'emulatore di archiviazione, è necessario avere privilegi di amministratore nel computer.

L'emulatore di archiviazione attualmente viene eseguito solo in Windows. Per coloro che stanno valutando un emulatore di archiviazione per Linux, un'opzione è l'emulatore di archiviazione open source gestito dalla community [Azurite](https://github.com/arafato/azurite).

> [!NOTE]
> I dati creati in una versione dell'emulatore di archiviazione non sono necessariamente accessibili quando si usa una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, è consigliabile archiviare i dati in un account di archiviazione di Azure invece che nell'emulatore di archiviazione.
> <p/>
> L'emulatore di archiviazione dipende dalle versioni specifiche delle librerie OData. La sostituzione delle DLL OData usate dall'emulatore di archiviazione con altre versioni non è supportata e può provocare un comportamento imprevisto. Tutte le versioni di OData supportate dal servizio di archiviazione possono tuttavia essere usate per inviare richieste all'emulatore.
>

## <a name="how-the-storage-emulator-works"></a>Come funziona l'emulatore di archiviazione
L'emulatore di archiviazione usa un'istanza di Microsoft SQL Server locale e il file system locale per emulare i servizi di archiviazione di Azure. Per impostazione predefinita, l'emulatore di archiviazione usa un database in Microsoft SQL Server 2012 Express LocalDB. È possibile scegliere di configurare l'emulatore di archiviazione per accedere a un'istanza locale di SQL Server anziché l'istanza LocalDB. Per altre informazioni, vedere la sezione [Avviare e inizializzare l'emulatore di archiviazione](#start-and-initialize-the-storage-emulator) più avanti in questo articolo.

L'emulatore di archiviazione si connette a SQL Server o a LocalDB usando l'autenticazione Windows.

Esistono alcune differenze a livello di funzionalità tra l'emulatore di archiviazione e i servizi di archiviazione di Azure. Per altre informazioni su queste differenze, vedere la sezione [Differenze tra l'emulatore di archiviazione e Archiviazione di Azure](#differences-between-the-storage-emulator-and-azure-storage) più avanti in questo articolo.

## <a name="start-and-initialize-the-storage-emulator"></a>Avviare e inizializzare l'emulatore di archiviazione

### <a name="run-the-azure-storage-emulator-in-dockerhttpshubdockercomrmicrosoftazure-storage-emulator"></a>[Eseguire l'emulatore di archiviazione di Azure in Docker](https://hub.docker.com/r/microsoft/azure-storage-emulator/)
```
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 microsoft/azure-storage-emulator
```

### <a name="using-sdk"></a>Uso dell'SDK

Per avviare l'emulatore di archiviazione di Azure:
1. Fare clic sul pulsante **Start** oppure premere il tasto **WINDOWS**.
1. Iniziare a digitare `Azure Storage Emulator`.
1. Selezionare l'emulatore nell'elenco delle applicazioni visualizzate.

All'avvio dell'emulatore di archiviazione, viene visualizzata una finestra del prompt dei comandi. È possibile usare questa finestra della console per avviare e arrestare l'emulatore di archiviazione, cancellare i dati, ottenere lo stato e inizializzare l'emulatore. Per altre informazioni, vedere la sezione [Informazioni di riferimento sullo strumento da riga di comando dell'emulatore di archiviazione](#storage-emulator-command-line-tool-reference) più avanti in questo articolo.

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows.

Quando si chiude la finestra del prompt dei comandi dell'emulatore di archiviazione, questo resta in esecuzione. Per visualizzare di nuovo la finestra della console dell'emulatore di archiviazione, eseguire la procedura precedente come per avviare l'emulatore di archiviazione.

La prima volta che si esegue l'emulatore di archiviazione, l'ambiente di archiviazione locale viene inizializzato automaticamente. Il processo di inizializzazione crea un database in LocalDB e riserva le porte HTTP per ogni servizio di archiviazione locale.

Per impostazione predefinita, l'emulatore di archiviazione viene installato in `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator`.

> [!TIP]
> È possibile usare [Microsoft Azure Storage Explorer](http://storageexplorer.com) per usare risorse dell'emulatore di archiviazione locale. Cercare "(Sviluppo)" in "Account di archiviazione" nell'albero delle risorse di Storage Explorer dopo aver installato e avviato l'emulatore di archiviazione.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inizializzare l'emulatore di archiviazione per l'uso di un database SQL diverso
È possibile usare lo strumento della riga di comando Emulatore di archiviazione per inizializzare l'emulatore di archiviazione in modo che punti a un'istanza di database SQL diversa dall'istanza LocalDB predefinita:

1. Aprire la finestra della console dell'emulatore di archiviazione come descritto nella sezione [Avviare e inizializzare l'emulatore di archiviazione](#start-and-initialize-the-storage-emulator).
1. Nella finestra della console digitare il comando seguente, dove `<SQLServerInstance>` è il nome dell'istanza di SQL Server. Per usare LocalDB, specificare `(localdb)\MSSQLLocalDb` come istanza di SQL Server.

  `AzureStorageEmulator.exe init /server <SQLServerInstance>`

  Per indicare all'emulatore di usare l'istanza di SQL Server predefinita, digitare il comando seguente:

  `AzureStorageEmulator.exe init /server .\\`

  In alternativa, è possibile usare il comando seguente, che consente di inizializzare nuovamente l'istanza predefinita LocalDB:

  `AzureStorageEmulator.exe init /forceCreate`

Per altre informazioni su questi comandi, vedere [Informazioni di riferimento sullo strumento da riga di comando dell'emulatore di archiviazione](#storage-emulator-command-line-tool-reference).

> [!TIP]
> È possibile usare [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per gestire le istanze di SQL Server, inclusa l'installazione di LocalDB. Nella finestra di dialogo **Connetti al server** di SMSS specificare `(localdb)\MSSQLLocalDb` nel campo **Nome server** per connettersi all'istanza di LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticazione delle richieste nell'emulatore di archiviazione
Dopo aver installato e avviato l'emulatore di archiviazione, è possibile testare il codice sull'emulatore. Come per Archiviazione di Azure nel cloud, tutte le richieste effettuate nell'emulatore di archiviazione devono essere autenticate, a meno che non si tratti di richieste anonime. È possibile autenticare le richieste nell'emulatore di archiviazione tramite l'autenticazione con chiave condivisa o con una firma di accesso condiviso.

### <a name="authenticate-with-shared-key-credentials"></a>Autenticazione con credenziali con chiave condivisa
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](../storage-configure-connection-string.md).

### <a name="authenticate-with-a-shared-access-signature"></a>Eseguire l'autenticazione con una firma di accesso condiviso
Alcune librerie client dell'archiviazione di Azure, ad esempio la libreria Xamarin, supportano solo l'autenticazione con un token di firma di accesso condiviso. È possibile creare il token di firma di accesso condiviso usando uno strumento come [Esplora soluzioni](http://storageexplorer.com/) o un'altra applicazione che supporta l'autenticazione con chiave condivisa.

È anche possibile generare un token di firma di accesso condiviso usando Azure PowerShell. L'esempio seguente genera un token di firma di accesso condiviso con autorizzazioni complete per un contenitore BLOB:

1. Installare Azure PowerShell se non è già stato fatto. È consigliabile usare la versione più recente dei cmdlet di PowerShell. Per le istruzioni di installazione, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Aprire Azure PowerShell ed eseguire i comandi seguenti, sostituendo `ACCOUNT_NAME` e `ACCOUNT_KEY==` con le proprie credenziali e `CONTAINER_NAME` con il nome scelto:

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

L'URI della firma di accesso condiviso risultante per il nuovo contenitore sarà simile a:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

La firma di accesso condiviso creata con questo esempio è valida per un giorno. La firma concede l'accesso completo (lettura, scrittura, eliminazione ed elenco) ai BLOB all'interno del contenitore.

Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso in Archiviazione di Azure](../storage-dotnet-shared-access-signature-part-1.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Indirizzamento delle risorse nell'emulatore di archiviazione
Gli endpoint di servizio per l'emulatore di archiviazione sono diversi da quelle di un account di archiviazione di Azure. La differenza è dovuta al fatto che il computer locale non esegue la risoluzione dei nomi di dominio, quindi gli endpoint dell'emulatore di archiviazione devono essere indirizzi locali.

Quando si indirizza una risorsa in un account di archiviazione di Azure, usare lo schema seguente. Il nome dell'account fa parte del nome host dell'URI e la risorsa indirizzata fa parte del percorso URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Ad esempio, l'URI seguente è un indirizzo valido per un BLOB in un account di archiviazione di Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Tuttavia, poiché il computer locale non esegue la risoluzione dei nomi di dominio, nell'emulatore di archiviazione il nome dell'account fa parte del percorso URI invece che del nome host. Per una risorsa in esecuzione nell'emulatore di archiviazione, usare il formato URI seguente:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB nell'emulatore di archiviazione:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Gli endpoint di servizio per l'emulatore di archiviazione sono:

* Servizio BLOB: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Servizio di accodamento: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Servizio tabelle: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Indirizzamento dell'account secondario con RA-GRS
A partire dalla versione 3.1, l'emulatore di archiviazione supporta la replica con ridondanza geografica e accesso in lettura (RA-GRS). Per le risorse di archiviazione nel cloud e nell'emulatore locale, è possibile accedere al percorso secondario aggiungendo "-secondary" al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB mediante il percorso secondario di sola lettura nell'emulatore di archiviazione:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli, vedere [Libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Riferimenti allo strumento da riga di comando emulatore di archiviazione
A partire dalla versione 3.0, quando si avvia l'emulatore di archiviazione viene visualizzata una finestra della console. Usare la riga di comando nella finestra della console per avviare e arrestare l'emulatore, nonché per eseguire query sullo stato e altre operazioni.

> [!NOTE]
> Se è installato l'emulatore di calcolo di Microsoft Azure, viene visualizzata un'icona nella barra delle applicazioni quando si avvia l'emulatore di archiviazione. Fare clic con il pulsante destro del mouse sull'icona per visualizzare un menu che include una modalità grafica per avviare e arrestare l'emulatore di archiviazione.
>
>

### <a name="command-line-syntax"></a>Sintassi della riga di comando
`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opzioni
Per visualizzare l'elenco di opzioni, digitare `/help` al prompt dei comandi.

| Opzione | DESCRIZIONE | Comando | Argomenti |
| --- | --- | --- | --- |
| **Inizia** |Avvia l'emulatore di archiviazione. |`AzureStorageEmulator.exe start [-inprocess]` |*-inprocess*: consente di avviare l'emulatore nel processo corrente anziché creare un nuovo processo. |
| **Stop** |Interrompe l'emulatore di archiviazione. |`AzureStorageEmulator.exe stop` | |
| **Status** |Stampa lo stato dell'emulatore di archiviazione. |`AzureStorageEmulator.exe status` | |
| **Cancella** |Cancella i dati in tutti i servizi specificati nella riga di comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]                                                    ` |*blob*: cancella i dati BLOB. <br/>*queue*: cancella i dati della coda. <br/>*table*: cancella i dati delle tabelle. <br/>*all*: cancella tutti i dati in tutti i servizi. |
| **Init** |Esegue l'inizializzazione una tantum per configurare l'emulatore. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: specifica il server che ospita l'istanza di SQL. <br/>*-sqlinstance instanceName*: specifica il nome dell'istanza di SQL da usare. <br/>*-forcecreate*: forza la creazione del database SQL, anche se già esistente. <br/>*-skipcreate*: ignora la creazione del database SQL. Questa opzione ha la precedenza sull'opzione -forcecreate.<br/>*-reserveports*: tenta di prenotare le porte HTTP associate ai servizi.<br/>*-unreserveports*: tenta di rimuovere le prenotazioni delle porte HTTP associate ai servizi. Questa opzione ha la precedenza sull'opzione -reserveports.<br/>*-inprocess*: esegue l'inizializzazione nel processo corrente anziché generare un nuovo processo. Se si modificano le prenotazioni delle porte, è necessario avviare il processo corrente con autorizzazioni elevate. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Differenze tra l'emulatore di archiviazione e Archiviazione di Azure
Poiché l'emulatore di archiviazione è un ambiente emulato eseguito in un'istanza di SQL locale, esistono differenze a livello di funzionalità tra l'emulatore e un account di archiviazione di Azure nel cloud:

* L'emulatore di archiviazione supporta solo un singolo account fisso e una chiave di autenticazione nota.
* L'emulatore di archiviazione non è un servizio di archiviazione scalabile e non supporta un numero elevato di client simultanei.
* Come descritto in [Indirizzamento delle risorse nell'emulatore di archiviazione](#addressing-resources-in-the-storage-emulator), le risorse sono indirizzate diversamente nell'emulatore di archiviazione rispetto a un account di archiviazione di Azure. La differenza è dovuta al fatto che la risoluzione dei nomi di dominio è disponibile nel cloud, ma non nel computer locale.
* A partire dalla versione 3.1, l'account dell'emulatore di archiviazione supporta RA-GRS (replica con ridondanza geografica in sola lettura). Nell'emulatore, RA-GRS è abilitato per tutti gli account e non esiste mai una discordanza tra le repliche primaria e secondaria. Le operazioni Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta `LastSyncTime` come ora corrente in base al database SQL sottostante.
* Gli endpoint del servizio file e del servizio protocollo SMB non sono attualmente supportati nell'emulatore di archiviazione.
* Se si usa una versione dei servizi di archiviazione che non è ancora supportata dall'emulatore, l'emulatore di archiviazione restituisce l'errore VersionNotSupportedByEmulator (codice di stato HTTP 400: richiesta non valida).

### <a name="differences-for-blob-storage"></a>Differenze per l'archiviazione BLOB
All'archiviazione BLOB nell'emulatore si applicano le seguenti differenze:

* L'emulatore di archiviazione supporta solo dimensioni BLOB fino a 2 GB.
* La copia incrementale consente di copiare gli snapshot dai BLOB sovrascritti, ma viene restituito un errore del servizio.
* Get Page Ranges Diff non funziona con gli snapshot copiati usando il BLOB di copia incrementale.
* Un'operazione Put Blob potrebbe avere esito positivo su un BLOB esistente nell'emulatore di archiviazione con un lease attivo, anche se l'ID del lease non è stato specificato nella richiesta.
* Le operazioni del BLOB di accodamento non sono supportate dall'emulatore. Se si tenta un'operazione su un blob di Accodamento restituisce un errore FeatureNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida).

### <a name="differences-for-table-storage"></a>Differenze per l'archiviazione tabelle
All'archiviazione tabelle nell'emulatore si applicano le seguenti differenze:

* Le proprietà di data nel servizio tabelle nell'emulatore di archiviazione supportano solo l'intervallo supportato da SQL Server 2005 (le date devono essere successive al 1° gennaio 1753). Tutte le date precedenti a 1 gennaio 1753 vengono modificate in questo valore. La precisione delle date è limitata alla precisione di SQL Server 2005, ovvero le date sono precise a 1/300 di secondo.
* L'emulatore di archiviazione supporta valori di proprietà chiave di riga e chiave di partizione con dimensioni minori di 512 byte ognuno. Inoltre, le dimensioni totali del nome dell'account, del nome della tabella e dei nomi delle proprietà chiave non possono superare i 900 byte.
* Le dimensioni totali di una riga in una tabella nell'emulatore di archiviazione sono limitate a meno di 1 MB.
* Nell'emulatore di archiviazione le proprietà con tipo di dati `Edm.Guid` o `Edm.Binary` supportano solo gli operatori di confronto `Equal (eq)` e `NotEqual (ne)` nelle stringhe di filtro delle query.

### <a name="differences-for-queue-storage"></a>Differenze per l'archiviazione di accodamento
Non esistono differenze specifiche per l'archiviazione di accodamento nell'emulatore.

## <a name="storage-emulator-release-notes"></a>Note sulla versione dell'emulatore di archiviazione
### <a name="version-52"></a>Versione 5.2
* L'emulatore di archiviazione supporta ora la versione 2017-04-17 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle.
* È stato risolto un bug per cui i valori delle proprietà di tabella non venivano codificati correttamente.

### <a name="version-51"></a>Versione 5.1
* È stato risolto un bug per cui l'emulatore di archiviazione restituiva l'intestazione `DataServiceVersion` in risposte in cui il servizio non era presente.

### <a name="version-50"></a>Versione 5.0
* Il programma di installazione dell'emulatore di archiviazione non verifica l'eventuale presenza di installazioni MSSQL e .NET Framework esistenti.
* Il programma di installazione dell'emulatore di archiviazione non crea più il database come parte del processo di installazione. Se necessario, il database verrà comunque creato come parte del processo di avvio.
* Per la creazione del database non è più necessaria l'elevazione dei privilegi.
* Per l'avvio non sono più necessarie prenotazioni di porte.
* Aggiunge a `init` le opzioni seguenti: `-reserveports` (è richiesta l'elevazione), `-unreserveports`(è richiesta l'elevazione) e `-skipcreate`.
* L'opzione Storage Emulator UI (Interfaccia utente emulatore di archiviazione) sull'icona nell'area di notifica avvia ora l'interfaccia della riga di comando. L'interfaccia utente grafica precedente non è più disponibile.
* Alcune DLL sono state rimosse o rinominate.

### <a name="version-46"></a>Versione 4.6
* L'emulatore di archiviazione supporta ora la versione 2016-05-31 dei servizi di archiviazione per gli endpoint di servizio BLOB, di accodamento e tabelle.

### <a name="version-45"></a>Versione 4.5
* Ha risolto un bug che causava il blocco dell'inizializzazione e dell'installazione dell'emulatore di archiviazione quando il database di backup veniva rinominato.

### <a name="version-44"></a>Versione 4.4
* L'emulatore di archiviazione supporta ora la versione 2015-12-11 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle.
* L'operazione di Garbage Collection dell'emulatore di archiviazione dei dati BLOB è ora più efficiente quando si lavora con un numero elevato di BLOB.
* Risolto un bug che ha causato la convalida del contenitore ACL XML in modo leggermente diverso dal servizio di archiviazione.
* Risolto un bug che a volte ha causato la segnalazione di valori DateTime max e min nel fuso orario non corretto.

### <a name="version-43"></a>Versione 4.3
* L'emulatore di archiviazione supporta ora la versione 2015-07-08 dei servizi di archiviazione su endpoint di servizio BLOB, di accodamento e tabelle.

### <a name="version-42"></a>Versione 4.2
* L'emulatore di archiviazione supporta ora la versione 2015-04-05 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle.

### <a name="version-41"></a>Versione 4.1
* L'emulatore di archiviazione supporta ora la versione 2015-02-21 dei servizi di archiviazione per gli endpoint di servizio BLOB, di accodamento e tabelle, tranne le nuove funzionalità dei BLOB di aggiunta.
* Se si usa una versione dei servizi di archiviazione che non è ancora supportata d'emulatore, l'emulatore restituisce un messaggio di errore significativo. È consigliabile utilizzare la versione più recente dell'emulatore. Se si verifica un errore VersionNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida), è necessario scaricare la versione più recente dell'emulatore di archiviazione.
* Sistemare un bug in cui un race condition ha fatto sì che i dati di entità della tabella non siano corretti durante le operazioni di fusione simultanee.

### <a name="version-40"></a>Versione 4.0
* L'emulatore di archiviazione eseguibile è stato rinominato in *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versione 3.2
* L'emulatore di archiviazione supporta ora la versione 2014-02-14 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle. Gli endpoint del servizio file non sono attualmente supportati nell'emulatore di archiviazione. Vedere [Controllo delle versioni per i servizi di archiviazione Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) per informazioni dettagliate sulla versione 2014-02-14.

### <a name="version-31"></a>Versione 3.1
* Il servizio di archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è ora supportato nell'emulatore di archiviazione. Le API Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta LastSyncTime come ora corrente in base al database SQL sottostante. Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli, vedere il materiale di riferimento della libreria client di archiviazione di Microsoft Azure per .NET.

### <a name="version-30"></a>Versione 3.0
* L'emulatore di archiviazione di Azure non è più disponibile nello stesso pacchetto dell'emulatore di calcolo.
* L'interfaccia utente grafica dell'emulatore di archiviazione è deprecata a favore di un'interfaccia della riga di comando configurabile tramite script. Per informazioni dettagliate sull'interfaccia della riga di comando, vedere le informazioni di riferimento sullo strumento da riga di comando dell'emulatore di archiviazione. L'interfaccia grafica continuerà a essere presente nella versione 3.0, ma è possibile accedervi solo quando è installato l'emulatore di calcolo facendo clic con il pulsante destro del mouse sull'icona della barra delle applicazioni e selezionando Mostra interfaccia utente emulatore di archiviazione.
* La versione 2013-08-15 dei servizi di archiviazione di Azure è ora completamente supportata. (In precedenza questa versione era supportata solo dalla versione 2.2.1 dell'emulatore di archiviazione di anteprima.)

## <a name="next-steps"></a>Passaggi successivi

* Valutare l'emulatore di archiviazione open source multipiattaforma gestito dalla community [Azurite](https://github.com/arafato/azurite). 
* L'articolo [Esempi di Archiviazione di Azure che usano .NET](../storage-samples-dotnet.md) contiene collegamenti a diversi esempi di codice che è possibile usare durante lo sviluppo dell'applicazione.
* È possibile usare [Microsoft Azure Storage Explorer](http://storageexplorer.com) per usare risorse nell'account di archiviazione cloud e nell'emulatore di archiviazione.
