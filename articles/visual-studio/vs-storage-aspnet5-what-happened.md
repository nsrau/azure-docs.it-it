---
title: Cosa è successo al progetto ASP.NET 5 (servizi connessi a Visual Studio)
description: Viene descritto cosa succede dopo la connessione a un account di archiviazione di Azure in un progetto di Visual Studio ASP.NET 5 utilizzando i servizi connessi a Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300009"
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

Per altre informazioni, vedere [ASP.NET 5](https://www.asp.net/vnext).

