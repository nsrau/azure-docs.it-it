---
title: Visualizzare la fattura e convertire la versione di valutazione per il pagamento in base al consumo in Azure IoT Central applicazione | Microsoft Docs
description: In qualità di amministratore, Scopri come visualizzare la fattura e il Covert dalla versione di valutazione a pagamento in base all'uso nell'applicazione IoT Central di Azure
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d27288718b7f224c78c9cf1f15d05198f628fd15
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879643"
---
# <a name="view-your-bill-in-an-iot-central-application"></a>Visualizza la fattura in un'applicazione IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Questo articolo descrive come un amministratore può visualizzare la fattura nell'applicazione IoT Central di Azure nella sezione relativa all'amministrazione e come è possibile convertire la versione di valutazione in con pagamento in base al consumo.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina **Fatturazione** nella sezione **Amministrazione** Verrà visualizzata una nuova scheda della pagina di fatturazione di Azure dove sarà possibile visualizzare le fatture per ogni applicazione Azure IoT Central.

## <a name="convert-your-trial-to-pay-as-you-go"></a>Converti la versione di valutazione in pagamento in base al consumo

- Le applicazioni di **valutazione** sono gratuite per sette giorni prima della scadenza. Possono essere convertite in applicazioni con pagamento in base al consumo in qualsiasi momento prima della scadenza.
- Le applicazioni con **pagamento in base** al consumo vengono addebitate per ogni dispositivo, con i primi cinque dispositivi gratuiti per ogni sottoscrizione.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Nella sezione fatturazione è possibile convertire le applicazioni di valutazione in con pagamento in base al consumo.

Per completare il processo self-service, seguire questa procedura:

1. Passare alla pagina **Fatturazione** nella sezione **Amministrazione**.

    ![Stato della versione di valutazione](media/howto-view-bill-pnp/freetrialbilling.png)

1. Selezionare **Converti in con pagamento in base al**consumo.

    ![Convertire la versione di valutazione](media/howto-view-bill-pnp/convert.png)

1. Selezionare l'istanza di Azure Active Directory appropriata e quindi la sottoscrizione di Azure da usare per l'applicazione con pagamento in base al consumo.

1. Dopo aver selezionato **Convert**, l'applicazione è ora un'applicazione con pagamento in base al consumo e si inizia a ricevere una fattura.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come visualizzare la fattura nell'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [personalizzare l'interfaccia utente dell'applicazione](howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in Azure IOT Central.