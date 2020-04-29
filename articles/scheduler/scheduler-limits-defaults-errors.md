---
title: Limiti, quote e soglie di Utilità di pianificazione di Azure
description: Informazioni su limiti, quote, valori predefiniti e soglie di Utilità di pianificazione di Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898518"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limiti, quote e soglie di Utilità di pianificazione di Azure

> [!IMPORTANT]
> [App](../logic-apps/logic-apps-overview.md) per la logica di Azure sostituisce l'utilità di pianificazione di Azure, che sta per [essere ritirata](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Per continuare a usare i processi configurati nell'utilità di pianificazione, [eseguire la migrazione alle app per la logica di Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) il prima possibile. 
>
> L'utilità di pianificazione non è più disponibile nella portale di Azure, ma i cmdlet di [PowerShell](scheduler-powershell-reference.md) per l' [API REST](/rest/api/scheduler) e l'utilità di pianificazione di Azure restano disponibili in questo momento, in modo da poter gestire processi e raccolte di processi.

## <a name="limits-quotas-and-thresholds"></a>Limiti, quote e soglie

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>L'intestazione x-ms-request-id

Ogni richiesta effettuata nel servizio Utilità di pianificazione restituisce un'intestazione di risposta denominata **x-ms-request-ID**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta. Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, è possibile riportare l'errore a Microsoft usando il valore dell'intestazione di risposta **x-ms-request-id** e includendo questi dettagli: 

* Il valore **x-ms-request-id**
* L'ora approssimativa in cui è stata effettuata la richiesta 
* Gli identificatori per la sottoscrizione di Azure, la raccolta processi e il processo 
* Il tipo di operazione tentata con la richiesta

## <a name="next-steps"></a>Passaggi successivi

* [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)
* [Piani e fatturazione per l'Utilità di pianificazione di Azure](scheduler-plans-billing.md)
* [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](/rest/api/scheduler)
* [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)