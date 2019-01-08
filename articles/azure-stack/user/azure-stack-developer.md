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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 51f3861d53ac5dac80b53fad9a4efe7b276807fe
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065410"
---
# <a name="develop-for-azure-stack"></a>Sviluppare per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile iniziare a sviluppare applicazioni oggi stesso, anche se non si ha accesso a un ambiente Azure Stack. Perché Azure Stack offre servizi di Microsoft Azure in esecuzione nel tuo Data Center, è possibile utilizzare gli strumenti e processi simili a sviluppare con Azure Stack come si farebbe con Azure.

## <a name="development-considerations"></a>Considerazioni sullo sviluppo

Con alcune attività di preparazione e seguendo le indicazioni fornite negli argomenti seguenti, è possibile usare Azure per emulare un ambiente Azure Stack.

* In Azure, è possibile creare modelli di Azure Resource Manager distribuibili in Azure Stack. Visualizzare [considerazioni sui modelli](azure-stack-develop-templates.md) per materiale sussidiario sullo sviluppo di modelli per garantire la portabilità.
* Esistono differenze di disponibilità del servizio e controllo delle versioni del servizio tra Azure e Azure Stack. È possibile usare la [modulo di criteri di Azure Stack](azure-stack-policy-module.md) per limitare i tipi di risorse e disponibilità servizio di Azure a quello disponibile in Azure Stack. Applicazione di servizi di un vincolo garantisce che le applicazioni si basano su servizi disponibili in Azure Stack.
* Il [modelli di avvio rapido di Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) sono comuni scenari di esempio che illustrano come sviluppare modelli che possono essere distribuiti in Azure e Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo di Azure Stack, vedere gli articoli seguenti:

* [Azure Resource Manager consigliate sui modelli](azure-stack-develop-templates.md)
* [Modelli di avvio rapido di Azure Stack in GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)