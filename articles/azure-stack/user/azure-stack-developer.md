---
title: Sviluppare App per lo Stack di Azure | Documenti Microsoft
description: Altre considerazioni di sviluppo di applicazioni di creazione di prototipi nello Stack di Azure
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>Sviluppare per Azure Stack
È possibile iniziare lo sviluppo di applicazioni di oggi, anche se non si ha accesso a un ambiente dello Stack di Azure. Poiché lo Stack di Azure offre servizi di Microsoft Azure in esecuzione nel Data Center, è possibile utilizzare strumenti e processi analoghi per sviluppare sullo Stack di Azure, come si farebbe con Azure.  Con un minimo di preparazione e alle linee guida gli argomenti seguenti, è possibile utilizzare Azure per emulare un ambiente dello Stack di Azure:

* In Azure, è possibile creare modelli di gestione risorse di Azure che sono anche distribuibili allo Stack di Azure.  Vedere [considerazioni sui modelli](azure-stack-develop-templates.md) per informazioni aggiuntive sullo sviluppo di modelli per garantire la portabilità.
* Non vi è un delta di disponibilità del servizio e controllo delle versioni di servizio tra Azure e Azure Stack. È possibile utilizzare il [modulo criteri di Azure Stack](azure-stack-policy-module.md) per limitare i tipi di risorse e la disponibilità di servizio di Azure a quello disponibile nello Stack di Azure. Vincolare i servizi disponibili consentirà l'applicazione si basano sui servizi disponibili allo Stack di Azure.
* Il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sono esempi comuni di uno scenario di sviluppo di modelli affinché possano essere distribuiti in Azure sia Azure Stack.


