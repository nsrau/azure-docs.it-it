---
title: Uso dell'API REST per accedere alle API del servizio Azure Mobile Engagement
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
ms.openlocfilehash: 4b21bca6fee7012ce1dba96950ae075eded414f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Uso di REST per accedere alle API del servizio Azure Mobile Engagement
Azure Mobile Engagement offre l'[API REST per Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) che consente di gestire i dispositivi, le campagne push e di copertura e così via.

> [!NOTE]
> Il servizio Azure Mobile Engagement verrà ritirato a marzo 2018 ed è attualmente disponibile solo per i clienti esistenti. Per altre informazioni, vedere [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Per chi non vuole usare le API REST direttamente è disponibile un [file Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) che è possibile usare con gli strumenti per la generazione degli SDK per la lingua preferita. Per generare l'SDK dal file Swagger è consigliabile usare lo strumento [AutoRest](https://github.com/Azure/AutoRest). In modo simile è stato creato un .NET SDK che consente di interagire con queste API tramite un wrapper C#. Non è necessario eseguire manualmente la negoziazione di token di autenticazione e l'aggiornamento. Vedere l'[esempio di API del servizio .NET SDK](mobile-engagement-dotnet-sdk-service-api.md) per informazioni su come usare .NET SDK per l'API
