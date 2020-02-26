---
title: Connettere il Centro sicurezza di Azure per le cose ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere il Centro sicurezza di Azure per i dati di Internet delle cose in Sentinel di Azure.
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
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588638"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Connetti i tuoi dati dal centro sicurezza di Azure per le cose ad Azure Sentinel 


> [!IMPORTANT]
> Il Centro sicurezza di Azure per il connettore dati su Internet è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare il Centro sicurezza di Azure per il connettore Internet per eseguire lo streaming di tutti gli eventi del Centro sicurezza di Azure in Sentinel di Azure. 

## <a name="prerequisites"></a>Prerequisites

- Autorizzazioni di **lettura** e **scrittura** per l'area di lavoro in cui viene distribuito Azure Sentinel
- Il **Centro sicurezza di Azure per** l'IT deve essere **abilitato** negli hub degli oggetti Internet rilevanti
- Autorizzazioni di **lettura** e **scrittura** nell' **Hub Azure** Internet che si vuole connettere
- Autorizzazioni di **lettura** e **scrittura** per il **gruppo di risorse dell'hub Azure** .

> [!NOTE]
> Anche se è necessario abilitare la licenza del livello **standard** del Centro sicurezza di Azure per la sottoscrizione per trasmettere gli avvisi delle risorse di Azure ad Sentinel, è sufficiente abilitare la licenza del livello **gratuito** del Centro sicurezza di Azure per la sottoscrizione per visualizzare il Centro sicurezza di Azure per gli avvisi di Azure in Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Connetti al centro sicurezza di Azure per le cose

1. In Sentinel di Azure selezionare **connettori dati** e quindi fare clic sul **Centro sicurezza di Azure per** il riquadro degli altri.
1. Dal riquadro in basso a destra fare clic su **Apri pagina connettore**. 
1. Fare clic su **Connetti**accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel. 
    - Se il Centro sicurezza di Azure non è abilitato nell'hub, verrà visualizzato un messaggio di avviso di **Abilitazione** . Fare clic sul collegamento **Enable (Abilita** ) per avviare il servizio. 
1. È possibile decidere se si desidera che gli avvisi del Centro sicurezza di Azure possano generare automaticamente eventi imprevisti in Sentinel di Azure. In **crea eventi imprevisti**selezionare **Abilita** per abilitare la regola di analisi predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. Questa regola può essere modificata o modificata in **Analytics** > regole **attive** .

> [!NOTE]
> Dopo aver apportato le modifiche alla connessione, l'elenco hub potrebbe richiedere del tempo. 

## <a name="log-analytics-alert-display"></a>Visualizzazione Log Analytics avviso

Per usare lo schema pertinente in Log Analytics per visualizzare il Centro sicurezza di Azure per gli avvisi relativi agli elementi Internet:

1. Aprire **Logs** > **SecurityInsights** > **SecurityAlert**o cercare **SecurityAlert**. 
2. Filtrare per visualizzare solo il Centro sicurezza di Azure per gli avvisi generati dagli elementi con il seguente filtro KQL:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Note servizio

Dopo la connessione di un hub Internet delle cose, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere il Centro sicurezza di Azure per i dati relativi ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.
