---
title: Sviluppare App per lo Stack di Azure | Documenti Microsoft
description: Considerazioni sullo sviluppo di applicazioni di creazione di prototipi nello Stack di Azure
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Sviluppare per Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile iniziare lo sviluppo di applicazioni di oggi, anche se non si ha accesso a un ambiente dello Stack di Azure. Poiché lo Stack di Azure offre servizi di Microsoft Azure in esecuzione nel Data Center, è possibile utilizzare strumenti e processi analoghi per sviluppare sullo Stack di Azure, come si farebbe con Azure. Con alcune attività di preparazione, utilizzando le indicazioni fornite negli argomenti seguenti, è possibile utilizzare Azure per emulare un ambiente dello Stack di Azure.

* In Azure, è possibile creare modelli di Azure Resource Manager distribuibili in Azure Stack. Vedere [considerazioni sui modelli](azure-stack-develop-templates.md) per informazioni aggiuntive sullo sviluppo di modelli per garantire la portabilità.
* Esistono differenze di disponibilità del servizio e controllo delle versioni del servizio tra Azure e Azure Stack. È possibile utilizzare il [modulo criteri di Azure Stack](azure-stack-policy-module.md) per limitare i tipi di risorse e la disponibilità di servizio di Azure a quello disponibile nello Stack di Azure. Vincolare services garantisce che le applicazioni si basano su servizi disponibili allo Stack di Azure.
* Il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sono comuni scenari di esempio che illustrano come sviluppare modelli che possono essere distribuiti in Azure e Azure Stack.
