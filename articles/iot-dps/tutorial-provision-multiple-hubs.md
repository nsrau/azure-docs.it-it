---
title: Usare il servizio Device Provisioning in hub IoT per il provisioning dei dispositivi tra hub IoT con bilanciamento del carico | Microsoft Docs
description: Provisioning automatico dei dispositivi con DPS tra hub IoT con bilanciamento del carico nel portale di Azure
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico

Questa esercitazione mostra come eseguire il provisioning dei dispositivi per più hub IoT con bilanciamento del carico usando il servizio Device Provisioning (DPS). In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare il portale di Azure per eseguire il provisioning di un secondo dispositivo in un secondo hub IoT 
> * Aggiungere una voce dell'elenco di registrazione al secondo dispositivo
> * Impostare i criteri di allocazione DPS sulla **distribuzione uniforme**
> * Collegare il nuovo hub IoT a DPS

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione si basa sulla precedente, [Provision device to a hub](tutorial-provision-device-to-hub.md) (Provisioning di un dispositivo in un hub).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Usare il portale di Azure per eseguire il provisioning di un secondo dispositivo in un secondo hub IoT

Seguire la procedura nell'esercitazione [Provision device to a hub](tutorial-provision-device-to-hub.md) (Provisioning di un dispositivo in un hub) per eseguire il provisioning di un secondo dispositivo in un altro hub IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Aggiungere una voce dell'elenco di registrazione al secondo dispositivo

L'elenco di registrazione indica al servizio DPS il metodo di attestazione (il metodo per confermare l'identità di un dispositivo) usato con il dispositivo. Il passaggio successivo consiste nell'aggiungere una voce dell'elenco di registrazione per il secondo dispositivo. 

1. Nella pagina del servizio DPS fare clic su **Manage enrollments** (Gestisci registrazioni). Verrà visualizzata la pagina **Add enrollment list entry** (Aggiungi voce elenco di registrazione). 
2. Nella parte superiore della pagina fare clic su **Add** (Aggiungi).
2. Compilare i campi e quindi fare clic su **Save** (Salva).

## <a name="set-the-dps-allocation-policy"></a>Impostare i criteri di allocazione DPS

I criteri di allocazione rappresentano un'impostazione del servizio DPS che determina la modalità di assegnazione dei dispositivi a un hub IoT. Sono supportati tre criteri di allocazione: 

1. **Lowest latency** (Latenza minima): il provisioning dei dispositivi viene eseguito in un hub IoT in base all'hub con latenza minima per il dispositivo.
2. **Evenly weighted distribution** (Distribuzione ponderata uniforme): gli hub IoT collegati hanno le stesse probabilità di avere dispositivi sottoposti a provisioning. Questa è l'impostazione predefinita. Se si esegue il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione. 
3. **Static configuration via the enrollment list** (Configurazione statica tramite l'elenco di registrazione): la specifica dell'hub IoT desiderato nell'elenco di registrazione ha la priorità rispetto ai criteri di allocazione a livello di servizio DPS.

Seguire questa procedura per impostare i criteri di allocazione:

1. Per impostare i criteri di allocazione, nella pagina DPS fare clic su **Manage allocation policy** (Gestisci criteri di allocazione).
2. Impostare i criteri di allocazione su **Evenly weighted distribution** (Distribuzione ponderata uniforme).
3. Fare clic su **Salva**.

## <a name="link-the-new-iot-hub-to-dps"></a>Collegare il nuovo hub IoT a DPS

Collegare DPS e l'hub IoT in modo che il servizio possa registrare i dispositivi in tale hub.

1. Nella pagina **Tutte le risorse** fare clic sul servizio DPS creato in precedenza.
2. Nella pagina DPS fare clic su **Linked IoT hubs** (Hub IoT collegati).
3. Fare clic su **Aggiungi**.
4. Nella pagina **Add link to IoT hub** (Aggiungi collegamento all'hub IoT) usare i pulsanti di opzione per specificare se l'hub IoT collegato si trova nella sottoscrizione attuale o in una sottoscrizione diversa. Scegliere quindi il nome dell'hub IoT dalla casella **IoT Hub** (Hub IoT).
5. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Usare il portale di Azure per eseguire il provisioning di un secondo dispositivo in un secondo hub IoT 
> * Aggiungere una voce dell'elenco di registrazione al secondo dispositivo
> * Impostare i criteri di allocazione DPS sulla **distribuzione uniforme**
> * Collegare il nuovo hub IoT a DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
