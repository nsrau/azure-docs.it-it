---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 11626dd95064cf972a845e5c37f930b9e49c57e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077056"
---
La [libreria di Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione indipendentemente dal fatto che l'applicazione client sia in esecuzione sul PC desktop, su un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.

Per fare riferimento al pacchetto CloudConfigurationManager, aggiungere l'istruzione `using` seguente:

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

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, ad esempio la classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) di .NET Framework.
