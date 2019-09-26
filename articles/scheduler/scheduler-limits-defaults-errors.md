---
title: Limiti, quote e soglie di Utilità di pianificazione di Azure
description: Informazioni su limiti, quote, valori predefiniti e soglie di Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300913"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limiti, quote e soglie di Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App](../logic-apps/logic-apps-overview.md) per la logica di Azure sostituisce l'utilità di pianificazione di Azure, che sta per [essere ritirata](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile.

## <a name="limits-quotas-and-thresholds"></a>Limiti, quote e soglie

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>L'intestazione x-ms-request-id

Ogni richiesta effettuata per il servizio dell'Utilità di pianificazione restituisce un'intestazione di risposta denominata **x-ms-request-id**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta. Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, è possibile riportare l'errore a Microsoft usando il valore dell'intestazione di risposta **x-ms-request-id** e includendo questi dettagli: 

* Il valore **x-ms-request-id**
* L'ora approssimativa in cui è stata effettuata la richiesta 
* Gli identificatori per la sottoscrizione di Azure, la raccolta processi e il processo 
* Il tipo di operazione tentata con la richiesta

## <a name="see-also"></a>Vedere anche

* [Informazioni su Utilità di pianificazione di Azure](scheduler-intro.md)
* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
