---
title: Configurare Sentinel (anteprima)
description: Viene illustrato come configurare Azure Sentinel per ricevere dati dal Centro sicurezza di Azure per la soluzione IoT.Explains how to configure Azure Sentinel to receive data from your Azure Security Center for IoT solution.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: fe8e4c1b08f96e5f6b2fc7649f7a4361616b7c87
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311308"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Connettere i dati dal Centro sicurezza di Azure per l'IoT ad Azure Sentinel (anteprima)Connect your data from Azure Security Center for IoT to Azure Sentinel (preview)

> [!IMPORTANT]
> Il connettore dati del Centro sicurezza di Azure per IoT in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In this guide, learn how to connect your Azure Security Center for IoT data to Azure Sentinel.

> [!div class="checklist"]
> * Prerequisiti
> * Impostazioni di connessione
> * Visualizzazione degli avvisi di Log AnalyticsLog Analytics alert view

Connettere avvisi dal Centro sicurezza di Azure per l'IoT e trasmetterli direttamente in Azure Sentinel.Connect alerts from Azure Security Center for IoT and stream them directly into Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di **lettura** e **scrittura** dell'area di lavoro.
- **Il Centro sicurezza di Azure per ioT** deve essere **abilitato** negli hub IoT pertinenti.
- È necessario disporre di autorizzazioni di **lettura** e **scrittura** nell'hub **IoT** di Azure che si vuole connettere.
- È inoltre necessario disporre delle autorizzazioni di **lettura** e **scrittura** per il gruppo di **risorse Hub IoT**di Azure.

> [!NOTE]
> Per inviare avvisi generali sulle risorse di Azure, è necessario disporre delle licenze di livello Standard del Centro sicurezza di Azure in esecuzione nella sottoscrizione. Con la licenza di livello gratuito necessaria per il Centro sicurezza di Azure per l'IoT, solo gli avvisi correlati al Centro sicurezza di Azure per IoT verranno inoltrati ad Azure Sentinel.With the free tier licensing required for Azure Security Center for IoT, only Azure Security Center for IoT related alerts will be forwarded to Azure Sentinel.

## <a name="connect-to-azure-security-center-for-iot"></a>Connettersi al Centro sicurezza di Azure per IoTConnect to Azure Security Center for IoT

1. In Azure Sentinel selezionare Connettori dati e quindi fare clic sul **riquadro Centro sicurezza di Azure per IoT.In** Azure Sentinel, select **Data connectors** and then click the Azure Security Center for IoT tile.
1. Nella parte inferiore del riquadro destro fare clic su **Apri pagina connettore**.
1. Fare clic su **Connetti**, accanto a ogni sottoscrizione dell'hub IoT di cui si desidera trasmettere gli avvisi e gli avvisi per i dispositivi in Azure Sentinel.
    - Se il Centro sicurezza di Azure per IoT non è abilitato in tale hub, verrà visualizzato un messaggio di avviso Abilita.If Azure Security Center for IoT isn't enabled on that Hub, you'll see an Enable warning message. Fare clic sul collegamento **Abilita** per avviare e abilitare il servizio.
1. È possibile decidere se si vuole che gli avvisi dal Centro sicurezza di Azure per IoT generino automaticamente eventi imprevisti in Azure Sentinel.You can decide whether you want the alerts from Azure Security Center for IoT to automatically generates incidents in Azure Sentinel. In **Crea eventi imprevisti**selezionare **Abilita** per abilitare la regola per la creazione automatica di eventi imprevisti dagli avvisi generati.  Questa regola può essere modificata o modificata in Regole**attive** **di Analytics.** > 

> [!NOTE]
>L'aggiornamento dell'elenco hub dopo aver apportato modifiche alla connessione possono richiedere 10 o più secondi.

## <a name="log-analytics-alert-display"></a>Visualizzazione degli avvisi di Log Analytics

Per usare lo schema pertinente in Log Analytics per visualizzare il Centro sicurezza di Azure per gli avvisi IoT:To use the relevant schema in Log Analytics to display the Azure Security Center for IoT alerts:

1. Aprire **Registri** > **SecurityInsights** > **SecurityAlert**oppure cercare **SecurityAlert**.
1. Filtrare per visualizzare solo gli avvisi generati dal Centro sicurezza di Azure per IoT usando il filtro kql seguente:Filter to see only Azure Security Center for IoT generated alerts using the following kql filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
```

### <a name="service-notes"></a>Note di servizio

Dopo aver connesso un hub IoT, i dati dell'hub sono disponibili in Azure Sentinel circa 15 minuti dopo.

## <a name="next-steps"></a>Passaggi successivi

In this document, you learned how to connect Azure Security Center for IoT to Azure Sentinel. Per altre informazioni sul rilevamento delle minacce e sull'accesso ai dati di sicurezza, vedere gli articoli seguenti:To learn more about threat detection and security data access, see the following articles:

- Informazioni su come usare Azure Sentinel per [ottenere visibilità sui dati e](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)sulle potenziali minacce.

- Scopri come accedere ai tuoi dati di [sicurezza IoT](how-to-security-data-access.md)
