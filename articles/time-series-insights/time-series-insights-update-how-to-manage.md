---
title: Effettuare il provisioning e la gestione di Anteprima di Azure Time Series | Microsoft Docs
description: Informazioni su come effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 47c96cb14f2e466d02d57f73fb2e66bc1f44a311
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989917"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights

Questo articolo illustra come creare e gestire un ambiente Anteprima di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Gli ambienti Anteprima di Azure Time Series Insights sono con pagamento in base al consumo.

Quando si esegue il provisioning di un ambiente Azure Time Series Insights anteprima, si creano le risorse di Azure seguenti:

* Un ambiente Anteprima di Azure Time Series Insights  
* Un account di archiviazione di Azure per utilizzo generico v1
* Archivio caldo facoltativo per query più veloci e illimitate
  
Vedere [come pianificare l'ambiente](./time-series-insights-update-plan.md).

Associare ogni ambiente Azure Time Series Insights anteprima a un'origine evento. Per altre informazioni, leggere [Aggiungere un'origine di un hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) e [Aggiungere un'origine di un hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). Durante questo passaggio si specificano un gruppo di consumer univoco e una proprietà ID timestamp. Questo assicura che l'ambiente abbia accesso agli eventi appropriati.

> [!NOTE]
> Il passaggio precedente è facoltativo nel flusso di lavoro di provisioning mentre si crea l'ambiente di anteprima della serie temporale. Tuttavia, è necessario alleghi un'origine evento all'ambiente in modo che i dati possano iniziare a fluire in tale ambiente.

Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente per soddisfare i requisiti aziendali.

## <a name="create-the-environment"></a>Creare l'ambiente

Per creare un ambiente di anteprima Azure Time Series Insights:

1. Nel menu **SKU** selezionare il pulsante **PAYG** . Specificare un nome per l'ambiente e scegliere il gruppo di sottoscrizioni e il gruppo di risorse da usare. Selezionare quindi un percorso supportato per ospitare l'ambiente.

   [![creare un'istanza di Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Immettere un ID serie temporale.

    >[!NOTE]
    > * L'ID serie temporale è con distinzione tra maiuscole/minuscole e non è modificabile. Non può essere modificato dopo averlo impostato.
    > * Gli ID serie temporale possono contenere un massimo di tre chiavi.
    > * Per altre informazioni sulla selezione di un ID serie temporale, leggere [Scegliere un ID serie temporale](./time-series-insights-update-how-to-id.md).

1. Creare un account di archiviazione di Azure selezionando un nome di account di archiviazione e designando un'opzione di replica. In questo modo verrà automaticamente creato un account di archiviazione di Azure per utilizzo generico v1 L'account viene creato nella stessa area dell'ambiente di Azure Time Series Insights anteprima selezionato in precedenza.

    [![creare un account di archiviazione di Azure per l'istanza](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Facoltativo)** Abilitare l'archivio a caldo per l'ambiente se si desiderano query più veloci e illimitate sui dati più recenti nell'ambiente in uso. È anche possibile creare o eliminare un archivio a caldo tramite l'opzione di **Configurazione archiviazione** nel riquadro di spostamento a sinistra, dopo aver creato un ambiente di Time Series Insights anteprima.

1. **(Facoltativo)** È ora possibile aggiungere un'origine evento. In alternativa, è possibile attendere il completamento del provisioning dell'istanza.

   * Time Series Insights supporta l' [Hub Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e gli hub [eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni di origine eventi. Sebbene sia possibile aggiungere una sola origine evento quando si crea l'ambiente, è possibile aggiungere un'altra origine evento in un secondo momento. È possibile selezionare un gruppo di consumer esistente o creare un nuovo gruppo di consumer quando si aggiunge l'origine evento. È consigliabile creare un gruppo di consumer univoco per assicurarsi che tutti gli eventi siano visibili all'ambiente di Azure Time Series Insights Preview.

   * Scegliere la proprietà timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights utilizza l'ora accodata dal messaggio per ogni origine evento.

     > [!TIP]
     > Il tempo di Accodamento dei messaggi potrebbe non essere l'impostazione migliore configurata per l'utilizzo in scenari di eventi batch o scenari di caricamento cronologico dei dati. In questi casi, assicurarsi di verificare la decisione di utilizzare o meno una proprietà timestamp.

     [scheda origine evento![](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Verificare che sia stato effettuato il provisioning dell'ambiente con le impostazioni desiderate.

    [![rivedere + scheda Crea](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Anteprima di Azure Time Series Insights tramite il portale di Azure. Quando si gestiscono i portale di Azure, vengono visualizzate alcune differenze principali tra un ambiente di anteprima Azure Time Series Insights con pagamento in base al consumo e gli ambienti S1 o S2 disponibili a livello generale:

* Il pannello **Panoramica** del portale di Azure rimane invariato in Azure Time Series Insights, con le seguenti eccezioni:
  * La capacità viene rimossa perché non è applicabile agli ambienti con pagamento in base al consumo.
  * Viene aggiunta la proprietà ID della serie temporale. che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indirizza allo [strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello di **configurazione** della portale di Azure viene rimosso in Azure Time Series Insights anteprima perché gli ambienti PAYG non sono configurabili. Tuttavia, è possibile usare la **configurazione di archiviazione** per configurare l'archivio warm appena introdotto.

* Il pannello **dati di riferimento** portale di Azure viene rimosso in Azure Time Series Insights anteprima perché i dati di riferimento non fanno parte di ambienti con pagamento in base al consumo.

[![Time Series Insights ambiente di anteprima nella portale di Azure](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Time Series Insights ambienti disponibili a livello generale e sugli ambienti di anteprima, leggere [pianificare l'ambiente](./time-series-insights-update-plan.md).

- Informazioni su come [aggiungere un'origine di hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurare un' [origine Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet delle cose.
