---
title: Configurare il cloud per il servizio Device Provisioning in hub IoT di Azure nel portale
description: 'Esercitazione: Configurare le risorse cloud per il provisioning di dispositivi nel [portale di Azure](https://portal.azure.com) con il servizio Device Provisioning in hub IoT'
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: bdfe851532383e5a051f43cc4b20772f8053dbfc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228320"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Esercitazione: Configurare risorse cloud per il provisioning dei dispositivi con il servizio Device Provisioning in hub IoT

Questa esercitazione illustra come configurare il cloud per il provisioning automatico dei dispositivi con il servizio Device Provisioning in hub IoT. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare il portale di Azure per creare un servizio Device Provisioning in hub IoT e ottenere l'ambito ID
> * Creare un hub IoT
> * Collegare l'hub IoT al servizio Device Provisioning
> * Impostare criteri di allocazione sul servizio Device Provisioning

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Creare un'istanza del servizio Device Provisioning e ottenere l'ambito ID

Seguire questa procedura per creare una nuova istanza del servizio Device Provisioning.

1. Nell'angolo superiore sinistro del portale di Azure fare clic su **Crea una risorsa**.

2. Nella casella di ricerca digitare **provisioning dei dispositivi**. 

3. Fare clic su **Servizio Device Provisioning in hub IoT**.

4. Compilare il modulo **Servizio Device Provisioning in hub IoT** con le informazioni seguenti:
    
   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome** | Qualsiasi nome univoco | -- | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |
   | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |   

   ![Immettere le informazioni di base sul servizio Device Provisioning nel portale](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Fare clic su **Create**(Crea). Dopo alcuni istanti viene creata l'istanza del servizio Device Provisioning e viene visualizzata la pagina **Panoramica**.

6. Nella pagina **Panoramica** della nuova istanza del servizio copiare il valore dell'**ambito ID** per usarlo successivamente. Questo valore viene usato per identificare gli ID di registrazione e offre una garanzia in merito al fatto che l'ID di registrazione sia univoco.

7. Copiare anche il valore dell'**endpoint di servizio** per usarlo successivamente. 

## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Ottenere la stringa di connessione per l'hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

L'hub IoT è stato così creato e sono ora disponibili il nome host e la stringa di connessione dell'hub necessari per completare il resto di questa esercitazione.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Collegare il servizio Device Provisioning a un hub IoT

Il passaggio successivo consiste nel collegare il servizio Device Provisioning e l'hub IoT in modo che il servizio Device Provisioning in hub IoT possa registrare i dispositivi nell'hub. Il servizio può eseguire il provisioning dei dispositivi solo negli hub IoT che sono stati collegati al servizio Device Provisioning. Seguire questa procedura.

1. Nella pagina **Tutte le risorse** fare clic sull'istanza del servizio Device Provisioning creata in precedenza.

2. Nella pagina Device Provisioning Service (Servizio Device Provisioning) fare clic su **Linked IoT hubs** (Hub IoT collegati).

3. Fare clic su **Aggiungi**.

4. Nella pagina **Aggiungi collegamento all'hub IoT** fornire le informazioni seguenti e fare clic su **Salva**:

    * **Sottoscrizione:** assicurarsi di selezionare la sottoscrizione che contiene l'hub IoT. È possibile aggiungere un collegamento all'hub IoT presente in un'altra sottoscrizione.

    * **Hub IoT:** scegliere il nome dell'hub IoT che si vuole collegare con la nuova istanza del servizio Device Provisioning.

    * **Criteri di accesso:** selezionare **iothubowner** come credenziali da usare per stabilire il collegamento con l'hub IoT.

   ![Collegare il nome dell'hub da associare al servizio Device Provisioning nel portale](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Impostare criteri di allocazione sul servizio Device Provisioning

I criteri di allocazione rappresentano un'impostazione del servizio Device Provisioning in hub IoT che determina la modalità di assegnazione dei dispositivi a un hub IoT. Sono supportati tre criteri di allocazione: 

1. **Latenza più bassa**: il provisioning dei dispositivi viene eseguito in un hub IoT in base all'hub con la latenza più bassa per il dispositivo.

2. **Distribuzione ponderata in modo uniforme** (impostazione predefinita): gli hub IoT collegati hanno le stesse probabilità di essere scelti per il provisioning dei dispositivi. Questa è l'impostazione predefinita. Se si esegue il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione. 

3. **Configurazione statica tramite elenco di registrazione**: la specifica dell'hub IoT desiderato nell'elenco di registrazione ha priorità rispetto ai criteri di allocazione a livello del servizio Device Provisioning.

Per impostare i criteri di allocazione, nella pagina Device Provisioning Service (Servizio Device Provisioning) fare clic su **Manage allocation policy** (Gestisci criteri di allocazione). Verificare che i criteri di allocazione siano impostati su **Evenly weighted distribution** (Distribuzione ponderata uniforme) (impostazione predefinita). Al termine, se si apportano modifiche, fare clic su **Save** (Salva).

![Gestire i criteri di allocazione](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Altre esercitazioni della raccolta si basano su questa. Se si prevede di continuare a usare le guide introduttive successive o le esercitazioni, non eliminare le risorse create in questa esercitazione. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa esercitazione nel portale di Azure.

1. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'istanza del servizio Device Provisioning in hub IoT. Nella parte superiore della pagina **Tutte le risorse** fare clic su **Elimina**.  

2. Nel portale di Azure fare clic su **Tutte le risorse** nel menu a sinistra e quindi selezionare l'hub IoT. Nella parte superiore della pagina **Tutte le risorse** fare clic su **Elimina**.
 
## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare il portale di Azure per creare un servizio Device Provisioning in hub IoT e ottenere l'ambito ID
> * Creare un hub IoT
> * Collegare l'hub IoT al servizio Device Provisioning
> * Impostare criteri di allocazione sul servizio Device Provisioning

Passare all'esercitazione successiva per informazioni su come configurare il dispositivo per il provisioning.

> [!div class="nextstepaction"]
> [Configurare il dispositivo per il provisioning](tutorial-set-up-device.md)
