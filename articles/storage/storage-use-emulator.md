<properties 
	pageTitle="Usare l'emulatore di archiviazione di Azure per sviluppo e test | Microsoft Azure" 
	description="L'emulatore di archiviazione di Azure fornisce un ambiente di sviluppo locale gratuito per attività di sviluppo e test per Archiviazione di Azure. Informazioni sull'emulatore di archiviazione, tra cui modalità di autenticazione delle richieste, come connettersi all'emulatore dall'applicazione e come usare lo strumento da riga di comando." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="tamram"/>

# Usare l'emulatore di archiviazione di Azure per sviluppo e test

## Panoramica

L'emulatore di archiviazione di Microsoft Azure offre un ambiente locale che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo. Usando l'emulatore di archiviazione, è possibile testare l'applicazione con i servizi di archiviazione in locale, senza creare una sottoscrizione di Azure o sostenere alcun costo. Quando si è soddisfatti della modalità di funzionamento dell'applicazione nell'emulatore, è possibile passare all'utilizzo di un account di archiviazione di Azure nel cloud.

> [AZURE.NOTE] L'emulatore di archiviazione è disponibile come parte di [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). È inoltre possibile installare l'emulatore di archiviazione usando il [programma di installazione autonomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409). Per configurare l'emulatore di archiviazione, è necessario avere i privilegi di amministratore nel computer.
>  
> Si noti che i dati creati in una versione dell'emulatore di archiviazione non sono necessariamente accessibili quando si utilizza una versione diversa. Se è necessario rendere persistenti i dati a lungo termine, si consiglia di archiviare i dati in un account di archiviazione di Azure anziché nell'emulatore di archiviazione.

## Come funziona l'emulatore di archiviazione
 
L'emulatore di archiviazione usa un'istanza di Microsoft SQL Server e il file system locali per emulare i servizi di archiviazione di Azure. Per impostazione predefinita, l'emulatore di archiviazione usa un database in Microsoft SQL Server 2012 Express LocalDB. È possibile scegliere di configurare l'emulatore di archiviazione per accedere a un'istanza locale di SQL Server anziché l'istanza LocalDB. Per altre informazioni, vedere [Avviare e inizializzare l'emulatore di archiviazione](#start-and-initialize-the-storage-emulator) di seguito.

È possibile installare SQL Server Management Studio Express per gestire l'installazione di LocalDB. L'emulatore di archiviazione si connette a SQL Server o a LocalDB usando l'autenticazione Windows.

Esistono alcune differenze a livello di funzionalità tra l'emulatore di archiviazione e i servizi di archiviazione di Azure. Per altre informazioni su queste differenze, vedere [Differenze tra l'emulatore di archiviazione e Archiviazione di Azure](#differences-between-the-storage-emulator-and-azure-storage).

## Autenticazione delle richieste nell'emulatore di archiviazione

Come per Archiviazione di Azure nel cloud, tutte le richieste effettuate nell'emulatore di archiviazione devono essere autenticate, a meno che non si tratti di richieste anonime. È possibile autenticare le richieste nell'emulatore di archiviazione tramite l'autenticazione con chiave condivisa o con una firma di accesso condiviso.

### Autenticazione con credenziali con chiave condivisa

[AZURE.INCLUDE [risorsa di archiviazione-emulatore-connessione-stringa-includere](../../includes/storage-emulator-connection-string-include.md)]

Per altri dettagli sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](storage-configure-connection-string.md).

### Autenticazione con una firma di accesso condiviso 

Alcune librerie client dell'archiviazione di Azure, ad esempio la libreria Xamarin, supportano solo l'autenticazione con un token di firma di accesso condiviso. Sarà necessario creare il token di firma di accesso condiviso mediante uno strumento o un'applicazione che supporta l'autenticazione con chiave condivisa. Un modo semplice per generare il token di firma di accesso condiviso è tramite Azure PowerShell:

1. Installare Azure PowerShell, se non è stato già installato. È consigliabile usare l'ultima versione dei cmdlet di Azure PowerShell. Per istruzioni per l'installazione, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md#Install).

2. Aprire Azure PowerShell ed eseguire i comandi riportati di seguito. Ricordarsi di sostituire *ACCOUNT\_NAME* e *ACCOUNT\_KEY==* con le credenziali personali. ostituire *CONTAINER\_NAME* con un nome a scelta.

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

