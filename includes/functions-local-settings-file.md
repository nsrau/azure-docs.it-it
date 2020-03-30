---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205740"
---
## <a name="local-settings-file"></a>File di impostazioni locali

Il file local.settings.json archivia le impostazioni dell'app, le stringhe di connessione e le impostazioni usate dagli strumenti di sviluppo locale. Le impostazioni nel file local.settings.json vengono utilizzate solo quando si eseguono progetti in locale. Il file di impostazioni locali ha questa struttura:

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

Queste impostazioni sono supportate quando si eseguono progetti in locale:These settings are supported when you run projects locally:

| Impostazione      | Descrizione                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando questa impostazione `true`è impostata su , tutti i valori vengono crittografati con una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è `false`. |
| **`Values`** | Matrice di impostazioni dell'applicazione e stringhe di connessione utilizzate quando un progetto viene eseguito in locale. Queste coppie chiave-valore (stringa-stringa) corrispondono alle impostazioni dell'applicazione nell'app per le funzioni in Azure, ad esempio [`AzureWebJobsStorage`]. Molti trigger e associazioni hanno una proprietà che fa riferimento `Connection` a un'impostazione dell'app della stringa di connessione, ad esempio per il trigger di [archiviazione BLOB](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Per queste proprietà, è necessaria un'impostazione dell'applicazione definita nella `Values` matrice. <br/>[`AzureWebJobsStorage`]è un'impostazione obbligatoria dell'app per i trigger diversi da HTTP. <br/>La versione 2.x e successive del`FUNCTIONS_WORKER_RUNTIME`runtime di Funzioni richiede l'impostazione [ ] , che viene generata per il progetto dagli strumenti di base. <br/> Quando l'emulatore di [archiviazione](../articles/storage/common/storage-use-emulator.md) di [`AzureWebJobsStorage`] `UseDevelopmentStorage=true`Azure è installato in locale e si imposta su , Core Tools usa l'emulatore. L'emulatore è utile durante lo sviluppo, ma è consigliabile eseguire il test con una connessione di archiviazione effettiva prima della distribuzione.<br/> I valori devono essere stringhe e non da oggetti o matrici JSON. L'impostazione dei nomi`:`non può includere`__`i due punti ( ) o una doppia sottolineatura ( ). Questi caratteri sono riservati dal runtime.  |
| **`Host`** | Le impostazioni in questa sezione consentono di personalizzare il processo host Funzioni quando si eseguono progetti in locale. Queste impostazioni sono separate dalle impostazioni host.json, che si applicano anche quando si eseguono progetti in Azure.These settings are separate from the host.json settings, which also apply when you run projects in Azure. |
| **`LocalHttpPort`** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questa impostazione. |
| **`CORS`** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **`CORSCredentials`** |  Se impostato `true`su `withCredentials` , consente le richieste. |
| **`ConnectionStrings`** | Raccolta. Non usare questa raccolta per le stringhe di connessione usate dalle associazioni di funzioni. Questa raccolta viene utilizzata solo dai framework che `ConnectionStrings` in genere ottengono stringhe di connessione dalla sezione di un file di configurazione, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Gli elementi di questa raccolta non vengono pubblicati in Azure con altre impostazioni dell'app. È necessario aggiungere in `Connection strings` modo esplicito questi valori alla raccolta delle impostazioni dell'app per le funzioni. Se si sta [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) creando un nel codice della funzione, è necessario archiviare il valore della stringa di connessione con le altre connessioni in **Impostazioni applicazione** nel portale. |

['AzureWebJobsStorage'''AzureWebJobsStorage']: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
