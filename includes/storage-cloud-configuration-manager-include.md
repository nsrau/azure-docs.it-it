---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038161"
---
La [libreria di Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione. Analizza le impostazioni per le applicazioni client in esecuzione sul desktop, su un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.

Per fare riferimento al pacchetto `CloudConfigurationManager`, aggiungere le seguenti direttive `using`:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Ecco un esempio che illustra come recuperare una stringa di connessione da un file di configurazione:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, ad esempio la [classe ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)del .NET Framework.
