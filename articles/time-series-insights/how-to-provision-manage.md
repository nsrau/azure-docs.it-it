---
title: Effettuare il provisioning e gestire un ambiente di generazione 2-Azure Time Series | Microsoft Docs
description: Informazioni su come eseguire il provisioning e gestire un ambiente Azure Time Series Insights generazione 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: d067d4a7fff385deea946ffa5475e1eb83548a50
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098765"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Effettuare il provisioning e gestire Azure Time Series Insights Gen2

Questo articolo descrive come creare e gestire un ambiente Gen2 Azure Time Series Insights usando il [portale di Azure](https://portal.azure.com/).

## <a name="overview"></a>Panoramica

Quando si esegue il provisioning di un ambiente Azure Time Series Insights Gen2, è necessario creare le risorse di Azure seguenti:

* Un ambiente Azure Time Series Insights Gen2 che segue il modello di determinazione prezzi con pagamento in base al consumo
* Un account di archiviazione Azure
* Archivio caldo facoltativo per query più veloci e illimitate

> [!TIP]
> * Vedere [come pianificare l'ambiente](./time-series-insights-update-plan.md).
> * Leggere le informazioni su come [aggiungere un'origine Hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md) o su come [aggiungere un'origine Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet.

Si apprenderà come:

1.  Associare ogni ambiente Azure Time Series Insights generazione 2 a un'origine evento. Si forniranno anche una proprietà ID timestamp e un gruppo di consumer univoco per garantire che l'ambiente possa accedere agli eventi appropriati.

1. Al termine del provisioning, è possibile modificare i criteri di accesso e altri attributi dell'ambiente in base alle esigenze aziendali.

   > [!NOTE]
   > Il primo passaggio è facoltativo quando si esegue il provisioning di un ambiente. Se si ignora questo passaggio, è necessario aggiungere un'origine evento all'ambiente in un secondo momento, in modo che i dati possano essere avviati nel proprio ambiente ed è possibile accedervi tramite query.

## <a name="create-the-environment"></a>Creare l'ambiente

Per creare un ambiente Azure Time Series Insights generazione 2:
1. Creare una risorsa Azure Time Series Insights in *Internet delle cose* [portale di Azure](https://portal.azure.com/).

1. Selezionare **Gen2 (L1)** come **livello**. Specificare un nome per l'ambiente e scegliere il gruppo di sottoscrizioni e il gruppo di risorse da usare. Selezionare quindi un percorso supportato per ospitare l'ambiente.

   [![Creare un'istanza di Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Immettere un ID serie temporale.

    > [!NOTE]
    > * L'ID della serie temporale fa *distinzione tra maiuscole* e minuscole e non è *modificabile*. Non può essere modificato dopo averlo impostato.
    > * Gli ID delle serie temporali possono contenere fino a *tre* chiavi. Considerarlo come chiave primaria in un database, che rappresenta in modo univoco ogni sensore di dispositivo che invia i dati all'ambiente. Potrebbe trattarsi di una proprietà o di una combinazione di fino a tre proprietà.
    > * Scopri di più su [come scegliere un ID Time Series](time-series-insights-update-how-to-id.md)

1. Creare un account di archiviazione di Azure selezionando il nome dell'account di archiviazione, il tipo di account e designando una scelta di [replica](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal) . In questo modo viene creato automaticamente un account di archiviazione di Azure. Per impostazione predefinita, verrà creato un account per [utilizzo generico V2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) . L'account viene creato nella stessa area dell'ambiente Azure Time Series Insights Gen2 selezionato in precedenza. In alternativa, è anche possibile usare l'archiviazione personalizzata (BYOS) tramite il [modello ARM](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) quando si crea un nuovo ambiente Gen2 di Azure Time Series. 

1. **(Facoltativo)** Abilitare l'archivio a caldo per l'ambiente se si desiderano query più veloci e illimitate sui dati più recenti nell'ambiente in uso. È anche possibile creare o eliminare un archivio a caldo tramite l'opzione di **Configurazione archiviazione** nel riquadro di spostamento a sinistra, dopo aver creato un ambiente di Azure Time Series Insights Gen2.

1. **(Facoltativo)** È ora possibile aggiungere un'origine evento. È anche possibile attendere fino al termine del provisioning dell'istanza.

   * Azure Time Series Insights supporta l' [Hub Azure](./time-series-insights-how-to-add-an-event-source-iothub.md) e gli hub [eventi di Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) come opzioni di origine eventi. Sebbene sia possibile aggiungere una sola origine evento quando si crea l'ambiente, è possibile aggiungere un'altra origine evento in un secondo momento. 
   
     È possibile selezionare un gruppo di consumer esistente o creare un nuovo gruppo di consumer quando si aggiunge l'origine evento. Si noti che l'origine evento richiede un gruppo di consumer univoco per l'ambiente in cui leggere i dati.

   * Scegliere la proprietà timestamp appropriata. Per impostazione predefinita, Azure Time Series Insights utilizza l'ora accodata dal messaggio per ogni origine evento.

     > [!TIP]
     > Il tempo di Accodamento dei messaggi potrebbe non essere l'impostazione migliore configurata per l'utilizzo in scenari di eventi batch o scenari di caricamento cronologico dei dati. In questi casi, assicurarsi di verificare la decisione di utilizzare o meno una proprietà timestamp.

     [![Scheda configurazione origine evento](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Verificare che sia stato effettuato il provisioning e la configurazione dell'ambiente nel modo desiderato.

    [![Verifica + scheda Crea](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Gestire l'ambiente

È possibile gestire l'ambiente Gen2 Azure Time Series Insights usando il portale di Azure. Esistono alcune differenze principali tra un ambiente Gen2 e gli ambienti Gen1 S1 o Gen1 S2 da tenere presente quando si gestisce l'ambiente tramite la portale di Azure:

* Il pannello **Panoramica** di portale di Azure Gen2 presenta le modifiche seguenti:

  * La capacità viene rimossa perché non è applicabile agli ambienti Gen2.
  * Viene aggiunta la proprietà **ID della serie temporale** . che determina come vengono partizionati i dati.
  * I set di dati di riferimento vengono rimossi.
  * L'URL visualizzato indica il [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md).
  * Viene elencato il nome dell'account di archiviazione di Azure.

* Il pannello **Configura** del portale di Azure viene rimosso perché le unità di scala non si applicano agli ambienti Azure Time Series Insights Gen2. Tuttavia, è possibile usare la **configurazione di archiviazione** per configurare l'archivio warm appena introdotto.

* Il pannello **dei dati di riferimento** portale di Azure è stato rimosso in Azure Time Series Insights Gen2 perché il concetto di dati di riferimento è stato sostituito dal [modello Time Series (TSM)](./time-series-insights-update-how-to-tsm.md).

[![Azure Time Series Insights ambiente Gen2 nell'portale di Azure](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Azure Time Series Insights ambienti disponibili a livello generale e sugli ambienti Gen2, vedere [pianificare l'ambiente](./time-series-insights-update-plan.md).

- Informazioni su come [aggiungere un'origine di hub eventi](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Configurare un' [origine Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)Internet delle cose.
