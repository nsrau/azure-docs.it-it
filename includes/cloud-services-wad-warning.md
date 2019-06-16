---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125380"
---
> [!WARNING]
> Quando si abilita la diagnostica per un ruolo esistente, tutte le estensioni già impostate vengono disabilitate quando viene distribuito il pacchetto. incluse le seguenti:
>
> * Diagnostica di Microsoft Monitoring Agent
> * Microsoft Azure Security Monitoring
> * Antimalware Microsoft                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler Agent      
> * Windows Azure Domain Extension        
> * Windows Azure Diagnostics Extension   
> * Windows Azure Remote Desktop Extension
> * Windows Azure Log Collector
>
> Dopo avere distribuito il ruolo aggiornato, è possibile reimpostare le estensioni tramite il portale di Azure o PowerShell.
>
