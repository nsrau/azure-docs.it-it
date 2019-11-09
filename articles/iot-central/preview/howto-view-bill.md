---
title: Gestire la fattura e convertire la versione di valutazione per il pagamento in base al consumo in Azure IoT Central applicazione | Microsoft Docs
description: In qualità di amministratore, Scopri come gestire la fatturazione e il Covert dalla versione di valutazione per il pagamento in base al consumo nell'applicazione IoT Central di Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 11bcdaab34cf4d82b688a578ab2d9ee686ffe07e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894812"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gestire la fattura in un'applicazione IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un amministratore può gestire la fattura nell'applicazione IoT Central di Azure nella sezione amministrazione e come è possibile convertire la versione di valutazione in pagamento in base al consumo.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina **Fatturazione** nella sezione **Amministrazione** Verrà visualizzata una nuova scheda della pagina di fatturazione di Azure dove sarà possibile visualizzare le fatture per ogni applicazione Azure IoT Central.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Converti la versione di valutazione in pagamento in base al consumo

- Le applicazioni di tipo **Versione di prova** sono gratuite per sette giorni prima della scadenza. Possono essere convertite in applicazioni con pagamento in base al consumo in qualsiasi momento prima della scadenza.
- Le applicazioni con **pagamento in base** al consumo vengono addebitate per ogni dispositivo, con i primi cinque dispositivi gratuiti per ogni sottoscrizione.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Nella sezione fatturazione è possibile convertire le applicazioni di valutazione in con pagamento in base al consumo.

Per completare il processo self-service, seguire questa procedura:

1. Passare alla pagina **Fatturazione** nella sezione **Amministrazione**.

    ![Stato della versione di valutazione](media/howto-view-bill/freetrialbilling.png)

1. Selezionare **Converti in con pagamento in base al**consumo.

    ![Convertire la versione di valutazione](media/howto-view-bill/convert.png)

1. Selezionare l'istanza di Azure Active Directory appropriata e quindi la sottoscrizione di Azure da usare per l'applicazione con pagamento in base al consumo.

1. Dopo aver selezionato **Convert**, l'applicazione è ora un'applicazione con pagamento in base al consumo e si inizia a ricevere una fattura.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire la fattura in Azure IoT Central applicazione, il passaggio successivo suggerito consiste nell'apprendere come [personalizzare l'interfaccia utente dell'applicazione](howto-customize-ui.md) in Azure IOT Central.