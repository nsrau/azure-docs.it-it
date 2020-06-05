---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648253"
---
Funzioni di Azure supporta la condivisione di risorse tra origini (CORS, Cross-Origin Resource Sharing). La funzionalità CORS è configurata [nel portale](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) e tramite l'[interfaccia della riga di comando di Azure](/cli/azure/functionapp/cors). L'elenco delle origini consentite per CORS si applica a livello di app per le funzioni. Con la funzionalità CORS abilitata, le risposte includono l'intestazione `Access-Control-Allow-Origin`. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 