---
title: File di inclusione (anteprima flussi dispositivo)
description: File di inclusione (anteprima flussi dispositivo)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050161"
---
Questa sezione illustra come creare un hub IoT usando il [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Scegliere **Crea una risorsa** e quindi immettere *Hub IoT* nel campo **Cerca nel Marketplace**.

1. Nei risultati della ricerca selezionare **Hub IoT** e quindi fare clic su **Crea**.

1. Nella scheda **Informazioni di base** completare i campi come indicato di seguito:

   - **Sottoscrizione** Selezionare la sottoscrizione da usare per l'hub.

   - **Gruppo di risorse**: selezionare un gruppo di risorse o crearne uno nuovo. Per crearne uno nuovo, fare clic su **Crea nuovo** e specificare il nome da usare. Per usare un gruppo di risorse esistente, selezionarlo. Per altre informazioni, vedere l'articolo su come [gestire gruppi di risorse di Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Area**: scegliere l'area in cui si vuole collocare l'hub. Selezionare un'area che supporta l'anteprima dei flussi del dispositivo per l'hub IoT, ossia **Stati Uniti centrali** o **Stati Uniti centrali EUAP**.

   - **Nome hub IoT**: immettere un nome per l'hub. Il nome deve essere univoco a livello globale. Se il nome immesso è disponibile, viene visualizzato un segno di spunta verde.

   ![Creazione di un hub IoT nel portale di Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Selezionare **Avanti: Dimensioni e piano** per continuare a creare l'hub.

   ![Configurare le dimensioni e il piano per un nuovo hub IoT con il portale di Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   In **Dimensioni e piano** è possibile accettare le impostazioni predefinite e selezionare **Rivedi e crea** nella parte inferiore. Valutare le opzioni seguenti:

   - **Piano tariffario e livello di scalabilità**: il piano e il livello selezionati. Selezionare uno dei livelli standard (**S1**, **S2** o **S3**) oppure **F1: Livello gratuito**. Questa scelta può anche essere guidata dalle dimensioni della flotta e dai carichi di lavoro non di streaming che si prevedono nell'hub, ad esempio i messaggi di telemetria. Ad esempio, il livello gratuito è utilizzabile a scopo di test e valutazione. Consente la connessione di 500 dispositivi all'hub IoT e un massimo di 8.000 messaggi al giorno. Per ogni sottoscrizione di Azure è possibile creare un solo hub IoT nel livello gratuito. 

   - **Numero di unità dell'hub IoT**: Il numero di messaggi consentiti per unità al giorno dipende dal piano tariffario dell'hub. questa scelta dipende dal carico di lavoro non di streaming previsto nell'hub. È possibile selezionare 1 per il momento.

   - **Impostazioni avanzate** > **Partizioni da dispositivo a cloud**: questa proprietà associa i messaggi da dispositivo a cloud al numero di lettori simultanei di tali messaggi. La maggior parte degli hub richiede solo quattro partizioni.

   Per altre informazioni sulle opzioni dei livelli, vedere [Scegliere il livello di hub IoT più adatto](../articles/iot-hub/iot-hub-scaling.md).

1. Per rivedere le scelte, fare clic su **Rivedi e crea**. I risultati saranno simili ai seguenti:

   ![Informazioni per la creazione del nuovo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Per creare il nuovo hub IoT, fare clic su **Crea**. La procedura richiede alcuni minuti.
