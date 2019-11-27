---
title: Offboard di Azure Sentinel | Microsoft Docs
description: Come eliminare l'istanza di Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: a45f273078a622de5e256457fc45b6cb6cae512f
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464132"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Rimuovere Azure Sentinel dal tenant

Se non si vuole più usare Azure Sentinel, questo articolo illustra come rimuoverlo dal tenant.

## <a name="how-to-delete-azure-sentinel"></a>Come eliminare Sentinel di Azure

In background, quando si installa Azure Sentinel, la soluzione **SecurityInsights** viene installata nell'area di lavoro selezionata. Per prima cosa è necessario rimuovere la soluzione **SecurityInsights** .

1.  Passare ad **Azure Sentinel**, seguito dalla **configurazione**, seguito dalle **impostazioni dell'area di lavoro**e quindi dalle **soluzioni**.

2.  Selezionare `SecurityInsights` e fare clic su di essa.

    ![Trovare la soluzione SecurityInsights](media/offboard/find-solution.png)

3.  Nella parte superiore della pagina selezionare **Elimina**.

    > [!IMPORTANT]
    > Se si rimuove l'area di lavoro, può interessare altre soluzioni e origini dati che usano questa area di lavoro, incluso monitoraggio di Azure. Per verificare quali soluzioni usano questa area di lavoro, vedere [elencare le soluzioni di monitoraggio installate](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions). Per verificare quali sono i dati delle soluzioni da inserire nell'area di lavoro, vedere [informazioni sul volume dei dati](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)inseriti.

    ![Eliminare la soluzione SecurityInsights](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Cosa accade dietro le quinte?

Quando si elimina la soluzione, Azure Sentinel impiega fino a 48 ore per completare la prima fase del processo di eliminazione.

Una volta identificata la disconnessione, viene avviato il processo offboarding.

**La configurazione di questi connettori è stata eliminata:**
-   Office 365

-   AWS

-   Avvisi di sicurezza dei servizi Microsoft (Azure ATP, Microsoft Cloud App Security inclusi Cloud Discovery segnalazione Shadow IT, Azure AD Identity Protection, Microsoft Defender ATP, Centro sicurezza di Azure)

-   Intelligence per le minacce

-   Log di sicurezza comuni (inclusi i log basati su CEF, Barracuda e syslog). Se si dispone del Centro sicurezza di Azure, questi log continueranno a essere raccolti.

-   Eventi di sicurezza di Windows. Se si dispone del Centro sicurezza di Azure, questi log continueranno a essere raccolti.

Nelle prime 48 ore, le regole relative a dati e avvisi (inclusa la configurazione di automazione in tempo reale) non saranno più accessibili o disponibili per le query in Sentinel di Azure.

**Dopo 30 giorni, vengono eliminate le risorse seguenti:**

-   Eventi imprevisti (inclusi i metadati di analisi)

-   Regole di avviso

-   segnalibri

I PlayBook, le cartelle di lavoro salvate, le query di ricerca salvate e i notebook non vengono eliminati. **Alcune potrebbero interrompersi a causa dei dati rimossi. È possibile rimuoverli manualmente.**

Dopo aver rimosso il servizio, si verifica un periodo di tolleranza di 30 giorni durante il quale è possibile riabilitare la soluzione e i dati e le regole di avviso verranno ripristinati, ma i connettori configurati che erano disconnessi devono essere riconnessi.

> [!NOTE]
> Se si rimuove la soluzione, la sottoscrizione continuerà a essere registrata con il provider di risorse Sentinel di Azure. **È possibile rimuoverlo manualmente.**




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come rimuovere il servizio Sentinel di Azure. Se si cambia idea e si vuole installarlo di nuovo:
- Introduzione [all'onboarding di Azure Sentinel](quickstart-onboard.md).

