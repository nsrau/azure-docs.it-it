---
title: File di inclusione
description: File di inclusione
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424147"
---
> [!NOTE]
> Un'app Web può raggiungere il timeout dopo 20 minuti di inattività. Solo le richieste all'app Web effettiva comportano la reimpostazione del timer. La visualizzazione della configurazione dell'app nel portale di Azure`https://<app_name>.scm.azurewebsites.net`o l'esecuzione di richieste al sito degli strumenti avanzati ( ) non reimposta il timer. Se l'app esegue processi Web continui o pianificati (trigger Timer), abilitare **Always On** per garantire che i WebJobs vengano eseguiti in modo affidabile. Questa funzionalità è disponibile solo nei [piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard e Premium.
