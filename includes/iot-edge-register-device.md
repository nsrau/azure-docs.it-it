---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37032113"
---
Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso dai dispositivi IoT tipici, dichiarare fin dall'inizio che si tratta di un dispositivo IoT Edge. 

1. Nel portale di Azure passare all'hub IoT.
1. Selezionare **IoT Edge** e quindi **Aggiungi dispositivo IoT Edge**.

   ![Add IoT Edge Device (Aggiungi dispositivo IoT Edge)](./media/iot-edge-register-device/add-device.png)

1. Assegnare al dispositivo simulato un ID univoco.
1. Selezionare **Salva** per aggiungere il dispositivo.
1. Selezionare il nuovo dispositivo dall'elenco dei dispositivi.
1. Copiare il valore per **Stringa di connessione - chiave primaria** e salvarlo. Usare questo valore per configurare il runtime IoT Edge nella sezione successiva. 

