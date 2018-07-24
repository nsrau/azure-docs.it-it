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
ms.openlocfilehash: 63acf0297a694ff442d56e67d52fd9b4e49f812d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008587"
---
Il primo passaggio consiste nell'usare il portale di Azure per creare un hub IoT nella propria sottoscrizione. L'hub IoT consente di acquisire volumi elevati di dati di telemetria nel cloud da molti dispositivi. L'hub abilita quindi la lettura ed elaborazione di tali dati da parte di uno o più servizi back-end in esecuzione nel cloud.

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub IoT**.

    ![Selezionare l'installazione dell'hub IoT][1]

1. Nel riquadro **Hub IoT** immettere le informazioni seguenti per l'hub IoT:

   * **Sottoscrizione**: scegliere la sottoscrizione da usare per creare questo hub IoT.
   * **Gruppo di risorse**: creare un gruppo di risorse per ospitare l'hub IoT o usarne uno esistente. Inserendo tutte le risorse correlate in un gruppo, ad esempio **TestResources**, è possibile gestirle tutte insieme. Ad esempio, con l'eliminazione del gruppo di risorse vengono eliminate tutte le risorse contenute in quel gruppo. Per altre informazioni, vedere l'articolo su come [usare i gruppi di risorse per gestire le risorse di Azure][lnk-resource-groups].
   * **Area:** selezionare la località più vicina ai dispositivi.
   * **Nome**: creare un nome univoco per l'hub IoT. Se il nome immesso è disponibile, viene visualizzato un segno di spunta verde.

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