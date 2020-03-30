---
title: Integrazione con i criteri di Azure - Azure Batch Documenti Microsoft
description: ''
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/24/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9a306457f838fc79d12be3217d96cc8fb25c9c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77618373"
---
# <a name="integration-with-azure-policy"></a>Integrazione con criteri di AzureIntegration with Azure Policy

Criteri di Azure è un servizio in Azure usato per creare, assegnare e gestire criteri che applicano regole sulle risorse per garantire che tali risorse rimangano conformi agli standard aziendali e ai contratti di servizio. Criteri di Azure valuta le risorse per la non conformità ai criteri assegnati. 

Azure Batch include due estensioni predefinite che consentono di gestire la conformità dei criteri. 

|**Nome**...|   **Descrizione**|**Effetto/i**|  **Version**|    **origine**
|----------------|----------|----------|----------------|---------------|
|È consigliabile abilitare i log di diagnostica negli account Batch|   Controlla l'abilitazione dei log di diagnostica consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa|AuditIfNotExists, Disabled|  2.0.0|  GitHub|
|È consigliabile configurare le regole di avviso per le metriche negli account Batch| Controlla la configurazione delle regole di avviso delle metriche per l'account Batch per abilitare la metrica richiesta|   AuditIfNotExists, Disabled| 1.0.0|  GitHub|

Le definizioni dei criteri descrivono le condizioni che devono essere soddisfatte. Una condizione confronta la proprietà della risorsa con un valore obbligatorio. L'accesso ai campi delle proprietà delle risorse avviene tramite alias predefiniti. Gli alias di proprietà vengono utilizzati per accedere a proprietà specifiche per un tipo di risorsa. Gli alias consentono di limitare le condizioni o i valori consentiti per una proprietà su una risorsa. Ogni alias esegue il mapping ai percorsi in versioni di API diverse per un tipo di risorsa specificato. Durante la valutazione dei criteri, il motore dei criteri ottiene il percorso della proprietà per la versione API specificata.

Le risorse richieste da Batch includono: account, nodo di calcolo, pool, processo e attività. Pertanto, è necessario utilizzare alias di proprietà per accedere a proprietà specifiche per queste risorse. Ulteriori informazioni sugli [alias](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

Per assicurarsi di conoscere gli alias correnti ed esaminare le risorse e i criteri, usare l'estensione dei criteri di Azure per il codice di Visual Studio.To make sure you know the current aliases and review your resources and policies, use the Azure policy extension for Visual Studio Code. Può essere installato su tutte le piattaforme supportate dal codice di Visual Studio.It can be installed on all platforms that are supported by Visual Studio Code. Questo supporto include Windows, Linux e macOS. Vedere [le linee guida per l'installazione](https://docs.microsoft.com/azure/governance/policy/how-to/extension-for-vscode).



