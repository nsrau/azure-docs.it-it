---
title: Gestire la fattura e la conversione dal piano tariffario gratuito nell'applicazione IoT Central di Azure | Microsoft Docs
description: In qualità di amministratore, Scopri come gestire la fattura e passare dal piano tariffario gratuito a un piano tariffario standard nell'applicazione IoT Central di Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e45109842a081b437c20de81321bf70f909efa2a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982355"
---
# <a name="view-your-bill-in-iot-central-application"></a>Visualizza la fattura nell'applicazione IoT Central

Questo articolo descrive come un amministratore può gestire la fattura nell'applicazione IoT Central di Azure nella sezione amministrazione. Si apprenderà come spostare l'applicazione dal piano tariffario gratuito a un piano tariffario standard, nonché come aggiornare o effettuare il downgrade del piano tariffario.

Per accedere e usare la sezione **Amministrazione** , è necessario avere il ruolo di *amministratore* o avere un *ruolo utente personalizzato* che consente di visualizzare la fatturazione per un'applicazione IoT Central di Azure. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="move-from-free-to-standard-pricing-plan"></a>Passare dal piano tariffario gratuito a quello standard

- Le applicazioni che usano il piano tariffario gratuito sono gratuite per sette giorni prima della scadenza. Per evitare la perdita di dati, è possibile spostarli in un piano tariffario standard in qualsiasi momento prima della scadenza.

Nella sezione relativa ai prezzi è possibile spostare l'applicazione dal piano tariffario gratuito a quello standard.

Per completare il processo self-service, seguire questa procedura:

1. Passare alla pagina dei **prezzi** nella sezione **Amministrazione** .

    ![Stato della versione di valutazione](media/howto-administer/freetrialbilling.png)

1. Selezionare **Converti in piano a pagamento**.

    ![Convertire la versione di valutazione](media/howto-administer/convert.png)

1. Selezionare il Azure Active Directory appropriato, quindi la sottoscrizione di Azure da usare per l'applicazione che usa un piano a pagamento.

1. Dopo aver selezionato **Convert**, l'applicazione ora usa un piano a pagamento e si inizia a fatturare.

> [!Note]
> Per impostazione predefinita, viene convertito in un piano tariffario *standard 2* .

## <a name="how-to-change-your-application-pricing-plan"></a>Come modificare il piano tariffario dell'applicazione

- Le applicazioni che usano un piano tariffario standard vengono addebitate per ogni dispositivo, con i primi due dispositivi gratuiti, per ogni applicazione.

Nella sezione relativa ai prezzi è possibile aggiornare o effettuare il downgrade del piano tariffario di Azure Internet in qualsiasi momento.

1. Passare alla pagina dei **prezzi** nella sezione **Amministrazione** .

    ![Stato della versione di valutazione](media/howto-administer/pricing.png)

1. Selezionare il **piano** e fare clic su **Save (Salva** ) per eseguire l'aggiornamento o il downgrade.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina dei **prezzi** nella sezione **Amministrazione** . Viene visualizzata la pagina dei prezzi di Azure in una nuova scheda in cui è possibile visualizzare la fattura per ogni applicazione Azure IoT Central.


## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come visualizzare la fattura nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [personalizzare l'interfaccia utente dell'applicazione](howto-customize-ui.md) in Azure IOT Central.