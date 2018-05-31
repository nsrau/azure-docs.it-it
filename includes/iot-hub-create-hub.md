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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371151"
---
1. Accedere al [portale di Azure][lnk-portal].
1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub IoT**.
   
    ![Screenshot del passaggio a Hub IoT nel portale di Azure][1]

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
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md