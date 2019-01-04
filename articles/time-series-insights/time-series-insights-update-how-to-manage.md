---
title: Gestione di Anteprima di Azure Time Series - Come effettuare il provisioning e la gestione di Anteprima di Azure Time Series. | Microsoft Docs
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
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273512"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Come effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights

Questo documento illustra come creare e gestire un ambiente Anteprima di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Di seguito è descritta una breve panoramica su come effettuare il provisioning di un ambiente Anteprima di Azure Time Series Insights:

* Gli ambienti Anteprima di Azure Time Series Insights sono **con pagamento in base al consumo**.
  * Come parte del processo di creazione è necessario fornire un ID serie temporale. Gli ID serie temporale possono contenere un massimo di tre chiavi. Per altre informazioni sulla selezione di un ID serie temporale, vedere [Come scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md).
  * Quando si effettua il provisioning di un ambiente Anteprima di Azure Time Series Insights, vengono create due risorse di Azure:

    * Un ambiente Anteprima di Azure Time Series Insights  
    * Un account di archiviazione di Azure per utilizzo generico V1
  
    Vedere [Come pianificare l'ambiente](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > Per i clienti che usano account V2, non abilitare le proprietà ad accesso sporadico/di archiviazione nell'account di archiviazione che verrà usato.

* Ogni ambiente Anteprima di Azure Time Series Insights può essere (facoltativamente) associato a un'origine evento. Per altre informazioni, vedere [Come aggiungere un'origine di un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Come aggiungere un'origine di un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
  * Durante questo passaggio verranno specificati un gruppo di consumer univoco e una proprietà ID timestamp. Ciò assicura che l'ambiente abbia accesso agli eventi appropriati.

* Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente per soddisfare i requisiti aziendali.

## <a name="new-environment-creation"></a>Creazione del nuovo ambiente

I passaggi seguenti descrivono come creare un ambiente Anteprima di Azure Time Series Insights:

1. Selezionare il pulsante **PAYG** (Con pagamento in base al consumo) sotto il menu **SKU**. Specificare un nome di ambiente e designare il gruppo di sottoscrizioni e il gruppo di risorse da usare, quindi selezionare una località supportata in cui ospitare l'ambiente.

1. Immettere un ID serie temporale

    >[!NOTE]
    > * L'ID serie temporale fa distinzione tra maiuscole e minuscole ed è immutabile, ovvero non può essere modificato dopo essere stato impostato.
    > * Gli ID serie temporale possono contenere un massimo di 3 chiavi.
    > * Per altre informazioni sulla selezione di un ID serie temporale, vedere [Come scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md).

1. Creare un account di archiviazione di Azure selezionando un nome di account di archiviazione di Azure e designando un'opzione di replica. In questo modo verrà automaticamente creato un account di archiviazione di Azure per utilizzo generico V1 nella stessa area dell'ambiente Anteprima di Azure Time Series Insights selezionata in precedenza.

    ![Creare un'istanza di Azure Time Series Insights.][1]

1. Facoltativamente, è possibile aggiungere un'origine evento.

   * Time Series Insights supporta [Hub IoT di Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e [Hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni. Anche se è possibile aggiungere una sola origine evento in fase di creazione dell'ambiente, sarà possibile aggiungerne un'altra in un secondo momento. È consigliabile creare un gruppo di consumer univoco per assicurarsi che tutti gli eventi siano visibili all'istanza di Anteprima di Azure Time Series Insights. È possibile selezionare un gruppo di consumer esistente o crearne uno nuovo quando si aggiunge l'origine evento.

   * È anche consigliabile designare la proprietà Timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights usa l'ora di accodamento del messaggio per ogni origine evento.

     > [!TIP]
     > L'ora di accodamento del messaggio potrebbe non essere la migliore impostazione configurata da usare negli scenari di caricamento di dati cronologici o di eventi di batch. Valutare con attenzione la decisione di usare o meno una proprietà Timestamp in tali casi.

    ![Aggiungere un'origine evento all'istanza.][2]

1. Rivedi e crea

    ![Aggiungere un'origine evento all'istanza.][3]

Verificare che il provisioning dell'ambiente sia stato effettuato con le impostazioni desiderate.

## <a name="management"></a>Gestione

È possibile gestire l'ambiente Anteprima di Azure Time Series Insights nel portale di Azure. Ecco le principali differenze nella gestione di un ambiente Anteprima di Azure Time Series Insights **con pagamento in base al consumo** rispetto a un ambiente S1 o S2 tramite il portale di Azure:

* Il pannello *Panoramica* del portale di Azure rimane invariato in Azure Time Series Insights, con le eccezioni seguenti:
  * La capacità viene rimossa perché questo concetto non è pertinente negli ambienti **con pagamento in base al consumo**.
  * È stata aggiunta la proprietà ID serie temporale, che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indirizza allo [strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello *Configura* del portale di Azure è stato rimosso in Anteprima di Azure Time Series Insights perché gli ambienti **con pagamento in base al consumo** non sono configurabili.

* Il pannello *Dati di riferimento* del portale di Azure è stato rimosso in Anteprima di Azure Time Series Insights perché i dati di riferimento non sono un componente degli ambienti **con pagamento in base al consumo**.

![Verificare l'istanza.][4]

## <a name="next-steps"></a>Passaggi successivi

Vedere [Come pianificare l'ambiente](./time-series-insights-update-plan.md).

Vedere [Come aggiungere un'origine di un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Vedere [Come aggiungere un'origine di un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
