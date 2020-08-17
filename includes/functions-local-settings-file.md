---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 6fd8c3c5839d4cc897caa2dff70af87980e547eb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206754"
---
## <a name="local-settings-file"></a>File di impostazioni locali

Nel file local.settings.json sono archiviate le impostazioni dell'app, le stringhe di connessione e le impostazioni usate dagli strumenti di sviluppo locali. Le impostazioni nel file local.settings.json vengono usate solo quando i progetti sono in esecuzione in locale. La struttura del file di impostazioni locali è la seguente:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Queste impostazioni sono supportate quando si eseguono progetti in locale:

| Impostazione      | Descrizione                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando questa impostazione è impostata su `true`, tutti i valori vengono crittografati con una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è `false`. |
| **`Values`** | Matrice di impostazioni dell'applicazione e stringhe di connessioni usate durante l'esecuzione di un progetto in locale. Queste coppie chiave-valore (stringa-stringa) corrispondono alle impostazioni dell'applicazione nell'app per le funzioni in Azure, ad esempio [`AzureWebJobsStorage`]. Molti trigger e associazioni includono una proprietà che fa riferimento a un'impostazione di app per la stringa di connessione, ad esempio `Connection` per il [trigger di archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Per queste proprietà è necessaria un'impostazione dell'applicazione definita nella matrice `Values`. <br/>[`AzureWebJobsStorage`] è un'impostazione di app obbligatoria per i trigger diversi da HTTP. <br/>Per la versione 2.x e le versioni successive del runtime di Funzioni è necessaria l'impostazione [`FUNCTIONS_WORKER_RUNTIME`], che viene generata per il progetto dagli strumenti di base. <br/> Quando l'[emulatore di archiviazione di Azure](../articles/storage/common/storage-use-emulator.md) è installato in locale e si imposta [`AzureWebJobsStorage`] su `UseDevelopmentStorage=true`, gli strumenti di base usano l'emulatore. L'emulatore è utile durante lo sviluppo, ma è consigliabile eseguire test con una connessione di archiviazione effettiva prima della distribuzione.<br/> I valori devono essere stringhe e non oggetti o matrici JSON. I nomi delle impostazioni non possono includere i due punti (`:`) o una doppia sottolineatura (`__`). Questi caratteri sono riservati al runtime. <br/>Per disabilitare una funzione durante l'esecuzione in locale, aggiungere `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` alla raccolta, dove `<FUNCTION_NAME>` è il nome della funzione. Per altre informazioni, vedere [Come disabilitare le funzioni in Funzioni di Azure](../articles/azure-functions/disable-function.md#localsettingsjson)  |
| **`Host`** | Le impostazioni in questa sezione consentono di personalizzare il processo host di Funzioni durante l'esecuzione dei progetti in locale. Queste impostazioni sono distinte da quelle del file host.json, che vengono applicate anche quando si eseguono progetti in Azure. |
| **`LocalHttpPort`** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questa impostazione. |
| **`CORS`** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **`CORSCredentials`** |  Quando è impostata su `true`, consente le richieste `withCredentials`. |
| **`ConnectionStrings`** | Raccolta. Non usare questa raccolta per le stringhe di connessione usate per le associazioni di funzione. Questa raccolta viene usata solo dai framework che in genere ottengono le stringhe di connessione dalla sezione `ConnectionStrings` di un file di configurazione, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Gli elementi in questa raccolta non vengono pubblicati in Azure con altre impostazioni di app. È necessario aggiungere questi valori in modo esplicito alla raccolta `Connection strings` delle impostazioni dell'app per le funzioni. Se si crea una connessione [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice di funzione, è consigliabile archiviare il valore della stringa di connessione insieme alle altre connessioni in **Impostazione applicazione** nel portale. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
