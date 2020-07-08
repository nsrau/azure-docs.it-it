---
title: Eseguire il provisioning e gestire un ambiente di anteprima-Azure Time Series | Microsoft Docs
description: Informazioni su come eseguire il provisioning e gestire un ambiente di Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77087224"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights

Questo articolo illustra come creare e gestire un ambiente Anteprima di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Gli ambienti di Azure Time Series Insights Preview sono ambienti *con pagamento in base al* consumo (PAYG).

Quando si esegue il provisioning di un ambiente Azure Time Series Insights anteprima, si creano le risorse di Azure seguenti:

* Un ambiente Anteprima di Azure Time Series Insights  
* Un account di archiviazione di Azure per utilizzo generico v1
* Archivio caldo facoltativo per query più veloci e illimitate

> [!TIP]
> * Vedere [come pianificare l'ambiente](./time-series-insights-update-plan.md).
> * Leggere le informazioni su come [aggiungere un'origine Hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) o su come [aggiungere un'origine Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet.

Si apprenderà come:

1. **(Facoltativo)** Associare ogni ambiente Azure Time Series Insights anteprima a un'origine evento. Si forniranno anche una proprietà ID timestamp e un gruppo di consumer univoco per garantire che l'ambiente possa accedere agli eventi appropriati.

   > [!NOTE]
   > Il passaggio precedente è facoltativo quando si esegue il provisioning di un ambiente. Se si ignora questo passaggio, è necessario aggiungere un'origine evento all'ambiente in un secondo momento, in modo che sia possibile accedere ai dati nell'ambiente.

1. Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente per soddisfare i requisiti aziendali.

## <a name="create-the-environment"></a>Creare l'ambiente

Per creare un ambiente di anteprima Azure Time Series Insights:

1. Selezionare **PAYG** come **livello**. Specificare un nome per l'ambiente e scegliere il gruppo di sottoscrizioni e il gruppo di risorse da usare. Selezionare quindi un percorso supportato per ospitare l'ambiente.

   [![Creare un'istanza di Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Immettere un ID serie temporale.

    > [!NOTE]
    > * L'ID della serie temporale fa *distinzione tra maiuscole* e minuscole e non è *modificabile*. Non può essere modificato dopo averlo impostato.
    > * Gli ID delle serie temporali possono contenere fino a *tre* chiavi.
    > * Scopri di più su [come scegliere un ID Time Series](time-series-insights-update-how-to-id.md)

1. Creare un account di archiviazione di Azure selezionando un nome dell'account di archiviazione e designando una scelta di replica. In questo modo verrà automaticamente creato un account di archiviazione di Azure per utilizzo generico v1 L'account viene creato nella stessa area dell'ambiente di Azure Time Series Insights anteprima selezionato in precedenza.

    [![Configurazione dell'archiviazione a freddo](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Facoltativo)** Abilitare l'archivio a caldo per l'ambiente se si desiderano query più veloci e illimitate sui dati più recenti nell'ambiente in uso. È anche possibile creare o eliminare un archivio a caldo tramite l'opzione di **Configurazione archiviazione** nel riquadro di spostamento a sinistra, dopo aver creato un ambiente di Time Series Insights anteprima.

    [![Configurazione dell'archiviazione a caldo](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Facoltativo)** È ora possibile aggiungere un'origine evento. È anche possibile attendere fino al termine del provisioning dell'istanza.

   * Time Series Insights supporta l' [Hub Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e gli hub [eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni di origine eventi. Sebbene sia possibile aggiungere una sola origine evento quando si crea l'ambiente, è possibile aggiungere un'altra origine evento in un secondo momento. 
   
     È possibile selezionare un gruppo di consumer esistente o creare un nuovo gruppo di consumer quando si aggiunge l'origine evento. È consigliabile creare un gruppo di consumer univoco per assicurarsi che tutti gli eventi siano visibili all'ambiente di Azure Time Series Insights Preview.

   * Scegliere la proprietà timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights utilizza l'ora accodata dal messaggio per ogni origine evento.

     > [!TIP]
     > Il tempo di Accodamento dei messaggi potrebbe non essere l'impostazione migliore configurata per l'utilizzo in scenari di eventi batch o scenari di caricamento cronologico dei dati. In questi casi, assicurarsi di verificare la decisione di utilizzare o meno una proprietà timestamp.

     [![Scheda configurazione origine evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Verificare che sia stato effettuato il provisioning e la configurazione dell'ambiente nel modo desiderato.

    [![Verifica + scheda Crea](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Anteprima di Azure Time Series Insights tramite il portale di Azure. Esistono alcune differenze principali tra un ambiente PAYG Azure Time Series Insights anteprima e gli ambienti S1 o S2 disponibili a livello generale da tenere presente quando si gestisce l'ambiente tramite la portale di Azure:

* Il pannello **Panoramica** di portale di Azure Preview presenta le modifiche seguenti:

  * La capacità viene rimossa perché non è applicabile agli ambienti PAYG.
  * Viene aggiunta la proprietà **ID della serie temporale** . che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indirizza allo [strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello di **configurazione** della portale di Azure viene rimosso in Azure Time Series Insights anteprima perché gli ambienti PAYG non sono configurabili. Tuttavia, è possibile usare la **configurazione di archiviazione** per configurare l'archivio warm appena introdotto.

* Il pannello **dei dati di riferimento** portale di Azure viene rimosso in Azure Time Series Insights anteprima perché i dati di riferimento non fanno parte degli ambienti PAYG.

[![Ambiente Anteprima di Time Series Insights nel portale di Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Time Series Insights ambienti disponibili a livello generale e sugli ambienti di anteprima, leggere [pianificare l'ambiente](./time-series-insights-update-plan.md).

- Informazioni su come [aggiungere un'origine di hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurare un' [origine Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet delle cose.
