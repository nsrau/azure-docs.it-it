---
title: "Cosa è successo a un progetto ASP.NET? | Microsoft Docs"
description: Viene descritto cosa succede dopo l'aggiunta di archiviazione di Azure a un progetto ASP.NET utilizzando i servizi connessi a Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 680960f51201a98c81db4bce3615e1602e795651
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Cosa è successo a un progetto ASP.NET (servizio connesso a Visual Studio Archiviazione di Azure)?
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

## <a name="connection-string-for-azure-storage-added"></a>Aggiunta di stringa di connessione per l'Archiviazione di Azure
Nel file web.config del progetto è stato creato un elemento con la stringa di connessione e la chiave dell'account di archiviazione selezionato.

Per altre informazioni, vedere [ASP.NET](http://www.asp.net).

