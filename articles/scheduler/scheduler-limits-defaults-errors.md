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
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713732"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limiti, quote e soglie di Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è in corso il ritiro. Per pianificare i processi, [provare App per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

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