L'URI della firma di accesso condiviso risultante per il nuovo contenitore dovrebbe essere simile al seguente:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma di accesso condiviso creata con questo esempio è valida per un giorno. La firma concede l'accesso completo (vale a dire di lettura, scrittura, eliminazione ed elenco) ai BLOB all'interno del contenitore.

Per altre informazioni sulle firme di accesso condiviso, vedere [Firme di accesso condiviso: informazioni sul modello di firma di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md).


## Avviare e inizializzare l'emulatore di archiviazione

Per avviare l'emulatore di archiviazione di Azure, selezionare il pulsante Start o premere il tasto WINDOWS. Iniziare a digitare **emulatore di archiviazione di Azure** e selezionare l'emulatore nell'elenco di applicazioni.

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows.

All'avvio dell'emulatore di archiviazione verrà visualizzata una finestra della riga di comando. È possibile usare questa finestra della riga di comando per avviare e arrestare l'emulatore di archiviazione, cancellare i dati, ottenere lo stato corrente e inizializzare l'emulatore. Per altre informazioni, vedere [Riferimenti allo strumento da riga di comando emulatore di archiviazione](#storage-emulator-command-line-tool-reference).

Quando la finestra della riga di comando viene chiusa, l'emulatore di archiviazione resta in esecuzione. Per visualizzare nuovamente la riga di comando, eseguire la procedura precedente come se si volesse avviare l'emulatore di archiviazione.

La prima volta che si esegue l'emulatore di archiviazione, l'ambiente di archiviazione locale viene inizializzato automaticamente. Il processo di inizializzazione crea un database in LocalDB e riserva le porte HTTP per ogni servizio di archiviazione locale.

L'emulatore di archiviazione viene installato per impostazione predefinita nella directory C:\\Programmi (x86)\\Microsoft SDKs\\Azure\\Storage Emulator.

### Inizializzare l'emulatore di archiviazione per l'uso di un database SQL diverso

È possibile usare lo strumento da riga di comando emulatore di archiviazione per inizializzare l'emulatore di archiviazione in modo che punti a un'istanza di database SQL diversa dall'istanza predefinita LocalDB. Si noti che è necessario eseguire lo strumento da riga di comando con privilegi amministrativi per inizializzare il database back-end per l'emulatore di archiviazione:

1. Fare clic sul pulsante **Start** o premere il tasto **WINDOWS**. Iniziare a digitare `Azure Storage Emulator` e selezionarlo quando compare, per visualizzare lo strumento da riga di comando emulatore di archiviazione.
2. Nella finestra del prompt dei comandi digitare il seguente comando dove `<SQLServerInstance>` è il nome dell'istanza di SQL Server. Per usare LocalDB, specificare `(localdb)\v11.0` come istanza di SQL Server.

		AzureStorageEmulator init /server <SQLServerInstance> 
    
	Per indicare all'emulatore di usare l'istanza di SQL Server predefinita, digitare il comando seguente:

    	AzureStorageEmulator init /server .\\ 

	In alternativa, è possibile usare il comando seguente, che consente di inizializzare nuovamente l'istanza predefinita LocalDB:

    	AzureStorageEmulator init /forceCreate 

Per altre informazioni su questi comandi, vedere [Riferimenti allo strumento da riga di comando emulatore di archiviazione](#storage-emulator-command-line-tool-reference).

## Indirizzamento delle risorse nell'emulatore di archiviazione

Gli endpoint di servizio per l'emulatore di archiviazione sono diversi da quelle di un account di archiviazione di Azure. La differenza è dovuta al fatto che il computer locale non esegue la risoluzione dei nomi di dominio, quindi gli endpoint dell'emulatore di archiviazione richiedono un indirizzo locale anziché un nome di dominio.

Per l'indirizzamento di una risorsa in un account di archiviazione di Azure si usa lo schema seguente, dove il nome dell'account fa parte del nome host dell'URI e la risorsa indirizzata fa parte del percorso URI:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Ad esempio, l'URI seguente è un indirizzo valido per un BLOB in un account di archiviazione di Azure:

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Nell'emulatore di archiviazione, poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI anziché del nome host. Per una risorsa in esecuzione nell'emulatore di archiviazione si usa lo schema seguente:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB nell'emulatore di archiviazione:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Gli endpoint di servizio per l'emulatore di archiviazione sono:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### Indirizzamento dell'account secondario con RA-GRS

A partire dalla versione 3.1, l'account dell'emulatore di archiviazione supporta RA-GRS (replica con ridondanza geografica in sola lettura). Per le risorse di archiviazione nel cloud e nell'emulatore locale, è possibile accedere al percorso secondario aggiungendo "-secondary" al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB mediante il percorso secondario di sola lettura nell'emulatore di archiviazione:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli, vedere [Libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)

## Riferimenti dello strumento da riga di comando emulatore di archiviazione

A partire dalla versione 3.0, quando si avvia l'emulatore di archiviazione viene visualizzata una finestra della riga di comando popup. Usare la finestra della riga di comando per avviare e arrestare l'emulatore, nonché per eseguire query sullo stato e altre operazioni.

> [AZURE.NOTE] Se è installato l'emulatore di calcolo di Microsoft Azure, viene visualizzata un'icona nella barra delle applicazioni quando si avvia l'emulatore di archiviazione. Fare clic con il pulsante destro del mouse sull'icona per visualizzare un menu che fornisce una modalità grafica per avviare e arrestare l'emulatore di archiviazione.

### Sintassi della riga di comando

	AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### Opzioni

Per visualizzare l'elenco di opzioni, digitare `/help` al prompt dei comandi.

| Opzione | Descrizione | Comando | Argomenti |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Inizia** | Avvia l'emulatore di archiviazione. | `AzureStorageEmulator start [-inprocess]` | *-inprocess*: consente di avviare l'emulatore nel processo corrente anziché creare un nuovo processo. |
| **Stop** | Interrompe l'emulatore di archiviazione. | `AzureStorageEmulator stop` | |
| **Status** | Stampa lo stato dell'emulatore di archiviazione. | `AzureStorageEmulator status` | |
| **Cancella** | Cancella i dati in tutti i servizi specificati nella riga di comando. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: cancella i dati BLOB. <br/>*queue*: cancella i dati della coda. <br/>*table*: cancella i dati delle tabelle. <br/>*all*: cancella tutti i dati in tutti i servizi. |
| **Init** | Esegue l'inizializzazione una tantum per configurare l'emulatore. | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server serverName\\instanceName*: specifica il server che ospita l'istanza di SQL. <br/>*-sqlinstance instanceName*: specifica il nome dell'istanza di SQL da usare. <br/>*-forcecreate*: forza la creazione del database SQL, anche se già esistente. <br/>*-inprocess*: esegue l'inizializzazione nel processo corrente anziché generare un nuovo processo. È necessario avviare il processo corrente con autorizzazioni elevate per eseguire l'inizializzazione. |
                                                                                                                  
## Differenze tra l'emulatore di archiviazione e Archiviazione di Azure

Poiché l'emulatore di archiviazione è un ambiente emulato eseguito in un'istanza di SQL locale, esistono alcune differenze a livello di funzionalità tra l'emulatore e un account di archiviazione di Azure nel cloud:

- L'emulatore di archiviazione supporta solo un singolo account fisso e una chiave di autenticazione nota.

- L'emulatore di archiviazione non è un servizio di archiviazione scalabile e non supporta un numero elevato di client simultanei.

- Come descritto in [Indirizzamento delle risorse nell'emulatore di archiviazione](#addressing-resources-in-the-storage-emulator), le risorse sono indirizzate diversamente nell'emulatore di archiviazione rispetto a un account di archiviazione di Azure. La differenza è dovuta al fatto che la risoluzione dei nomi di dominio è disponibile nel cloud, ma non nel computer locale.

- A partire dalla versione 3.1, l'account dell'emulatore di archiviazione supporta RA-GRS (replica con ridondanza geografica in sola lettura). Nell'emulatore, RA-GRS è abilitato per tutti gli account e non esiste mai una discordanza tra le repliche primaria e secondaria. Le operazioni Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta `LastSyncTime` come ora corrente in base al database SQL sottostante.

	Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli, vedere [Libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)

- Gli endpoint del servizio file e del servizio protocollo SMB non sono attualmente supportati nell'emulatore di archiviazione.

- Se si utilizza una versione dei servizi di archiviazione che non è ancora supportata dalla versione dell'emulatore in uso, l'emulatore di archiviazione restituisce un errore di VersionNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida).

### Differenze per l'archiviazione BLOB 

All'archiviazione BLOB nell'emulatore si applicano le seguenti differenze:

- L'emulatore di archiviazione supporta solo dimensioni BLOB fino a 2 GB.

- Un'operazione Put Blob potrebbe avere esito positivo su un BLOB esistente nell'emulatore di archiviazione e che presenta un lease attivo, anche se l'ID del lease non è stato specificato come parte della richiesta.

- Le operazioni del BLOB di accodamento non sono supportate dall'emulatore. Se si tenta un'operazione su un blob di Accodamento restituisce un errore FeatureNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida).

### Differenze per l'archiviazione tabelle 

All'archiviazione tabelle nell'emulatore si applicano le seguenti differenze:

- Le proprietà di data nel servizio tabelle nell'emulatore di archiviazione supportano solo l'intervallo supportato da SQL Server 2005 (*ovvero*, è necessario che le date siano successive al 1 gennaio 1753). Tutte le date precedenti a 1 gennaio 1753 vengono modificate in questo valore. La precisione delle date è limitata alla precisione di SQL Server 2005, ovvero le date sono precise a 1/300 di secondo.

- L'emulatore di archiviazione supporta valori di proprietà chiave di riga e chiave di partizione con dimensioni minori di 512 byte ognuno. Inoltre, le dimensioni totali del nome dell'account, del nome della tabella e dei nomi delle proprietà chiave non possono superare i 900 byte.

- Le dimensioni totali di una riga in una tabella nell'emulatore di archiviazione sono limitate a meno di 1 MB.

- Nell'emulatore di archiviazione le proprietà con tipo di dati `Edm.Guid` o `Edm.Binary` supportano solo gli operatori di confronto `Equal (eq)` e `NotEqual (ne)` nelle stringhe di filtro delle query.

### Differenze per l'archiviazione di accodamento

Non esistono differenze specifiche per l'archiviazione di accodamento nell'emulatore.

## Note sulla versione dell'emulatore di archiviazione

### Versione 4.2

- L'emulatore di archiviazione supporta ora la versione 2015-04-05 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle.

### Versione 4.1

- L'emulatore di archiviazione supporta ora la versione 2015-02-21 di servizi di archiviazione su BLOB, code ed endpoints di servizio tabelle, fatta eccezione per le nuove funzionalità di BLOB di Accodamento . 

- L'emulatore di archiviazione restituisce ora un messaggio di errore significativo se si utilizza una versione dei servizi di archiviazione che non è ancora supportata da tale versione dell'emulatore. È consigliabile utilizzare la versione più recente dell'emulatore. Se si verifica un errore VersionNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida), è necessario scaricare la versione più recente dell'emulatore di archiviazione.

- Sistemare un bug in cui un race condition ha fatto sì che i dati di entità della tabella non siano corretti durante le operazioni di fusione simultanee.

### Versione 4.0

- L'emulatore di archiviazione eseguibile è stato rinominato in *AzureStorageEmulator.exe*.

### Versione 3.2
- L'emulatore di archiviazione supporta ora la versione 2014-02-14 dei servizi di archiviazione per gli endpoint dei servizi BLOB, di accodamento e tabelle. Si noti che gli endpoint del servizio file non sono attualmente supportati nell'emulatore di archiviazione. Vedere [Controllo delle versioni per i servizi di archiviazione Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx) per informazioni dettagliate sulla versione 2014-02-14.

### Versione 3.1
- Il servizio di archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è ora supportato nell'emulatore di archiviazione. Le API Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta LastSyncTime come ora corrente in base al database SQL sottostante. Per l'accesso a livello di codice al percorso secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3.2 o successiva. Per i dettagli, vedere il materiale di riferimento della libreria client di archiviazione di Microsoft Azure per .NET.

### Versione 3.0
- L'emulatore di archiviazione di Azure non è più disponibile nello stesso pacchetto dell'emulatore di calcolo.

- L'interfaccia utente grafica dell'emulatore di archiviazione è deprecata a favore di un'interfaccia della riga di comando configurabile tramite script. Per informazioni dettagliate sull'interfaccia della riga di comando, vedere Riferimenti allo strumento da riga di comando emulatore di archiviazione. L'interfaccia grafica continuerà a essere presente nella versione 3.0, ma è possibile accedervi solo quando è installato l'emulatore di calcolo facendo clic con il pulsante destro del mouse sull'icona della barra delle applicazioni e selezionando Mostra interfaccia utente emulatore di archiviazione.

- La versione 2013-08-15 dei servizi di archiviazione di Azure è ora completamente supportata. (In precedenza questa versione era supportata solo dalla versione 2.2.1 dell'emulatore di archiviazione di anteprima.)

<!---HONumber=AcomDC_0406_2016-->