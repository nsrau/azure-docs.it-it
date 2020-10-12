---
title: includere file
description: includere file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77198420"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Versione di Azure Storage SDK in Funzioni 1.x

In Funzioni 1.x, i trigger di archiviazione e le associazioni usano la versione 7.2.1 di Azure Storage SDK (pacchetto NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Se si fa riferimento a una versione diversa di Storage SDK e si imposta l'associazione a un tipo di Storage SDK nella firma della funzione, il runtime di Funzioni può segnalare che non è possibile l'associazione a quel tipo. La soluzione consiste nell'assicurarsi che il progetto faccia riferimento a [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
