---
title: Configurare il cloud per il servizio Device Provisioning in hub IoT di Azure nel portale | Microsoft Docs
description: Provisioning automatico dei dispositivi nell'hub IoT nel portale di Azure
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
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 088d127521ce89d3a82e30ad8797fe5746ae7e03
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---

# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Configurare risorse cloud per il provisioning dei dispositivi con il servizio Device Provisioning in hub IoT

Questa esercitazione illustra come configurare il cloud per il provisioning automatico dei dispositivi con il servizio Device Provisioning in hub IoT. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare il portale di Azure per creare un servizio Device Provisioning in hub IoT e ottenere l'ambito ID
> * Creare un hub IoT
> * Collegare l'hub IoT al servizio Device Provisioning
> * Impostare criteri di allocazione sul servizio Device Provisioning

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Creare un'istanza del servizio Device Provisioning e ottenere l'ambito ID

Seguire questa procedura per creare una nuova istanza del servizio Device Provisioning.

1. Nell'angolo superiore sinistro del portale di Azure fare clic su **Nuovo**.
2. Nella casella di ricerca digitare **provisioning dei dispositivi**. 
3. Fare clic su **Servizio Device Provisioning in hub IoT**.
4. Compilare il modulo **Servizio Device Provisioning in hub IoT** con le informazioni seguenti:
    
   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome** | Qualsiasi nome univoco | -- | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |   

   ![Immettere le informazioni di base su DPS nel portale](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Fare clic su **Crea**.
6. L'*ambito ID* viene usato per identificare gli ID di registrazione e offre una garanzia in merito al fatto che l'ID di registrazione sia univoco. Per ottenere questo valore, fare clic su **Panoramica** per aprire la pagina **Essentials** per il servizio Device Provisioning. Copiare il valore di **ID Scope** (Ambito ID) in un percorso temporaneo per un uso successivo.
7. Prendere anche nota del valore **Service endpoint** (Endpoint di servizio) oppure copiarlo in un percorso temporaneo per un uso successivo. 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione dell'hub necessari per completare il resto di questa esercitazione.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Collegare il servizio Device Provisioning a un hub IoT

Il passaggio successivo consiste nel collegare il servizio Device Provisioning e l'hub IoT in modo che il servizio Device Provisioning in hub IoT possa registrare i dispositivi nell'hub. Il servizio può eseguire il provisioning dei dispositivi solo negli hub IoT che sono stati collegati al servizio Device Provisioning. Seguire questa procedura.

1. Nella pagina **Tutte le risorse** fare clic sull'istanza del servizio Device Provisioning creata in precedenza.
2. Nella pagina Device Provisioning Service (Servizio Device Provisioning) fare clic su **Linked IoT hubs** (Hub IoT collegati).
3. Fare clic su **Aggiungi**.
4. Nella pagina **Add link to IoT hub** (Aggiungi collegamento all'hub IoT) usare i pulsanti di opzione per specificare se l'hub IoT collegato si trova nella sottoscrizione attuale o in una sottoscrizione diversa. Scegliere quindi il nome dell'hub IoT dalla casella **IoT Hub** (Hub IoT).
5. Fare clic su **Salva**.

   ![Collegare il nome dell'hub da associare a DPS nel portale](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Impostare criteri di allocazione sul servizio Device Provisioning

I criteri di allocazione rappresentano un'impostazione del servizio Device Provisioning in hub IoT che determina la modalità di assegnazione dei dispositivi a un hub IoT. Sono supportati tre criteri di allocazione: 

1. **Lowest latency** (Latenza minima): il provisioning dei dispositivi viene eseguito in un hub IoT in base all'hub con latenza minima per il dispositivo.
2. **Evenly weighted distribution** (Distribuzione ponderata uniforme): gli hub IoT collegati hanno le stesse probabilità di avere dispositivi sottoposti a provisioning. Questa è l'impostazione predefinita. Se si esegue il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione. 
3. **Static configuration via the enrollment list** (Configurazione statica tramite l'elenco di registrazione): la specifica dell'hub IoT desiderato nell'elenco di registrazione ha priorità rispetto ai criteri di allocazione a livello del servizio Device Provisioning.

Per impostare i criteri di allocazione, nella pagina Device Provisioning Service (Servizio Device Provisioning) fare clic su **Manage allocation policy** (Gestisci criteri di allocazione). Verificare che i criteri di allocazione siano impostati su **Evenly weighted distribution** (Distribuzione ponderata uniforme) (impostazione predefinita). Al termine, se si apportano modifiche, fare clic su **Save** (Salva).

![Gestire i criteri di allocazione](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni della raccolta si basano su questa. Se si prevede di continuare a usare le guide introduttive successive o le esercitazioni, non eliminare le risorse create in questa esercitazione. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa esercitazione nel portale di Azure.

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'istanza del servizio Device Provisioning in hub IoT. Nella parte superiore della pagina **Tutte le risorse** fare clic su **Elimina**.  
2. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Nella parte superiore della pagina **Tutte le risorse** fare clic su **Elimina**.
 
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Usare il portale di Azure per creare un servizio Device Provisioning in hub IoT e ottenere l'ambito ID
> * Creare un hub IoT
> * Collegare l'hub IoT al servizio Device Provisioning
> * Impostare criteri di allocazione sul servizio Device Provisioning

Passare all'esercitazione successiva per informazioni su come configurare il dispositivo per il provisioning.

> [!div class="nextstepaction"]
> [Configurare il dispositivo per il provisioning](tutorial-set-up-device.md)

