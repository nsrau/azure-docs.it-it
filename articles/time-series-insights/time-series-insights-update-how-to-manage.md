---
title: Effettuare il provisioning e la gestione di Anteprima di Azure Time Series | Microsoft Docs
description: Informazioni su come effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: a73be313daa3b45cabc2adb07bb3d2592c7d307e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725466"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights

Questo articolo illustra come creare e gestire un ambiente Anteprima di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Gli ambienti Anteprima di Azure Time Series Insights sono con pagamento in base al consumo.

Quando si effettua il provisioning di un ambiente Anteprima di Azure Time Series Insights, vengono create due risorse di Azure:

* Un ambiente Anteprima di Azure Time Series Insights  
* Un account di archiviazione di Azure per utilizzo generico v1
  
Vedere [come pianificare l'ambiente](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Per l'anteprima, assicurarsi che si usa un servizio di archiviazione Azure utilizzo generico v1 (GPv1) account.  Nel prossimo futuro verrà aggiunto il supporto per utilizzo generico v2 e versioni successive.  

È possibile associare facoltativamente ogni ambiente Anteprima di Azure Time Series Insights a un'origine evento. Per altre informazioni, leggere [Aggiungere un'origine di un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Aggiungere un'origine di un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Durante questo passaggio si specificano un gruppo di consumer univoco e una proprietà ID timestamp. Questo assicura che l'ambiente abbia accesso agli eventi appropriati.

Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente per soddisfare i requisiti aziendali.

## <a name="create-the-environment"></a>Creare l'ambiente

I passaggi seguenti descrivono come creare un ambiente Anteprima di Azure Time Series Insights:

1. Selezionare il pulsante **PAYG** (Con pagamento in base al consumo) sotto il menu **SKU**. Specificare un nome di ambiente e designare il gruppo di sottoscrizioni e il gruppo di risorse da usare, quindi selezionare una località supportata in cui ospitare l'ambiente.

   ![Creare un'istanza di Azure Time Series Insights.][1]

1. Immettere un ID serie temporale.

    >[!NOTE]
    > * L'ID serie temporale è con distinzione tra maiuscole/minuscole e non è modificabile. Non può essere modificato dopo averlo impostato.
    > * Gli ID serie temporale possono contenere un massimo di tre chiavi.
    > * Per altre informazioni sulla selezione di un ID serie temporale, leggere [Scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md).

1. Creare un account di archiviazione di Azure selezionando un nome di account di archiviazione e designando un'opzione di replica. In questo modo verrà automaticamente creato un account di archiviazione di Azure per utilizzo generico v1 nella stessa area dell'ambiente Anteprima di Azure Time Series Insights selezionata in precedenza.

    ![Creare un account di archiviazione di Azure per l'istanza][5]

1. Facoltativamente, è possibile aggiungere un'origine evento.

   * Time Series Insights supporta [Hub IoT di Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e [Hub eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni. Anche se è possibile aggiungere una sola origine evento in fase di creazione dell'ambiente, sarà possibile aggiungerne un'altra in un secondo momento. È consigliabile creare un gruppo di consumer univoco per assicurarsi che tutti gli eventi siano visibili all'istanza di Anteprima di Azure Time Series Insights. È possibile selezionare un gruppo di consumer esistente o crearne uno nuovo quando si aggiunge l'origine evento.

   * È anche consigliabile scegliere la proprietà Timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights usa l'ora di accodamento del messaggio per ogni origine evento.

     > [!TIP]
     > L'ora di accodamento del messaggio potrebbe non essere la migliore impostazione configurata da usare negli scenari di caricamento di dati cronologici o di eventi di batch. Valutare con attenzione la decisione di usare o meno una proprietà Timestamp in tali casi.

     ![Scheda Origine evento][2]

1. Verificare che il provisioning dell'ambiente sia stato effettuato con le impostazioni desiderate.

    ![Scheda Rivedi e crea][3]

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Anteprima di Azure Time Series Insights tramite il portale di Azure. Ecco le principali differenze nella gestione di un ambiente Anteprima di Azure Time Series Insights con pagamento in base al consumo rispetto a un ambiente S1 o S2 tramite il portale di Azure:

* Il pannello **Panoramica** del portale di Azure rimane invariato in Azure Time Series Insights, con le seguenti eccezioni:
  * La capacità viene rimossa, in quanto questo concetto non è pertinente negli ambienti con pagamento in base al consumo.
  * È stata aggiunta la proprietà ID serie temporale, che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indirizza allo [strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello **Configura** del portale di Azure è stato rimosso in Anteprima di Azure Time Series Insights, in quanto gli ambienti con pagamento in base al consumo non sono configurabili.

* Il pannello **Dati di riferimento** del portale di Azure è stato rimosso in Anteprima di Azure Time Series Insights, in quanto i dati di riferimento non sono un componente degli ambienti con pagamento in base al consumo.

![Ambiente Anteprima di Time Series Insights nel portale di Azure][4]

## <a name="next-steps"></a>Passaggi successivi

- Leggere [Pianificare l'ambiente](./time-series-insights-update-plan.md).

- Informazioni su come [aggiungere un'origine di hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurare [un'origine di hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.PNG
[2]: media/v2-update-manage/manage_two.PNG
[3]: media/v2-update-manage/manage_three.PNG
[4]: media/v2-update-manage/manage_four.PNG
[5]: media/v2-update-manage/manage_five.PNG
