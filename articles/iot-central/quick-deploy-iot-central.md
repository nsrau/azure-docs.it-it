---
title: Creare un'applicazione Azure IoT Central | Microsoft Docs
description: Creare una nuova applicazione Azure IoT Central per gestire dispositivi per la distribuzione di bevande refrigerate. Visualizzare i dati di telemetria generati dai dispositivi simulati.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/12/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c6ed1f0feaa9b8b20d291be7929228707281cf9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158092"
---
# <a name="create-an-azure-iot-central-application"></a>Creare un'applicazione Azure IoT Central

I _produttori_ usano l'interfaccia utente di Azure IoT Central per definire l'applicazione Microsoft Azure IoT Central. Questa guida introduttiva illustra come creare un'applicazione Azure IoT Central contenente un _modello di dispositivo_ di esempio e _dispositivi_ simulati.

## <a name="create-the-application"></a>Creazione dell'applicazione

Per completare questa guida introduttiva, è necessario creare un'applicazione Azure IoT Central dal modello di applicazione **Sample Contoso**.

Passare alla pagina [Application Manager](https://aka.ms/iotcentral) (Gestione applicazioni). Quindi immettere l'indirizzo di posta elettronica e la password usati per accedere alla sottoscrizione di Azure:

![Immettere l'account dell'organizzazione](media/quick-deploy-iot-central/sign-in.png)

Per iniziare a creare una nuova applicazione Azure IoT Central, scegliere **Nuova applicazione**:

![Pagina Application Manager di Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Per creare una nuova applicazione Azure IoT Central:

1. Scegliere il piano di pagamento **Free Trial Application** (Applicazione di prova gratuita).
1. Scegliere un nome descrittivo per l'applicazione, ad esempio **Contoso IoT**. Azure IoT Central genera un prefisso URL univoco. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.
1. Scegliere il modello di applicazione **Sample Contoso**.
1. quindi scegliere **Crea**.

![Pagina Crea applicazione di Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'applicazione Azure IoT Central già popolata contenente un modello di dispositivo **Macchina per la distribuzione di bevande refrigerate** e dispositivi simulati. Per altre informazioni su come definire i propri modelli di dispositivi personalizzati in qualità di produttore, vedere [Define a new device template in your application](tutorial-define-device-type.md) (Definire un nuovo modello di dispositivo nell'applicazione).
