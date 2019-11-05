---
title: Connettere F5 BIG-IP data ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere F5 BIG-IP data a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: rkarlin
ms.openlocfilehash: ff5a8f940348c36ca2a19fb31a173b2dfc4f4d6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514134"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Connettere il dispositivo F5 BIG-IP 

> [!IMPORTANT]
> Il connettore di dati BIG-IP di F5 in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il connettore F5 BIG-IP consente di connettere facilmente tutti i log di F5 BIG-IP con Azure Sentinel, visualizzare le cartelle di lavoro, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. L'integrazione tra F5 BIG-IP e Azure Sentinel usa l'API REST.


> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Configurare e connettere F5 BIG-IP 

F5 BIG-IP può integrare ed esportare i log direttamente in Sentinel di Azure.

1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **F5 Big-IP** , quindi **aprire la pagina del connettore**. 
1. Per connettere F5 BIG-IP, è necessario inviare una dichiarazione JSON all'endpoint API del sistema. Per istruzioni su come eseguire questa operazione, vedere [integrazione di F5 Big-IP con Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. Dalla pagina F5 connettore BIG-IP, copiare l'ID dell'area di lavoro e la chiave primaria e incollarli come indicato in [streaming dei dati in Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Dopo aver completato le istruzioni di F5 BIG-IP, nella pagina connettore di Sentinel di Azure è possibile visualizzare i tipi di dati connessi.
1. Per usare lo schema pertinente in Log Analytics per gli eventi di F5 BIG-IP, cercare **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL**e **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere F5 BIG-IP ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

