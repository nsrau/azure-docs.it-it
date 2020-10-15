---
title: Gestire la fattura e la conversione dal piano tariffario gratuito nell'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di amministratore, Scopri come gestire la fattura e passare dal piano tariffario gratuito a un piano tariffario standard nell'applicazione IoT Central di Azure
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 515f5f665e947ff9594cce6dbbaea9b5e0c50ebf
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999712"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gestire la fattura in un'applicazione IoT Central

Questo articolo descrive come un amministratore può gestire la fatturazione di Azure IoT Central. È possibile spostare l'applicazione dal piano tariffario gratuito a un piano tariffario standard, nonché aggiornare o effettuare il downgrade del piano tariffario.

Per accedere alla sezione **Amministrazione** , è necessario avere il ruolo di *amministratore* o disporre di un *ruolo utente personalizzato* che consenta di visualizzare la fatturazione. Se si crea un'applicazione IoT Central di Azure, l'utente viene automaticamente assegnato al ruolo di **amministratore** .

## <a name="move-from-free-to-standard-pricing-plan"></a>Passare dal piano tariffario gratuito a quello standard

- Le applicazioni che usano il piano tariffario gratuito sono gratuite per sette giorni prima della scadenza. Per evitare la perdita di dati, è possibile spostarli in un piano tariffario standard in qualsiasi momento prima della scadenza.
- Le applicazioni che usano un piano tariffario standard vengono addebitate per ogni dispositivo, con i primi due dispositivi gratuiti, per ogni applicazione.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Nella sezione relativa ai prezzi è possibile spostare l'applicazione dal piano tariffario gratuito a quello standard.

Per completare il processo self-service, seguire questa procedura:

1. Passare alla pagina dei **prezzi** nella sezione **Amministrazione** .

    ![Stato della versione di valutazione](media/howto-view-bill/freetrialbilling.png)

1. Selezionare **Converti in piano a pagamento**.

    ![Convertire la versione di valutazione](media/howto-view-bill/convert.png)

1. Selezionare il Azure Active Directory appropriato, quindi la sottoscrizione di Azure da usare per l'applicazione che usa un piano a pagamento.

1. Dopo aver selezionato **Convert**, l'applicazione ora usa un piano a pagamento e si inizia a fatturare.

> [!Note]
> Per impostazione predefinita, viene convertito in un piano tariffario *standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Come modificare il piano tariffario dell'applicazione

Le applicazioni che usano un piano tariffario standard vengono addebitate per ogni dispositivo, con i primi due dispositivi gratuiti, per ogni applicazione.

Nella sezione relativa ai prezzi è possibile aggiornare o effettuare il downgrade del piano tariffario di Azure Internet in qualsiasi momento.

1. Passare alla pagina dei **prezzi** nella sezione **Amministrazione** .

    ![Aggiorna piano prcing](media/howto-view-bill/pricing.png)

1. Selezionare il **piano** e quindi fare clic su **Salva** per eseguire l'aggiornamento o il downgrade.

## <a name="view-your-bill"></a>Visualizzare la fattura

1. Selezionare il Azure Active Directory appropriato, quindi la sottoscrizione di Azure da usare per l'applicazione che usa un piano a pagamento.

1. Dopo aver selezionato **Convert**, l'applicazione ora usa un piano a pagamento e si inizia a fatturare.

> [!Note]
> Per impostazione predefinita, viene convertito in un piano tariffario *standard 2* .

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire la fattura in Azure IoT Central applicazione, il passaggio successivo suggerito consiste nell'apprendere come [personalizzare l'interfaccia utente dell'applicazione](howto-customize-ui.md) in Azure IOT Central.