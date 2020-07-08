---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279631"
---
> [!WARNING]
> Quando si abilita la diagnostica per un ruolo esistente, tutte le estensioni già impostate vengono disabilitate quando viene distribuito il pacchetto. Tra queste sono incluse:
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
