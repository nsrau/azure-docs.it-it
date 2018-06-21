---
title: File di inclusione
description: File di inclusione
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675276"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versione di Azure Storage SDK in Funzioni 1.x

In Funzioni 1.x, i trigger di archiviazione e le associazioni usano la versione 7.2.1 di Azure Storage SDK (pacchetto NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Se si fa riferimento a una versione diversa di Storage SDK e si imposta l'associazione a un tipo di Storage SDK nella firma della funzione, il runtime di Funzioni può segnalare che non è possibile l'associazione a quel tipo. La soluzione consiste nell'assicurarsi che il progetto faccia riferimento a [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
