---
title: Risolvere i problemi relativi all'uso dell'emulatore di Azure Cosmos
description: Informazioni su come risoluzione dei problemi i problemi di servizio non disponibili, certificati, crittografia e controllo delle versioni quando si usa l'emulatore di Azure Cosmos.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.openlocfilehash: 49c03b506441c93be57b24505b09524d72c2c8fb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996986"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-emulator"></a>Risolvere i problemi relativi all'uso dell'emulatore di Azure Cosmos

Azure Cosmos Emulator fornisce un ambiente locale che emula il servizio Azure Cosmos DB a scopo di sviluppo. Usare i suggerimenti di questo articolo per risolvere i problemi riscontrati durante l'installazione o l'uso dell'emulatore di Azure Cosmos. 

Se è stata installata una nuova versione dell'emulatore e si verificano errori, provare a ripristinare i dati. È possibile reimpostare i dati facendo clic con il pulsante destro del mouse sull'icona dell'emulatore di Azure Cosmos sulla barra delle applicazioni e quindi scegliendo Reimposta dati.... Se gli errori non vengono risolti, è possibile disinstallare l'emulatore e tutte le versioni precedenti dell'emulatore, se disponibili, rimuovere *C:\Program files\Azure Cosmos DB emulatore* directory e reinstallare l'emulatore. Per istruzioni, vedere [Disinstallare l'emulatore locale](local-emulator.md#uninstall). In alternativa, se la reimpostazione dei dati non funziona, passare a `%LOCALAPPDATA%\CosmosDBEmulator` percorso ed eliminare la cartella.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Risolvere i problemi relativi ai contatori delle prestazioni di Windows danneggiati

* Se l'emulatore di Azure Cosmos si arresta in modo anomalo, raccogliere i file di dump dalla `%LOCALAPPDATA%\CrashDumps` cartella, comprimerli e aprire un ticket di supporto dal [portale di Azure](https://portal.azure.com).

* Se si verificano arresti anomali in `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, potrebbe trattarsi di un sintomo della presenza di contatori delle prestazioni danneggiati. L'esecuzione del comando seguente dal prompt dei comandi come amministratore in genere consente di risolvere il problema:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Risolvere i problemi di connettività

* In caso di problemi di connettività, [raccogliere i file di traccia](#trace-files), comprimerli e aprire un ticket di supporto nel [portale di Azure](https://portal.azure.com).

* Se si riceve un messaggio di tipo **Servizio non disponibile**, è possibile che l'emulatore non riesca a inizializzare lo stack di rete. Controllare per verificare se è stato installato il client sicuro Pulse o il client di rete Juniper, perché è possibile che i rispettivi driver del filtro di rete provochino il problema. La disinstallazione dei driver filtro di rete di terze parti consente in genere di risolvere il problema. In alternativa, avviare l'emulatore con /DisableRIO. In questo modo, per la comunicazione di rete dell'emulatore, si passerà ai Winsock normali. 

* Se si riscontrano **"Forbidden", "message": "la richiesta viene effettuata con una crittografia non consentita nel protocollo di transito o nella crittografia. Controllare l'impostazione minima consentita del protocollo SSL/TLS... "** problemi di connettività. questa situazione potrebbe essere causata da modifiche globali nel sistema operativo (ad esempio, Insider Preview Build 20170) o dalle impostazioni del browser che abilitano TLS 1,3 come valore predefinito. Potrebbe verificarsi un errore simile quando si usa l'SDK per eseguire una richiesta nell'emulatore Cosmos, ad esempio **Microsoft.Azure.Documents.DocumentClientException: la richiesta viene effettuata con una crittografia non consentita nel protocollo di transito o nella crittografia. Controllare l'impostazione del protocollo minimo consentito SSL/TLS**. Questo problema è previsto in questo momento perché l'emulatore di Cosmos accetta e funziona solo con il protocollo TLS 1.2. La soluzione consigliata consiste nel modificare le impostazioni e il valore predefinito in TLS 1,2; ad esempio, in Gestione IIS passare a "siti"-> "siti Web predefiniti" e individuare i "binding del sito" per la porta 8081 e modificarli per disabilitare TLS 1,3. È possibile eseguire un'operazione simile per il Web browser tramite le opzioni di "Impostazioni".

* Mentre l'emulatore è in esecuzione, se il computer passa in modalità sospensione o esegue eventuali aggiornamenti del sistema operativo, potrebbe essere visualizzato il messaggio **Il servizio non è attualmente disponibile**. Reimpostare i dati dell'emulatore facendo clic con il pulsante destro del mouse sull'icona visualizzata nell'area di notifica di Windows e scegliendo **Reset Data** (Reimposta dati).

## <a name="collect-trace-files"></a><a id="trace-files"></a>Raccogliere i file di traccia

Per raccogliere le tracce di debug, eseguire i comandi seguenti da un prompt dei comandi amministrativi:

1. Passare al percorso in cui è installato l'emulatore:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Arrestare l'emulatore e controllare la barra di sistema per assicurarsi che il programma sia stato arrestato. Il completamento dell'operazione potrebbe richiedere alcuni minuti. È anche possibile selezionare **Esci** nell'interfaccia utente dell'emulatore di Azure Cosmos.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Avviare la registrazione con il comando seguente:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Avviare l'emulatore

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Riproduci il problema. Se Esplora dati non funziona, è sufficiente attendere che il browser si apra per alcuni secondi per rilevare l'errore.

1. Arrestare la registrazione con il comando seguente:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Passare a `%ProgramFiles%\Azure Cosmos DB Emulator` percorso e trovare il file *docdbemulator_000001. etl* .

1. Aprire un ticket di supporto nel [portale di Azure](https://portal.azure.com) e includere il file ETL insieme ai passaggi di ripetizione.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come eseguire il debug dei problemi con l'emulatore locale. È ora possibile passare agli articoli successivi:

* [Esportare i certificati dell'emulatore di Azure Cosmos per l'uso con app Java, Python e Node.js](local-emulator-export-ssl-certificates.md)
* [Usare i parametri della riga di comando e i comandi di PowerShell per controllare l'emulatore](emulator-command-line-parameters.md)
