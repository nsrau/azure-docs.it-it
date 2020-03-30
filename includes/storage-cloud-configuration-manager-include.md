---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038161"
---
La [libreria di Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) fornisce una classe per l'analisi della stringa di connessione da un file di configurazione. La classe [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analizza le impostazioni di configurazione. Analizza le impostazioni per le applicazioni client eseguite sul desktop, in un dispositivo mobile, in una macchina virtuale di Azure o in un servizio cloud di Azure.It parses settings for client applications that run on the desktop, on a mobile device, in an Azure virtual machine, or in an Azure cloud service.

Per fare `CloudConfigurationManager` riferimento al `using` pacchetto, aggiungere le direttive seguenti:To reference the package, add the following directives:

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

L'uso di Gestione configurazione di Azure è facoltativo. È inoltre possibile utilizzare un'API, ad esempio la [classe ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)di .NET Framework.
