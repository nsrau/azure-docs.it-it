---
title: File di inclusione
description: File di inclusione
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371236"
---
## <a name="create-an-iot-hub"></a>Creare un hub IoT
Creare un hub IoT per connettere l'app per dispositivo simulato. La procedura seguente illustra come completare questa attività usando il portale di Azure.

1. Accedere al [portale di Azure][lnk-portal].

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub IoT**.
   
    ![Indice del portale di Azure][1]

1. Nel riquadro **Hub IoT** immettere le informazioni seguenti per l'hub IoT:

   * **Sottoscrizione**: scegliere la sottoscrizione da usare per creare questo hub IoT.

   * **Gruppo di risorse**: creare un gruppo di risorse per ospitare l'hub IoT o usarne uno esistente. Per altre informazioni, vedere l'articolo su come [usare i gruppi di risorse per gestire le risorse di Azure][lnk-resource-groups].

   * **Area**: selezionare la località più vicina.

   * **Nome**: creare un nome per l'hub IoT. Se il nome immesso è disponibile, viene visualizzato un segno di spunta verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Finestra delle informazioni di base hub IoT][2]

2. Selezionare **Next: Size and scale** (Avanti: Dimensioni e scalabilità) per continuare a creare l'hub IoT. 

3. Scegliere un valore per **Piano tariffario e livello di scalabilità**. Per questo articolo selezionare il livello **F1 - Gratuito** se ancora disponibile nella sottoscrizione. Per altre informazioni, vedere la pagina relativa a [piano tariffario e livello di scalabilità][lnk-pricing].

   ![Finestra per specificare dimensioni e scalabilità dell'hub IoT][3]

4. Selezionare **Rivedi e crea**.

1. Esaminare le informazioni sull'hub IoT e quindi fare clic su **Crea**. La creazione dell'hub IoT può richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento nel riquadro **Notifiche**.

1. Quando il nuovo hub IoT è pronto, fare clic sul suo riquadro nel portale di Azure per aprire la finestra delle proprietà. Ora che è stato creato un hub IoT, individuare le informazioni importanti che consentono di connettere dispositivi e applicazioni all'hub IoT. Fare clic su **Criteri di accesso condivisi**.
   
1. In **Criteri di accesso condivisi** selezionare il criterio **iothubowner**. Copiare il valore di **Stringa di connessione---Chiave primaria** dell'hub IoT per usarlo in seguito. Per altre informazioni, vedere [Controllo di accesso][lnk-access-control] nella "Guida per gli sviluppatori dell'hub IoT".
   
    ![Criteri di accesso condivisi][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
