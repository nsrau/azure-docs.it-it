---
title: Riga di comando e informazioni di riferimento su PowerShell per Azure Cosmos DB emulatore
description: Informazioni sui parametri della riga di comando per Azure Cosmos DB emulatore, su come controllare l'emulatore con PowerShell e su come modificare il numero di contenitori che è possibile creare nell'emulatore.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.openlocfilehash: 70e68c566ccb0fe08ce3b8d2dc11fc3c141c16bc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997802"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Riga di comando e informazioni di riferimento su PowerShell per Azure Cosmos DB emulatore

Azure Cosmos Emulator fornisce un ambiente locale che emula il servizio Azure Cosmos DB per scopi di sviluppo locale. Dopo aver [installato l'emulatore](local-emulator.md), è possibile controllare l'emulatore con la riga di comando e i comandi di PowerShell. Questo articolo descrive come usare i comandi della riga di comando e di PowerShell per avviare e arrestare l'emulatore, configurare le opzioni ed eseguire altre operazioni. È necessario eseguire i comandi dal percorso di installazione.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Gestire l'emulatore con la sintassi della riga di comando

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Per visualizzare l'elenco di opzioni, digitare `Microsoft.Azure.Cosmos.Emulator.exe /?` al prompt dei comandi.

|**Opzione** | **Descrizione** | **Comando**| **Argomenti**|
|---|---|---|---|
|[Nessun argomento] | Avvia l'emulatore di Azure Cosmos con le impostazioni predefinite. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Visualizza l'elenco di argomenti della riga di comando supportati.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Ottiene lo stato dell'emulatore Azure Cosmos. Lo stato è indicato dal codice di uscita: 1 = Avvio, 2 = In esecuzione, 3 = Arrestato. Un codice di uscita negativo indica che si è verificato un errore. Non viene prodotto altro output. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Arresta l'emulatore Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Specifica il percorso in cui archiviare i file di dati. Il valore predefinito è %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Un percorso accessibile |
|Porta | Specifica il numero di porta da usare per l'emulatore. Il valore predefinito è 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>: numero di porta singolo |
| ComputePort | Specifica il numero di porta da usare per il servizio Gateway di interoperabilità di calcolo. La porta probe dell'endpoint HTTP del gateway viene calcolata come ComputePort + 79. ComputePort e ComputePort + 79 devono pertanto essere aperte e disponibili. Il valore predefinito è 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: numero di porta singolo |
| EnableMongoDbEndpoint=3.2 | Abilita l'API MongoDB 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Abilita l'API MongoDB 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Specifica il numero di porta da usare per l'API di compatibilità MongoDB. Il valore predefinito è 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>: numero di porta singolo|
| EnableCassandraEndpoint | Abilita l'API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Specifica il numero di porta da usare per l'endpoint Cassandra. Il valore predefinito è 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: numero di porta singolo |
| EnableGremlinEndpoint | Abilita l'API Gremlin | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Numero di porta da usare per l'endpoint Gremlin. Il valore predefinito è 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>: numero di porta singolo |
|EnableTableEndpoint | Abilita l'API Tabella di Azure | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Numero di porta da usare per l'endpoint Tabella di Azure. Il valore predefinito è 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>: numero di porta singolo|
| KeyFile | Legge la chiave di autorizzazione dal file specificato. Usare l'opzione /GenKeyFile per generare un file di chiave | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: percorso del file |
| ResetDataPath | Rimuove in modo ricorsivo tutti i file nel percorso specificato. Se non si specifica un percorso, per impostazione predefinita viene usato %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>: Percorso del file  |
| StartTraces  |  Avvia la raccolta dei log di traccia di debug tramite LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Arresta la raccolta dei log di traccia di debug tramite LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Avvia la raccolta dei log di traccia di debug tramite lo strumento di registrazione delle prestazioni di Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Arresta la raccolta dei log di traccia di debug tramite lo strumento di registrazione delle prestazioni di Windows. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Per impostazione predefinita, l'emulatore rigenera il certificato TLS/SSL autofirmato, se la SAN del certificato non include il nome di dominio dell'host dell'emulatore, l'indirizzo IPv4 locale,' localhost ' è 127.0.0.1'. Con questa opzione, l'emulatore invece avrà problemi all'avvio. Sarà quindi necessario usare l'opzione /GenCert per creare e installare un nuovo certificato TLS/SSL autofirmato. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Genera e installa un nuovo certificato TLS/SSL autofirmato, includere facoltativamente un elenco delimitato da virgole di nomi DNS aggiuntivi per accedere all'emulatore tramite la rete. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>: elenco facoltativo di nomi DNS aggiuntivi delimitati da virgole  |
| DirectPorts |Specifica le porte da usare per la connettività diretta. I valori predefiniti sono 10251, 10252, 10253 e 10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: elenco delimitato da virgole di 4 porte |
| Chiave |Chiave di autorizzazione per l'emulatore. La chiave deve essere la codifica Base 64 di un vettore a 64 byte. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>: La chiave deve essere la codifica Base 64 di un vettore a 64 byte.|
| EnableRateLimiting | Specifica che il comportamento di limitazione della frequenza è abilitato. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Specifica che il comportamento di limitazione della frequenza è disabilitato. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | Non mostra l'interfaccia utente dell'emulatore. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Non mostra Esplora dati all'avvio. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Specifica il numero massimo di contenitori partizionati. Per altre informazioni, vedere [Modificare il numero di contenitori](#set-partitioncount). | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: numero massimo di contenitori con partizione singola consentiti. Il valore predefinito è 25. Il valore massimo consentito è 250.|
| DefaultPartitionCount| Specifica il numero predefinito di partizioni per un contenitore partizionato. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> Il valore predefinito è 25.|
| AllowNetworkAccess | Consente l'accesso all'emulatore tramite una rete. Per abilitare l'accesso di rete, è necessario passare anche /Key=\<key_string\> o /KeyFile=\<file_name\>. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> or  Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Non modifica le regole del firewall quando viene usata l'opzione /AllowNetworkAccess. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Genera una nuova chiave di autorizzazione e la salva nel file specificato. La chiave generata può essere usata con le opzioni /Key o /KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| Consistenza | Imposta il livello di coerenza per l'account. | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: Il valore deve essere uno dei [livelli di coerenza](consistency-levels.md) seguenti: Di sessione, Assoluta, Finale, o BoundedStaleness. Il valore predefinito è Session. |
| ? | Mostra il messaggio della Guida.| | |

## <a name="manage-the-emulator-with-powershell"></a>Gestire l'emulatore con PowerShell

L'emulatore include un modulo di PowerShell per avviare, arrestare, disinstallare e recuperare lo stato del servizio. Eseguire il cmdlet seguente per usare il modulo di PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

o collocare la directory `PSModules` in `PSModulesPath` e importare il modulo come illustrato nel comando seguente:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Ecco un riepilogo dei comandi per il controllo dell'emulatore da PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintassi**

`Get-CosmosDbEmulatorStatus`

**Osservazioni:**

Restituisce uno di questi valori per ServiceControllerStatus: ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running o ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Sintassi**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Osservazioni:**

Avvia l'emulatore. Per impostazione predefinita, il comando attende fino a quando l'emulatore è pronto per accettare richieste. Usare l'opzione -NoWait se si vuole che il cmdlet restituisca valori subito dopo avere avviato l'emulatore.

### `Stop-CosmosDbEmulator`

**Sintassi**

 `Stop-CosmosDbEmulator [-NoWait]`

**Osservazioni:**

Attesta l'emulatore. Per impostazione predefinita, questo comando attende fino all'arresto completo dell'emulatore. Usare l'opzione -NoWait se si vuole che il cmdlet restituisca valori subito dopo l'inizio dell'arresto dell'emulatore.

### `Uninstall-CosmosDbEmulator`

**Sintassi**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Osservazioni:**

Disinstalla l'emulatore e facoltativamente rimuove i contenuti completi di $env:LOCALAPPDATA\CosmosDbEmulator.
Il cmdlet verifica che l'emulatore sia arrestato prima di disinstallarlo.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Modificare il numero di contenitori predefiniti

Per impostazione predefinita, è possibile creare fino a 25 contenitori di dimensione fissa (supportati solo con Azure Cosmos DB SDK) o 5 contenitori senza limiti con l'emulatore Azure Cosmos. Modificando il valore **PartitionCount**, è possibile creare fino a 250 contenitori di dimensione fissa o 50 contenitori senza limiti oppure qualsiasi combinazione di elementi di questi due tipi, purché non si superino 250 contenitori di dimensione fissa (dove un contenitore senza limiti = 5 contenitori di dimensione fissa). Non è tuttavia consigliabile configurare l'emulatore per l'esecuzione con più di 200 contenitori di dimensione fissa. Ciò comporta infatti un sovraccarico delle operazioni di I/O su disco e può quindi causare timeout imprevedibili quando si usano le API per endpoint.

Se si tenta di creare un contenitore dopo il superamento del conteggio corrente delle partizioni, l'emulatore genera un'eccezione ServiceUnavailable con il messaggio seguente.

> Attualmente si sta riscontrando una richiesta elevata in questa area e non è possibile soddisfare la richiesta in questo momento. We work continuously to bring more and more capacity online, and encourage you to try again.
> ID attività: 12345678-1234-1234-1234-123456789ABC

Per modificare il numero di contenitori disponibili nell'emulatore Azure Cosmos, procedere come segue:

1. Eliminare tutti i dati locali dell'emulatore di Azure Cosmos facendo clic con il pulsante destro del mouse sull'icona dell' **emulatore Azure Cosmos DB** nella barra delle applicazioni e quindi scegliendo **Reimposta dati...**.

1. Eliminare tutti i dati dell'emulatore in questa cartella: `%LOCALAPPDATA%\CosmosDBEmulator`.

1. Chiudere tutte le istanze aperte facendo clic con il pulsante destro del mouse sull'icona dell'**emulatore di Azure Cosmos DB** sull'area di notifica, quindi fare clic su **Esci**. La chiusura di tutte le istanze può richiedere qualche minuto.

1. Installare la versione più recente dell'[emulatore Azure Cosmos](https://aka.ms/cosmosdb-emulator).

1. Avviare l'emulatore con il flag PartitionCount impostando un valore <= 250. Ad esempio: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Passaggi successivi

* [Esportare i certificati dell'emulatore di Azure Cosmos per l'uso con app Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Problemi di debug con l'emulatore](troubleshoot-local-emulator.md)
