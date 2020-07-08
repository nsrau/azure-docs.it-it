---
title: includere il file
description: includere file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68424147"
---
> [!NOTE]
> Un'app Web può raggiungere il timeout dopo 20 minuti di inattività. Solo le richieste all'app Web effettiva comportano la reimpostazione del timer. La visualizzazione della configurazione dell'app nel portale di Azure o l'esecuzione di richieste al sito degli strumenti avanzati ( `https://<app_name>.scm.azurewebsites.net` ) non reimposta il timer. Se l'app esegue processi Web continui o pianificati (trigger timer), abilitare **Always on** per assicurarsi che i processi Web vengano eseguiti in modo affidabile. Questa funzionalità è disponibile solo nei [piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard e Premium.
