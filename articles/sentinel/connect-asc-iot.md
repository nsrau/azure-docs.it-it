---
title: Collegare il Centro sicurezza di Azure per l'IoT ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere il Centro sicurezza di Azure per i dati IoT ad Azure Sentinel.Learn how to connect Azure Security Center for IoT data to Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588638"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>Connettere i dati dal Centro sicurezza di Azure per l'IoT ad Azure SentinelConnect your data from Azure Security Center for IoT to Azure Sentinel 


> [!IMPORTANT]
> Il connettore dati del Centro sicurezza di Azure per IoT è attualmente in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usare il connettore Centro sicurezza di Azure per IoT per trasmettere tutti i centro sicurezza di Azure per gli eventi IoT in Azure Sentinel.Use the Azure Security Center for IoT connector to stream all your Azure Security Center for IoT events into Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisiti

- **Autorizzazioni di lettura** e scrittura nell'area di lavoro in cui viene distribuito Azure SentinelRead and **Write** permissions on the Workspace onto which Azure Sentinel is deployed
- **Il Centro sicurezza di Azure per l'IoT** deve essere abilitato negli hub IoT pertinentiAzure Security Center for IoT must be **enabled** on your relevant IoT Hub(s)
- **Autorizzazioni di lettura** e **scrittura** nell'hub **IoT** di Azure a cui si vuole connettersi
- **Autorizzazioni di lettura** e scrittura per il gruppo di **risorse dell'hub IoT di AzureRead** and **Write** permissions on the Azure IoT Hub resource group

> [!NOTE]
> Sebbene sia necessario abilitare la licenza di livello Standard del Centro sicurezza di Azure nella sottoscrizione per trasmettere gli avvisi delle risorse IoT ad Azure Sentinel, è sufficiente abilitare la licenza di livello Gratuito del Centro sicurezza di Azure nella sottoscrizione per visualizzare il Centro sicurezza di Azure per gli avvisi IoT in Azure Sentinel.While you must only enable the Azure Security Center **Standard** tier license on your subscription to stream IoT resource alerts to Azure Sentinel, you only need to enable the Azure Security Center **Free** tier license on your subscription to view Azure Security Center for IoT alerts in Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Connettersi al Centro sicurezza di Azure per IoTConnect to Azure Security Center for IoT

1. In Azure Sentinel selezionare Connettori dati e quindi fare clic sul **riquadro Centro sicurezza di Azure per IoT.In** Azure Sentinel, select **Data connectors** and then click the Azure Security Center for IoT tile.
1. Nel riquadro in basso a destra fare clic su **Apri pagina connettore**. 
1. Fare clic su **Connetti**, accanto a ogni sottoscrizione dell'hub IoT di cui si desidera trasmettere gli avvisi e gli avvisi per i dispositivi in Azure Sentinel. 
    - Se il Centro sicurezza di Azure per IoT non è abilitato in tale hub, verrà visualizzato un messaggio di avviso **Abilita.If** Azure Security Center for IoT is not enabled on that Hub, you'll see an Enable warning message. Fare clic sul collegamento **Abilita** per avviare il servizio. 
1. È possibile decidere se si vuole che gli avvisi dal Centro sicurezza di Azure per IoT generino automaticamente eventi imprevisti in Azure Sentinel.You can decide whether you want the alerts from Azure Security Center for IoT to automatically generates incidents in Azure Sentinel. In **Crea eventi imprevisti**selezionare **Abilita** per abilitare la regola analitica predefinita per creare automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. Questa regola può essere modificata o modificata in Regole**attive** **di Analytics.** > 

> [!NOTE]
> L'aggiornamento dell'elenco hub dopo aver apportato modifiche alla connessione può richiedere del tempo. 

## <a name="log-analytics-alert-display"></a>Visualizzazione degli avvisi di Log Analytics

Per usare lo schema pertinente in Log Analytics per visualizzare il Centro sicurezza di Azure per gli avvisi IoT:To use the relevant schema in Log Analytics to display the Azure Security Center for IoT alerts:

1. Aprire **Registri** > **SecurityInsights** > **SecurityAlert**oppure cercare **SecurityAlert**. 
2. Filtrare per visualizzare solo gli avvisi generati dal Centro sicurezza di Azure per IoT usando il filtro kql seguente:Filter to see only Azure Security Center for IoT generated alerts using the following kql filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Note di servizio

Dopo aver connesso un hub IoT, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.


## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect Azure Security Center for IoT data to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
