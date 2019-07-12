---
title: Abilitare la fusione nell'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come abilitare la fusione in Sentinel di Azure.
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
ms.openlocfilehash: 1e17afb2f06265975442c127baecc8cab75d63f7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611271"
---
# <a name="enable-fusion"></a>Abilitare Fusion

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning in Azure Sentinel è integrata fin dall'inizio. Il sistema sono pensati scrupoloso con lo scopo di rendere produttivi gli analisti della sicurezza, protezione dei dati scienziati e ingegneri innovazioni di Machine Learning. Una tale innovazione è che Fusion Sentinel Azure compilati in particolare per ridurre la fatica di avviso.

Fusion Usa graph basate su algoritmi di machine learning per la correlazione tra milioni di minore attività anomale fedeltà di prodotti diversi, ad esempio Azure AD Identity Protection e Microsoft Cloud App Security, combinarli in un numero gestibile di casi di sicurezza interessanti.

## <a name="enable-fusion"></a>Abilitare Fusion

1. Nel portale di Azure, selezionare l'icona per aprire Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Nel **Benvenuti in Cloud Shell** windows che si apre sotto, selezionare PowerShell.

3.  Scegliere la sottoscrizione in cui è stato distribuito Azure Sentinel, e **creare un archivio**.

4. Dopo che l'utente sia autenticato e l'unità Azure viene compilato, al prompt dei comandi, eseguire i comandi seguenti:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Disabilitare la fusione

Seguire la procedura descritta in precedenza ed eseguire il comando seguente:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Visualizzare lo stato di fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato descritto come abilitare la fusione in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

