---
title: Connettere Azure Defender per le cose a Sentinel di Azure | Microsoft Docs
description: Informazioni su come connettere Azure Defender (in precedenza il Centro sicurezza di Azure) per i dati di Internet delle cose in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659623"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Connetti i tuoi dati da Azure Defender (noto in precedenza come Centro sicurezza di Azure) per le cose ad Azure Sentinel 


> [!IMPORTANT]
> Il connettore Azure Defender for Internet è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare il connettore Azure Defender for Internet per trasmettere in streaming tutti gli eventi di Azure Defender per gli eventi di Azure in Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- Autorizzazioni di **lettura** e **scrittura** per l'area di lavoro in cui viene distribuito Azure Sentinel
- **Azure Defender per** l'IT deve essere **abilitato** negli hub degli oggetti Internet rilevanti
- Autorizzazioni di **lettura** e **scrittura** nell' **Hub Azure** Internet che si vuole connettere
- Autorizzazioni di **lettura** e **scrittura** per il **gruppo di risorse dell'hub Azure** .

## <a name="connect-to-azure-defender-for-iot"></a>Connettersi ad Azure Defender per l'it

1. In Sentinel di Azure selezionare **connettori di dati** e quindi selezionare **Azure Defender per** le cose (potrebbe essere ancora chiamato Centro sicurezza di Azure per tutto) dalla raccolta.
1. Dal riquadro in basso a destra fare clic su **Apri pagina connettore**. 
1. Fare clic su **Connetti**accanto a ogni sottoscrizione dell'hub Internet con avvisi e avvisi del dispositivo che si vuole trasmettere in Azure Sentinel. 
    - Se Azure Defender per l'it non è abilitato nell'hub, verrà visualizzato un messaggio di avviso di **Abilitazione** . Fare clic sul collegamento **Enable (Abilita** ) per avviare il servizio. 
1. È possibile decidere se si desidera che gli avvisi di Azure Defender per gli eventi di Azure vengano generati automaticamente in Sentinel di Azure. In **crea eventi imprevisti**selezionare **Abilita** per abilitare la regola di analisi predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. Questa regola può essere modificata o modificata in **analisi**  >  regole**attive** .

> [!NOTE]
> Dopo aver apportato le modifiche alla connessione, l'elenco hub potrebbe richiedere del tempo. 

## <a name="log-analytics-alert-display"></a>Visualizzazione Log Analytics avviso

Per usare lo schema pertinente in Log Analytics per visualizzare gli avvisi di Azure Defender per le cose:

1. Aprire **logs**  >  **SecurityInsights**  >  **SecurityAlert**o cercare **SecurityAlert**. 
2. Filtrare per visualizzare solo Azure Defender per gli avvisi generati da Internet con il filtro KQL seguente:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Note servizio

Dopo la connessione di un hub Internet delle cose, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.


## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Azure Defender per i dati relativi ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
