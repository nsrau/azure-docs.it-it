---
title: Eseguire il provisioning e la gestione di un ambiente di anteprima - Serie temporale di Azure. Documenti Microsoft
description: Informazioni su come eseguire il provisioning e gestire un ambiente Azure Time Series Insights Preview.Learn how to provision and manage an Azure Time Series Insights Preview environment.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087224"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Effettuare il provisioning e la gestione di Anteprima di Azure Time Series Insights

Questo articolo illustra come creare e gestire un ambiente Anteprima di Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Gli ambienti Azure Time Series Insights Preview sono ambienti *PAYG (PayG) con pagamento in base* al tempo reale.

Quando si esegue il provisioning di un ambiente Azure Time Series Insights Preview, si creano queste risorse di Azure:When you provision an Azure Time Series Insights Preview environment, you create these Azure resources:

* Un ambiente Anteprima di Azure Time Series Insights  
* Un account di archiviazione di Azure per utilizzo generico v1
* Un negozio caldo opzionale per query più veloci e illimitate

> [!TIP]
> * Vedere [come pianificare l'ambiente](./time-series-insights-update-plan.md).
> * Informazioni su come [aggiungere un'origine hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) o su Aggiungere [un'origine hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Si apprenderà come:

1. **(Facoltativo)** Associare ogni ambiente azure Time Series Insights Preview a un'origine eventi. È inoltre possibile fornire una proprietà Timestamp ID e un gruppo di consumer univoco per garantire che l'ambiente abbia accesso agli eventi appropriati.

   > [!NOTE]
   > Il passaggio precedente è facoltativo quando si esegue il provisioning di un ambiente. Se si ignora questo passaggio, è necessario collegare un'origine eventi all'ambiente in un secondo momento in modo che sia possibile accedere ai dati nell'ambiente.

1. Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente per soddisfare i requisiti aziendali.

## <a name="create-the-environment"></a>Creare l'ambiente

Per creare un ambiente Azure Time Series Insights Preview:To create an Azure Time Series Insights Preview environment:

1. Selezionare **PAYG** come **livello**. Specificare un nome di ambiente e scegliere il gruppo di sottoscrizioni e il gruppo di risorse da usare. Selezionare quindi un percorso supportato per ospitare l'ambiente.

   [![Creare un'istanza di Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Immettere un ID serie temporale.

    > [!NOTE]
    > * L'ID della serie temporale fa *distinzione tra maiuscole e minuscole* e *non è modificabile.* Non può essere modificato dopo averlo impostato.
    > * Gli ID serie temporali possono essere fino a *tre* chiavi.
    > * Ulteriori informazioni su [come scegliere un ID serie temporali](time-series-insights-update-how-to-id.md)

1. Creare un account di archiviazione di Azure selezionando un nome di account di archiviazione e designando una scelta di replica. In questo modo verrà automaticamente creato un account di archiviazione di Azure per utilizzo generico v1 L'account viene creato nella stessa area dell'ambiente Azure Time Series Insights Preview selezionato in precedenza.

    [![Configurazione dell'archiviazione a freddo](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Facoltativo)** Abilitare l'archivio a caldo per l'ambiente se si desidera che le query più veloci e illimitate sui dati più recenti dell'ambiente. È inoltre possibile creare o eliminare un archivio a caldo tramite l'opzione **Configurazione archiviazione** nel riquadro di spostamento sinistro, dopo aver creato un ambiente Time Series Insights Preview.

    [![Configurazione dell'archiviazione a caldo](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Facoltativo)** È ora possibile aggiungere un'origine eventi. È inoltre possibile attendere fino a dopo il provisioning dell'istanza.

   * Time Series Insights supporta [Hub IoT di Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e Hub eventi di [Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni dell'origine evento. Sebbene sia possibile aggiungere una sola origine eventi quando si crea l'ambiente, è possibile aggiungere un'altra origine eventi in un secondo momento. 
   
     È possibile selezionare un gruppo di consumer esistente o crearne uno nuovo quando si aggiunge l'origine eventi. È consigliabile creare un gruppo di consumer univoco per garantire che tutti gli eventi siano visibili all'ambiente Azure Time Series Insights Preview.It's best to create a unique consumer group to ensure that all events are visible to your Azure Time Series Insights Preview environment.

   * Scegliere la proprietà Timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights usa il tempo di accodamento dei messaggi per ogni origine evento.

     > [!TIP]
     > Il tempo di accodamento dei messaggi potrebbe non essere l'impostazione configurata migliore da utilizzare in scenari di eventi batch o scenari di caricamento di dati cronologici. In questi casi, assicurati di verificare la tua decisione di utilizzare o meno una proprietà Timestamp.

     [![Scheda Configurazione origine evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Verificare che il provisioning e la configurazione dell'ambiente siano state eseguite nel modo desiderato.

    [![Scheda Rivedi e crea](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Anteprima di Azure Time Series Insights tramite il portale di Azure. Esistono alcune differenze fondamentali tra un ambiente di anteprima di Azure Time Series Insights PAYG e gli ambienti S1 o S2 disponibili in generale da tenere presenti quando si gestisce l'ambiente tramite il portale di Azure:There there a few key differences between a PAYG Azure Time Series Insights Preview environment and the generally available S1 or S2 environments to ter in mind when you manage your environment through the Azure portal:

* Il pannello **Panoramica** anteprima del portale di Azure include le modifiche seguenti:The Azure portal Preview Overview blade has the following changes:

  * La capacità viene rimossa perché non si applica agli ambienti PAYG.
  * Viene aggiunta la proprietà **ID serie temporali.** che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indirizza allo [strumento di esplorazione di Anteprima di Azure Time Series Insights](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello **Configura** del portale di Azure viene rimosso in Azure Time Series Insights Preview perché gli ambienti PAYG non sono configurabili. Tuttavia, è possibile utilizzare **Configurazione archiviazione** per configurare l'archivio caldo appena introdotto.

* Il pannello dati di **riferimento** del portale di Azure viene rimosso in Azure Time Series Insights Preview perché i dati di riferimento non fanno parte degli ambienti PAYG.

[![Ambiente Anteprima di Time Series Insights nel portale di Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sugli ambienti in genere disponibili e sugli ambienti di anteprima di Time Series Insights, vedere [Pianificare l'ambiente.](./time-series-insights-update-plan.md)

- Informazioni su come [aggiungere un'origine dell'hub eventi.](./time-series-insights-how-to-add-an-event-source-eventhub.md)

- Configurare [un'origine hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
