---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: c505909599b6b69719de1cb9224db52d2f524b2b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935888"
---
## <a name="local-settings-file"></a>File di impostazioni locali

Il file local. Settings. JSON archivia le impostazioni dell'app, le stringhe di connessione e le impostazioni usate dagli strumenti di sviluppo locali. Le impostazioni nel file local. Settings. JSON vengono usate solo quando i progetti sono in esecuzione in locale. La struttura del file di impostazioni locale è la seguente:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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

| Impostazione      | Description                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando questa impostazione è impostata su `true`, tutti i valori vengono crittografati con una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è `false`. |
| **`Values`** | Matrice delle impostazioni dell'applicazione e delle stringhe di connessione utilizzate quando un progetto viene eseguito localmente. Queste coppie chiave-valore (stringa-stringa) corrispondono alle impostazioni dell'applicazione nell'app per le funzioni in Azure, ad esempio [`AzureWebJobsStorage`]. Molti trigger e associazioni hanno una proprietà che fa riferimento a un'impostazione dell'app per la stringa di connessione, ad esempio `Connection` per il [trigger di archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Per queste proprietà, è necessaria un'impostazione dell'applicazione definita nella matrice `Values`. <br/>[`AzureWebJobsStorage`] è un'impostazione di app obbligatoria per trigger diversi da http. <br/>Per la versione 2. x e versioni successive del runtime di funzioni è necessaria l'impostazione [`FUNCTIONS_WORKER_RUNTIME`], generata per il progetto dagli strumenti di base. <br/> Quando l' [emulatore di archiviazione di Azure](../articles/storage/common/storage-use-emulator.md) è installato localmente e si imposta [`AzureWebJobsStorage`] su `UseDevelopmentStorage=true`, core tools usa l'emulatore. L'emulatore è utile durante lo sviluppo, ma è consigliabile eseguire il test con una connessione di archiviazione effettiva prima della distribuzione.<br/> I valori devono essere stringhe e non oggetti o matrici JSON. I nomi delle impostazioni non possono includere i due punti (`:`) o una doppia sottolineatura (`__`). Questi caratteri sono riservati dal runtime.  |
| **`Host`** | Le impostazioni in questa sezione consentono di personalizzare il processo host di funzioni quando si eseguono progetti localmente. Queste impostazioni sono separate dalle impostazioni host. JSON, che si applicano anche quando si eseguono progetti in Azure. |
| **`LocalHttpPort`** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione della riga di comando `--port` avrà la precedenza su questa impostazione. |
| **`CORS`** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **`CORSCredentials`** |  Quando è impostato su `true`, consente `withCredentials` richieste. |
| **`ConnectionStrings`** | Raccolta. Non usare questa raccolta per le stringhe di connessione usate dalle associazioni di funzioni. Questa raccolta viene usata solo dai Framework che in genere ottengono stringhe di connessione dalla sezione `ConnectionStrings` di un file di configurazione, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Gli elementi in questa raccolta non vengono pubblicati in Azure con altre impostazioni dell'app. È necessario aggiungere in modo esplicito questi valori alla raccolta `Connection strings` delle impostazioni dell'app per le funzioni. Se si sta creando una [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice della funzione, è necessario archiviare il valore della stringa di connessione con le altre connessioni nelle **impostazioni dell'applicazione** nel portale. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
