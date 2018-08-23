---
title: Sviluppare App per Azure Stack | Microsoft Docs
description: Considerazioni sullo sviluppo di applicazioni di creazione di prototipi in Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: a6f89f9a7e5960e4749c14fc9a4adb648f6781f4
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139539"
---
# <a name="develop-for-azure-stack"></a>Sviluppare per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile iniziare a sviluppare applicazioni oggi stesso, anche se non si ha accesso a un ambiente Azure Stack. Perché Azure Stack offre servizi di Microsoft Azure in esecuzione nel tuo Data Center, è possibile utilizzare gli strumenti e processi simili a sviluppare con Azure Stack come si farebbe con Azure. Con alcune attività di preparazione e seguendo le indicazioni fornite negli argomenti seguenti, è possibile usare Azure per emulare un ambiente Azure Stack.

* In Azure, è possibile creare modelli di Azure Resource Manager distribuibili in Azure Stack. Visualizzare [considerazioni sui modelli](azure-stack-develop-templates.md) per materiale sussidiario sullo sviluppo di modelli per garantire la portabilità.
* Esistono differenze di disponibilità del servizio e controllo delle versioni del servizio tra Azure e Azure Stack. È possibile usare la [modulo di criteri di Azure Stack](azure-stack-policy-module.md) per limitare i tipi di risorse e disponibilità servizio di Azure a quello disponibile in Azure Stack. Applicazione di servizi di un vincolo garantisce che le applicazioni si basano su servizi disponibili in Azure Stack.
* Il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sono comuni scenari di esempio che illustrano come sviluppare modelli che possono essere distribuiti in Azure e Azure Stack.
