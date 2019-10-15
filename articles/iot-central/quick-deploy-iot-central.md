---
title: Creare un'applicazione Azure IoT Central | Microsoft Docs
description: Creare una nuova applicazione Azure IoT Central. Creare un'applicazione di valutazione o con pagamento in base al consumo usando un modello di applicazione.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001259"
---
# <a name="create-an-azure-iot-central-application"></a>Creare un'applicazione Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

I _produttori_ usano l'interfaccia utente di Azure IoT Central per definire l'applicazione Microsoft Azure IoT Central. Questa guida introduttiva illustra come creare un'applicazione Azure IoT Central contenente un _modello di dispositivo_.

## <a name="create-an-application"></a>Creare un'applicazione

Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral). Eseguire quindi l'accesso con un account Microsoft personale, aziendale o dell'istituto di istruzione.

Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**. Questo collegamento consente di accedere alla pagina **Creare un'applicazione**.

![Pagina Creare un'applicazione di Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Per creare una nuova applicazione Azure IoT Central:

1. Scegliere un piano di pagamento:
   - Le applicazioni di tipo **Versione di prova** sono gratuite per sette giorni prima della scadenza. Possono essere convertite in applicazioni **con pagamento in base al consumo** in qualsiasi momento prima della scadenza. Se si crea un'applicazione di tipo **Versione di valutazione**, è necessario immettere le informazioni sul contatto e scegliere se ricevere informazioni e suggerimenti da Microsoft.
   - Le applicazioni di tipo **Con pagamento in base al consumo** prevedono un addebito per ogni dispositivo, con i primi cinque dispositivi offerti gratuitamente. Se si crea un'applicazione di tipo **Con pagamento in base al consumo**, è necessario selezionare un'opzione in *Directory*, *Sottoscrizione di Azure* e *Area*:
        - *Directory* è l'istanza di Azure Active Directory (AD) in cui creare l'applicazione. Contiene le identità degli utenti, le credenziali e altre informazioni sull'organizzazione. Se non si ha un'istanza di Azure AD, ne viene creata una automaticamente quando si crea una sottoscrizione di Azure.
        - Una *sottoscrizione di Azure* consente di creare istanze dei servizi di Azure. IoT Central effettua il provisioning delle risorse nella sottoscrizione. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription). Dopo aver creato la sottoscrizione di Azure, passare alla pagina **Create Application** (Crea applicazione). Selezionare la sottoscrizione nella casella di riepilogo a discesa **Azure Subscription** (Sottoscrizione di Azure).
        - *Area* è la posizione fisica o l'[area geografica](https://azure.microsoft.com/global-infrastructure/geographies/) in cui si vuole creare l'applicazione. Per ottenere prestazioni ottimali, in genere è consigliabile scegliere l'area fisicamente più vicina ai dispositivi. È possibile vedere le aree in cui Azure IoT Central è disponibile nella pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central). Dopo aver scelto un'area, non è possibile spostare l'applicazione in un'altra area geografica in un secondo momento.

        Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Selezionare un modello di applicazione. Un modello di applicazione può contenere elementi predefiniti, ad esempio modelli di dispositivi e dashboard che facilitano le cose.

    | Modello di applicazione | DESCRIZIONE |
    | -------------------- | ----------- |
    | Esempio Contoso       | Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
    | Esempio Devkits       | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |
    | Applicazione personalizzata   | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |

1. Azure IoT Central suggerisce automaticamente il nome di un'applicazione in base al modello di applicazione selezionato. È possibile accettare questo no oppure immettere un nome descrittivo per l'applicazione, ad esempio **Contoso IoT**. Azure IoT Central genera anche un prefisso URL univoco in base al nome dell'applicazione. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

1. Immettere le informazioni aggiuntive necessarie per il piano di pagamento selezionato in precedenza nel passaggio 1.

1. Selezionare **Crea** nella parte inferiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'applicazione IoT Central. Ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](./tutorial-define-device-type.md)
