---
title: Monitorare l'integrità di un'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di operatore o amministratore, monitorare l'integrità complessiva dei dispositivi connessi all'applicazione IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 664819b209aeb09093ce8711456b86ff4d3e8949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84249476"
---
# <a name="monitor-the-overall-health-of-the-devices-connected-to-an-iot-central-application"></a>Monitorare l'integrità complessiva dei dispositivi connessi a un'applicazione IoT Central

*Questo articolo si applica a operatori e amministratori.*

Questo articolo illustra come usare il set di metriche fornito da IoT Central per valutare l'integrità complessiva dei dispositivi connessi all'applicazione IoT Central.

Le metriche sono abilitate per impostazione predefinita per l'applicazione IoT Central e sono accessibili dall' [portale di Azure](https://portal.azure.com/). La [piattaforma dati di monitoraggio di Azure espone queste metriche](../../azure-monitor/platform/data-platform-metrics.md) e offre diversi modi per interagire con loro. Ad esempio, è possibile usare i grafici nell'portale di Azure, un'API REST o query in PowerShell o nell'interfaccia della riga di comando di Azure.

### <a name="trial-applications"></a>Applicazioni di valutazione

Le applicazioni che usano il piano di valutazione gratuita non hanno una sottoscrizione di Azure associata e quindi non supportano le metriche di monitoraggio di Azure. È possibile [convertire un'applicazione in un piano tariffario standard](./howto-view-bill.md#move-from-free-to-standard-pricing-plan) e ottenere l'accesso a queste metriche.

## <a name="view-metrics-in-the-azure-portal"></a>Visualizzare le metriche nell'portale di Azure

La procedura seguente presuppone che si disponga di un' [applicazione IoT Central](./quick-deploy-iot-central.md) con alcuni [dispositivi connessi](./tutorial-connect-device-nodejs.md).

Per visualizzare IoT Central metriche nel portale:

1. Passare alla risorsa dell'applicazione IoT Central nel portale. Per impostazione predefinita, le risorse di IoT Central si trovano in un gruppo di risorse denominato **IOTC**.
1. Per creare un grafico dalle metriche dell'applicazione, selezionare **metriche** nella sezione **monitoraggio** .

### <a name="azure-portal-permissions"></a>Autorizzazioni portale di Azure

L'accesso alle metriche nel portale di Azure è gestito dal [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Usare il portale di Azure per aggiungere utenti al IoT Central applicazione/gruppo di risorse/sottoscrizione per concedere loro l'accesso. È necessario aggiungere un utente nel portale anche se è già stato aggiunto all'applicazione IoT Central. Usare i [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md) per il controllo degli accessi con granularità fine.

## <a name="iot-central-metrics"></a>Metriche di IoT Central

La tabella seguente descrive le metriche attualmente disponibili per IoT Central:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione | Descrizione |
|--------|---------------------|------|------------------|-------------|
| connectedDeviceCount         | Totale dispositivi connessi                              | Conteggio  | Totale             | Numero di dispositivi connessi a IoT Central                               |
| C2D. Property. Read. Success    | Letture della proprietà del dispositivo riuscite da IoT Central    | Conteggio  | Totale             | Numero di tutte le letture di proprietà riuscite avviate da IoT Central    |
| C2D. Property. Read. Failure    | Letture della proprietà del dispositivo non riuscite da IoT Central        | Conteggio  | Totale             | Numero di tutte le letture di proprietà non riuscite avviate da IoT Central        |
| D2C. Property. Read. Success    | Letture della proprietà del dispositivo riuscite dai dispositivi        | Conteggio  | Totale             | Numero di tutte le letture di proprietà riuscite avviate dai dispositivi        |
| D2C. Property. Read. Failure    | Letture della proprietà del dispositivo non riuscite dai dispositivi            | Conteggio  | Totale             | Numero di tutte le letture di proprietà non riuscite avviate dai dispositivi            |
| C2D. Property. Update. Success  | Aggiornamenti della proprietà del dispositivo riusciti da IoT Central  | Conteggio  | Totale             | Numero di tutti gli aggiornamenti di proprietà riuscite avviati dal IoT Central  |
| C2D. Property. Update. Failure  | Aggiornamenti della proprietà del dispositivo non riusciti da IoT Central      | Conteggio  | Totale             | Numero di tutti gli aggiornamenti di proprietà non riuscite avviati dalla IoT Central      |
| D2C. Property. Update. Success  | Aggiornamenti della proprietà del dispositivo riusciti dai dispositivi      | Conteggio  | Totale             | Numero di tutti gli aggiornamenti della proprietà riusciti avviati dai dispositivi      |
| D2C. Property. Update. Failure  | Aggiornamenti della proprietà del dispositivo non riusciti dai dispositivi          | Conteggio  | Totale             | Numero di tutti gli aggiornamenti delle proprietà non riuscite avviati dai dispositivi          |

### <a name="metrics-and-invoices"></a>Metriche e fatture

Le metriche possono essere diverse dai numeri visualizzati nella fattura di Azure IoT Central. Questa situazione si verifica per diversi motivi, ad esempio:

- IoT Central [piani tariffari standard](https://azure.microsoft.com/pricing/details/iot-central/) includono due dispositivi e variano le quote dei messaggi gratuitamente. Mentre gli elementi gratuiti sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- IoT Central genera automaticamente un ID del dispositivo di test per ogni modello di dispositivo nell'applicazione. Questo ID dispositivo è visibile nella pagina **Gestisci dispositivo di test** per un modello di dispositivo. I generatori di soluzioni possono scegliere di [convalidare i modelli di dispositivo](./overview-iot-central.md#create-device-templates) prima di pubblicarli generando codice che usa questi ID del dispositivo di test. Anche se questi dispositivi sono esclusi dalla fatturazione, vengono comunque conteggiati nelle metriche.

- Sebbene le metriche possano mostrare un subset di comunicazione da dispositivo a cloud, tutte le comunicazioni tra il dispositivo e il cloud vengono [conteggiate come un messaggio per la fatturazione](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare i modelli di applicazione, il passaggio successivo suggerito consiste nell'apprendere come [gestire IOT Central dalla portale di Azure](howto-manage-iot-central-from-portal.md)
