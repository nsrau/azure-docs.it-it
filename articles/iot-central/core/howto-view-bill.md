---
title: Gestire la fattura e convertire dal piano tariffario gratuito nell'applicazione Azure IoT Central. Documenti Microsoft
description: In qualità di amministratore, scopri come gestire la fattura e passare dal piano tariffario gratuito a un piano tariffario standard nell'applicazione Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 888b9f31b8082ac124a773cc489293b335d7d255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157511"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>Gestire la fattura in un'applicazione IoT Central

Questo articolo descrive come, come amministratore, è possibile gestire la fattura nell'applicazione Azure IoT Central nella sezione relativa all'amministrazione. Si apprenderà come spostare l'applicazione dal piano tariffario gratuito a un piano tariffario standard e come aggiornare o ridurre il downgrade del piano tariffario.

Per accedere e usare la sezione **Amministrazione,** è necessario essere nel ruolo *di amministratore* o disporre di un ruolo utente personalizzato che consenta di visualizzare la fatturazione per un'applicazione Azure IoT Central.To access and use the Administration section, you must be in the Administrator role or have a *custom user role* that allows you to view billing for an Azure IoT Central application. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione.

## <a name="move-from-free-to-standard-pricing-plan"></a>Passare dal piano tariffario gratuito a quello standard

- Le applicazioni che utilizzano il piano tariffario gratuito sono gratuite per sette giorni prima della scadenza. Per evitare di perdere dati, è possibile spostarli in un piano tariffario standard in qualsiasi momento prima della scadenza.
- Le applicazioni che utilizzano un piano tariffario standard vengono addebitate per dispositivo, con i primi due dispositivi gratuiti, per applicazione.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Nella sezione dei prezzi, è possibile spostare l'applicazione dal piano di determinazione prezzi gratuito a uno standard.

Per completare il processo self-service, seguire questa procedura:

1. Vai alla pagina **Prezzi** nella sezione **Amministrazione.**

    ![Stato della versione di valutazione](media/howto-view-bill/freetrialbilling.png)

1. Selezionare **Converti in un piano a pagamento**.

    ![Convertire la versione di valutazione](media/howto-view-bill/convert.png)

1. Selezionare Azure Active Directory appropriato e quindi la sottoscrizione di Azure da usare per l'applicazione che usa un piano a pagamento.

1. Dopo aver selezionato **Converti**, l'applicazione utilizza ora un piano a pagamento e si inizia a ricevere la fatturazione.

> [!Note]
> Per impostazione predefinita, si viene convertiti in un piano tariffario *Standard 2.*

## <a name="how-to-change-your-application-pricing-plan"></a>Come modificare il piano tariffario dell'applicazione

Le applicazioni che utilizzano un piano tariffario standard vengono addebitate per dispositivo, con i primi due dispositivi gratuiti, per applicazione.

Nella sezione dei prezzi è possibile aggiornare o declassare il piano tariffario di Azure IoT in qualsiasi momento.

1. Vai alla pagina **Prezzi** nella sezione **Amministrazione.**

    ![Stato della versione di valutazione](media/howto-view-bill/pricing.png)

1. Selezionare **Il piano** e fare clic su **Salva** per eseguire l'aggiornamento o il downgrade.

## <a name="view-your-bill"></a>Visualizzare la fattura

1. Selezionare Azure Active Directory appropriato e quindi la sottoscrizione di Azure da usare per l'applicazione che usa un piano a pagamento.

1. Dopo aver selezionato **Converti**, l'applicazione utilizza ora un piano a pagamento e si inizia a ricevere la fatturazione.

> [!Note]
> Per impostazione predefinita, si viene convertiti in un piano tariffario *Standard 2.*

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come gestire la fattura nell'applicazione Azure IoT Central, il passaggio successivo consigliato consiste nell'apprendere [la personalizzazione dell'interfaccia utente dell'applicazione](howto-customize-ui.md) in Azure IoT Central.Now that you've learned about how to manage your bill in Azure IoT Central application, the suggested next step is to learn about Customize application UI in Azure IoT Central.