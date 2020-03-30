---
title: Rimozione di Azure Sentinel Documenti Microsoft
description: Come eliminare l'istanza di Azure Sentinel.How to delete your Azure Sentinel instance.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581685"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Rimuovere Azure Sentinel dall'area di lavoroRemove Azure Sentinel from your workspace

Se non si vuole più usare Azure Sentinel, in questo articolo viene illustrato come rimuoverlo dall'area di lavoro.

## <a name="how-to-remove-azure-sentinel"></a>Come rimuovere Azure Sentinel

Seguire questa procedura per rimuovere Azure Sentinel dall'area di lavoro:Follow this process to remove Azure Sentinel from your workspace:

1. Passare a **Azure Sentinel**, quindi **Impostazioni**e selezionare la scheda **Rimuovi Azure Sentinel**.

1. Prima di rimuovere Azure Sentinel, usare le caselle di controllo per indicare il motivo della rimozione.

1. Selezionare **Rimuovi Azure Sentinel dall'area di lavoro**.
    
    ![Eliminare la soluzione SecurityInsightsDelete the SecurityInsights solution](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Cosa succede dietro le quinte?

Quando si rimuove la soluzione, Azure Sentinel richiede fino a 48 ore per completare la prima fase del processo di eliminazione.

Dopo aver identificato la disconnessione, inizia il processo di offboarding.

**La configurazione di questi connettori viene rimossa:**
-   Office 365

-   AWS

-   Avvisi di sicurezza dei servizi Microsoft (Azure ATP, Microsoft Cloud App Security, inclusi i report IT Shadow di Individuazione cloud, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Intelligence per le minacce

-   Registri di sicurezza comuni (inclusi i log basati su CEF, Barracuda e Syslog) (se si dispone di Centro sicurezza di Azure, questi log continueranno a essere raccolti).

-   Eventi di sicurezza di Windows (se si dispone del Centro sicurezza di Azure, questi log continueranno a essere raccolti).

Entro le prime 48 ore, i dati e le regole analitiche (inclusa la configurazione dell'automazione in tempo reale) non saranno più accessibili o querybili in Azure Sentinel.

**Dopo 30 giorni queste risorse vengono rimosse:**

-   Incidenti (inclusi i metadati dell'indagine)

-   Regole analitiche

-   Segnalibri

I playbook, le cartelle di lavoro salvate, le query di caccia salvate e i blocchi appunti non vengono rimossi. **Alcuni potrebbero interrompersi a causa dei dati rimossi. È possibile rimuoverli manualmente.**

Dopo aver rimosso il servizio, esiste un periodo di prova di 30 giorni durante il quale è possibile riattivare la soluzione e i dati e le regole analitiche verranno ripristinati, ma i connettori configurati che sono stati disconnessi devono essere riconnessi.

> [!NOTE]
> Se si rimuove la soluzione, la sottoscrizione continuerà a essere registrata con il provider di risorse di Azure Sentinel.If you remove the solution, your subscription will continue to be registered with the Azure Sentinel resource provider. **È possibile rimuoverlo manualmente.**




## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato illustrato come rimuovere il servizio Azure Sentinel.In this document, you learned how to remove the Azure Sentinel service. Se cambi idea e vuoi installarlo di nuovo:
- Iniziare [l'onboarding di Azure Sentinel](quickstart-onboard.md).
