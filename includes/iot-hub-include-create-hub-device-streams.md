---
title: File di inclusione (anteprima flussi dispositivo)
description: File di inclusione (anteprima flussi dispositivo)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ede897054a6cbef254c06bd1d810b933ec09016a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158645"
---
Questa sezione illustra come creare un hub IoT usando il [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Scegliere **+Crea una risorsa** e quindi scegliere **Internet delle cose**.

3. Fare clic su **Hub IoT** nell'elenco a destra. Verrà visualizzata la prima schermata per la creazione di un hub IoT.

   ![Screenshot che illustra come creare un hub nel portale di Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Compilare i campi:

   **Sottoscrizione** selezionare la sottoscrizione da usare per l'hub IoT.

   **Gruppo di risorse**: è possibile creare un nuovo gruppo di risorse o selezionarne uno esistente. Per crearne uno nuovo, fare clic su **Crea nuovo** e specificare il nome da usare. Per usare un gruppo di risorse esistente, fare clic su **Usa esistente** e selezionare il gruppo di risorse nell'elenco a discesa. Per altre informazioni, vedere l'articolo su come [gestire gruppi di risorse di Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Area**: si tratta dell'area in cui si vuole collocare l'hub. Selezionare un'area che supporta l'anteprima dei flussi dispositivo per l'hub IoT, ossia Stati Uniti centrali o Stati Uniti centrali EUAP.

   **Nome hub IoT**: inserire il nome dell'hub IoT. Il nome deve essere univoco a livello globale. Se il nome immesso è disponibile, viene visualizzato un segno di spunta verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Fare clic su **Avanti: Dimensioni e piano** per continuare a creare l'hub IoT.

   ![Screenshot che mostra la configurazione delle dimensioni e della scalabilità per un nuovo hub IoT con il portale di Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   In questa schermata è sufficiente accettare le impostazioni predefinite e fare semplicemente clic su **Revisione e creazione** nella parte inferiore.

   **Piano tariffario e livello di scalabilità**: assicurarsi di selezionare uno degli standard (S1, S2, S3) o il livello gratuito (F1). Questa scelta può anche essere guidata dalle dimensioni della flotta e dai carichi di lavoro non di streaming che si prevedono nell'hub (ad esempio, i messaggi di telemetria). Ad esempio, il livello gratuito è utilizzabile a scopo di test e valutazione. Consente la connessione di 500 dispositivi all'hub IoT e un massimo di 8.000 messaggi al giorno. Ogni sottoscrizione Azure può creare hub IoT nel livello gratuito. 

   **Unità di hub IoT**: questa scelta dipende dal carico di lavoro non di streaming previsto nell'hub. È possibile selezionare 1 per il momento.

   Per informazioni dettagliate sulle altre opzioni relative al livello, vedere [Scegliere il livello più adatto di hub IoT](../articles/iot-hub/iot-hub-scaling.md).

5. Fare clic su **Revisione e creazione** per rivedere le scelte effettuate. Dovrebbe essere visualizzata una schermata simile alla seguente.

   ![Screenshot per la revisione delle informazioni per la creazione del nuovo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

6. Fare clic su **Crea** per creare il nuovo hub IoT. La creazione dell'hub richiede alcuni minuti.
