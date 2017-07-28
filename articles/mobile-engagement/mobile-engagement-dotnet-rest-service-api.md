---
title: Uso dell&quot;API REST per accedere alle API del servizio Azure Mobile Engagement
description: Descrive come usare le API REST di Mobile Engagement per accedere alle API del servizio Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: 
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: e381eef22b68e89d5407fb452c05eba089b98780
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Uso di REST per accedere alle API del servizio Azure Mobile Engagement
Azure Mobile Engagement offre l'[API REST per Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) che consente di gestire i dispositivi, le campagne push e di copertura e così via.

Per chi non vuole usare le API REST direttamente è disponibile un [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) che è possibile usare con gli strumenti per la generazione degli SDK per la lingua preferita. Per generare l'SDK dal file Swagger è consigliabile usare lo strumento [AutoRest](https://github.com/Azure/AutoRest). In modo simile è stato creato un .NET SDK che consente di interagire con queste API tramite un wrapper C#. Non è necessario eseguire manualmente la negoziazione di token di autenticazione e l'aggiornamento. Vedere l'[esempio di API del servizio .NET SDK](mobile-engagement-dotnet-sdk-service-api.md) per informazioni su come usare .NET SDK per l'API

