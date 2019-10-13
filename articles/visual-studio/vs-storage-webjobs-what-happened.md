---
title: Cosa è successo al progetto processo Web (archiviazione di Visual Studio Azure)?
description: Descrive cosa è accaduto in un progetto processo Web di Azure dopo la connessione a un account di archiviazione con i servizi connessi di Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298737"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Cosa è successo al progetto Webjob (servizio connesso a Visual Studio Archiviazione di Azure)?
## <a name="references-added"></a>Aggiunta di riferimenti
Il pacchetto NuGet per l'Archiviazione di Azure è stato aggiunto o aggiornato nel progetto di Visual Studio.  
Il pacchetto aggiunge i riferimenti a .NET seguenti:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file App.config del progetto gli elementi **AzureWebJobsStorage** e **AzureWebJobsDashboard** sono stati aggiornati con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [Risorse di documentazione di Processi Web di Azure](https://go.microsoft.com/fwlink/?linkid=390226).

