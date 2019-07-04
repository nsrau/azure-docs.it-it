---
title: Creare un'applicazione Azure IoT Central | Microsoft Docs
description: Creare una nuova applicazione Azure IoT Central. Creare un'applicazione di valutazione o con pagamento in base al consumo usando un modello di applicazione.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 07b062bd39177f283bd38ed6b687b590c5c4fb46
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066025"
---
# <a name="create-an-azure-iot-central-application"></a>Creare un'applicazione Azure IoT Central

I _produttori_ usano l'interfaccia utente di Azure IoT Central per definire l'applicazione Microsoft Azure IoT Central. Questa guida introduttiva illustra come creare un'applicazione Azure IoT Central contenente un _modello di dispositivo_ di esempio e _dispositivi_ simulati.

## <a name="create-an-application"></a>Creare un'applicazione

Passare alla pagina [Application Manager](https://aka.ms/iotcentral) (Gestione applicazioni) di Azure IoT Central. È necessario eseguire l'accesso con un account Microsoft personale o con l'account aziendale o dell'istituto di istruzione.

Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**. Si aprirà la pagina **Crea applicazione**.

![Pagina Crea applicazione di Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Per creare una nuova applicazione Azure IoT Central:

1. Scegliere un piano di pagamento:
   - Le applicazioni di tipo **Versione di valutazione** sono gratuite per 7 giorni prima della scadenza. Possono essere convertite in applicazioni con pagamento in base al consumo in qualsiasi momento prima della scadenza. Se si crea un'applicazione di tipo **Versione di valutazione**, è necessario immettere le informazioni sul contatto e scegliere se ricevere informazioni e suggerimenti da Microsoft.
   - Le applicazioni di tipo **Pagamento in base al consumo** prevedono un addebito per ogni dispositivo, con i primi 5 dispositivi offerti gratuitamente. Se si crea un'applicazione di tipo **Con pagamento in base al consumo**, è necessario selezionare un'opzione in *Directory*, *Sottoscrizione di Azure* e *Area*:
      - *Directory* è l'istanza di Azure Active Directory (AD) in cui creare l'applicazione. Contiene le identità degli utenti, le credenziali e altre informazioni sull'organizzazione. Se non si ha un'istanza di Azure AD, ne viene creata una automaticamente quando si crea una sottoscrizione di Azure.
      - Una *sottoscrizione di Azure* consente di creare istanze dei servizi di Azure. IoT Central effettuerà il provisioning delle risorse nella sottoscrizione. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription). Dopo aver creato la sottoscrizione di Azure, passare alla pagina **Create Application** (Crea applicazione). Selezionare la sottoscrizione nella casella di riepilogo a discesa **Azure Subscription** (Sottoscrizione di Azure).
      - *Area* è la posizione fisica in cui si vuole creare l'applicazione. In genere è consigliabile scegliere l'area fisicamente più vicina ai dispositivi per ottenere prestazioni ottimali. Le aree in cui Azure IoT Central è disponibile sono elencate nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/). Dopo aver scelto un'area, non è possibile spostare l'applicazione in un'altra area geografica in un secondo momento.

      Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Scegliere un nome descrittivo per l'applicazione, ad esempio **Contoso IoT**. Azure IoT Central genera un prefisso URL univoco. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

1. Scegliere un modello di applicazione. Un modello di applicazione può contenere elementi predefiniti, ad esempio modelli di dispositivi e dashboard che facilitano le cose.

    | Modello di applicazione | DESCRIZIONE |
    | -------------------- | ----------- |
    | Esempio Contoso       | Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
    | Esempio Devkits       | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |
    | Applicazione personalizzata   | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |

1. Selezionare **Create** (Crea).

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'applicazione IoT Central. Ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Panoramica di IoT Central](overview-iot-central-tour.md)
