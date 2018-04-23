---
title: Cosa è successo a un progetto ASP.NET 5? (Servizi connessi di Visual Studio)| Documentazione Microsoft
description: 'Viene descritto cosa succede dopo la connessione a un account di archiviazione di Azure in un progetto di Visual Studio ASP.NET 5 utilizzando i servizi connessi a Visual Studio '
services: storage
author: ghogen
manager: douge
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: f99ba4b6c954ae9faa87b9604c06e94c56e4f631
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Cosa è successo a un progetto ASP.NET 5 (servizi connessi a Visual Studio Archiviazione di Azure)?
## <a name="references-added"></a>Aggiunta di riferimenti
Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto al progetto di Visual Studio.  
Il pacchetto aggiunge i riferimenti a .NET seguenti:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

È stato anche aggiunto il pacchetto NuGet **Microsoft.Framework.Configuration.Json** .

## <a name="connection-string-for-azure-storage-added"></a>Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file config.json del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET 5](http://www.asp.net/vnext).

