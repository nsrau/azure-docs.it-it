---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164682"
---
La [libreria di Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione indipendentemente dal fatto che l'applicazione client sia in esecuzione sul PC desktop, su un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.

Per fare riferimento al pacchetto CloudConfigurationManager, aggiungere l'istruzione `using` seguente:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Ecco un esempio che illustra come recuperare una stringa di connessione da un file di configurazione:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

L'uso di Gestione configurazione di Azure è facoltativo. È anche possibile usare un'API, ad esempio la classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) di .NET Framework.

