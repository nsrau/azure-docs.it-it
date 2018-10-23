---
title: Creare un'applicazione Azure IoT Central | Microsoft Docs
description: Creare una nuova applicazione Azure IoT Central per gestire dispositivi per la distribuzione di bevande refrigerate. Visualizzare i dati di telemetria generati dai dispositivi simulati.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0199efbc1456206c67efb846b4381cae333e3749
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309043"
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
