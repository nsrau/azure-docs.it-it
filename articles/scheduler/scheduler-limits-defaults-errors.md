---
title: "Limiti e impostazioni predefinite dell'Utilità di pianificazione"
description: "Limiti e impostazioni predefinite dell'Utilità di pianificazione"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-limits-and-defaults"></a>Limiti e impostazioni predefinite dell'Utilità di pianificazione
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Quote, limiti, impostazioni predefinite e limiti dell'utilità di pianificazione
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>L'intestazione x-ms-request-id
Ogni richiesta effettuata per il servizio dell’Utilità di pianificazione restituisce un'intestazione di risposta denominata**x-ms-request-id**. Questa intestazione contiene un valore opaco che identifica in modo univoco la richiesta.

Se una richiesta fallisce sistematicamente e si è verificato che la richiesta è formulata in modo appropriato, si può usare questo valore per riportare l'errore a Microsoft. Nel report includere il valore di x-ms-request-id, l'ora approssimativa in cui è stata eseguita la richiesta, l'identificativo della sottoscrizione, la raccolta processi e/o il processo e il tipo di operazione tentata con la richiesta.

## <a name="see-also"></a>Vedere anche
 [Che cos'è l'Utilità di pianificazione?](scheduler-intro.md)

 [Concetti, terminologia e gerarchia di entità dell'Utilità di pianificazione di Azure](scheduler-concepts-terms.md)

 [Introduzione all'uso dell'Utilità di pianificazione di Azure nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione nell'utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Informazioni di riferimento sull'API REST dell'Utilità di pianificazione di Azure](https://msdn.microsoft.com/library/mt629143)

 [Informazioni di riferimento sui cmdlet PowerShell dell'Utilità di pianificazione di Azure](scheduler-powershell-reference.md)

 [Livelli elevati di disponibilità e affidabilità dell'Utilità di pianificazione di Azure](scheduler-high-availability-reliability.md)

 [Autenticazione in uscita dell'Utilità di pianificazione di Azure](scheduler-outbound-authentication.md)

