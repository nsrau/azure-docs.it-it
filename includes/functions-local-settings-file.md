---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: e319356d555f26354ea29dc7be068bf6168abb17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455156"
---
## <a name="local-settings-file"></a>File di impostazioni locali

Il file local archivia le impostazioni dell'app, le stringhe di connessione e le impostazioni utilizzate dagli strumenti di sviluppo locale. Impostazioni nel file local vengono usate solo durante l'esecuzione locale. Il file di impostazioni locali presenta la struttura seguente:

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

Quando si esegue localmente, sono supportate le seguenti impostazioni:

| Impostazione      | Descrizione                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Se impostato su `true`, tutti i valori sono crittografati usando una chiave del computer locale. Usato con i comandi `func settings`. Il valore predefinito è `false`. |
| **`Values`** | Matrice di stringhe di connessione utilizzate durante l'esecuzione locale e le impostazioni dell'applicazione. Queste coppie chiave-valore (stringa-stringa) corrispondono alle impostazioni dell'applicazione nell'app per le funzioni in Azure, ad esempio [ `AzureWebJobsStorage` ]. Molti trigger e associazioni presentano una proprietà che fa riferimento a un'impostazione app stringa di connessione, ad esempio `Connection` per il [trigger di archiviazione Blob](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration). Per tali proprietà, è necessario un'impostazione dell'applicazione definita nel `Values` matrice. <br/>[`AzureWebJobsStorage`] è l'impostazione app obbligatoria per i trigger diversi da HTTP. <br/>Versione 2.x del runtime di funzioni richiede il [`FUNCTIONS_WORKER_RUNTIME`] impostazione, che viene generato per il progetto da strumenti di base. <br/> Quando si dispone di [emulatore di archiviazione di Azure](../articles/storage/common/storage-use-emulator.md) installato in locale, è possibile impostare [ `AzureWebJobsStorage` ] a `UseDevelopmentStorage=true` e strumenti di base usa l'emulatore. Ciò è utile durante lo sviluppo, ma è consigliabile testare con una connessione di archiviazione reale prima della distribuzione.<br/> Valori devono essere stringhe e non JSON oggetti o matrici. I nomi delle impostazioni non può includere i due punti (`:`) o un carattere di sottolineatura doppio (`__`); sono riservate dal runtime.  |
| **`Host`** | Le impostazioni in questa sezione consentono di personalizzare il processo host di Funzioni durante l'esecuzione in locale. Questi sono separati dalle impostazioni dell'host. JSON, che si applicano anche durante l'esecuzione in Azure. |
| **`LocalHttpPort`** | Consente di impostare la porta predefinita usata durante l'esecuzione nell'host locale di Funzioni, ovvero `func host start` e `func run`. L'opzione `--port` della riga di comando ha la precedenza su questo valore. |
| **`CORS`** | Definisce le origini consentite per la [condivisione di risorse tra le origini (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Le origini sono elencate in un elenco delimitato dalla virgola senza spazi. È supportato il valore del carattere jolly (\*) che consente le richieste di qualsiasi origine. |
| **`CORSCredentials`** |  Impostarla su true per consentire `withCredentials` richieste. |
| **`ConnectionStrings`** | Non usare questa raccolta per le stringhe di connessione usate per l'associazione di funzione. Questa raccolta viene usata solo da parte di Framework che in genere ottengono le stringhe di connessione dal `ConnectionStrings` sezione di configurazione di una file, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Le stringhe di connessione in questo oggetto vengono aggiunte all'ambiente con il tipo di provider di [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Gli elementi in questa raccolta non vengono pubblicati in Azure con altre impostazioni di app. È necessario aggiungere in modo esplicito questi valori per il `Connection strings` raccolta di impostazioni dell'app di funzione. Se si sta creando un [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice della funzione, è consigliabile archiviare il valore di stringa di connessione nel **le impostazioni dell'applicazione** nel portale con le altre connessioni. |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
