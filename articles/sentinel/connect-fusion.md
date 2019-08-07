---
title: Abilitare Fusion nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come abilitare Fusion in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780449"
---
# <a name="enable-fusion"></a>Abilitare Fusion

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il Machine Learning in Sentinel di Azure è incorporato sin dall'inizio. Il sistema è stato progettato in modo accurato con le innovazioni di ML mirate a rendere produttivi gli analisti della sicurezza, i data scientist e i tecnici. Una di queste innovazioni è la fusione di Azure Sentinel creata in particolare per ridurre la fatica degli avvisi.

Fusion usa algoritmi di machine learning basati su grafo per correlare tra milioni di attività anomale di fedeltà più bassa da prodotti diversi, ad esempio Azure AD Identity Protection e Microsoft Cloud App Security, per combinarli in un numero gestibile di problemi di sicurezza interessanti.

## <a name="enable-fusion"></a>Abilitare Fusion

1. Nella portale di Azure selezionare l'icona per aprire Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Nella pagina **iniziale di cloud Shell** Windows visualizzata di seguito, selezionare PowerShell.

3.  Scegliere la sottoscrizione in cui è stato distribuito Azure Sentinel e **creare l'archiviazione**.

4. Dopo che l'utente è stato autenticato e l'unità Azure è stata compilata, al prompt dei comandi eseguire i comandi seguenti:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Disabilita Fusion

Seguire la stessa procedura descritta in precedenza ed eseguire il comando seguente:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Visualizzare lo stato di Fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come abilitare Fusion in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).

