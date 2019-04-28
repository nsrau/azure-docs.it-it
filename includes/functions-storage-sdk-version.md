---
title: File di inclusione
description: File di inclusione
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
origin.date: 05/17/2018
ms.date: 05/30/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306747"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versione di Azure Storage SDK in Funzioni 1.x

In Funzioni 1.x, i trigger di archiviazione e le associazioni usano la versione 7.2.1 di Azure Storage SDK (pacchetto NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Se si fa riferimento a una versione diversa di Storage SDK e si imposta l'associazione a un tipo di Storage SDK nella firma della funzione, il runtime di Funzioni può segnalare che non è possibile l'associazione a quel tipo. La soluzione consiste nell'assicurarsi che il progetto faccia riferimento a [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).

<!-- ms.date: 05/30/2018 -->