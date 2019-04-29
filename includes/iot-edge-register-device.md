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
ms.openlocfilehash: bb0b140dd1f42cae1d5d4bb670af8780d66c1f80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768633"
---
Creare un'identità del dispositivo per il dispositivo simulato in modo che possa comunicare con l'hub IoT. Poiché i dispositivi IoT Edge si comportano e possono essere gestiti in modo diverso dai dispositivi IoT tipici, dichiarare fin dall'inizio che si tratta di un dispositivo IoT Edge. 

1. Nel portale di Azure passare all'hub IoT.
1. Selezionare **IoT Edge** e quindi **Aggiungi dispositivo IoT Edge**.

   ![Add IoT Edge Device (Aggiungi dispositivo IoT Edge)](./media/iot-edge-register-device/add-device.png)

1. Assegnare al dispositivo simulato un ID univoco.
1. Selezionare **Salva** per aggiungere il dispositivo.
1. Selezionare il nuovo dispositivo dall'elenco dei dispositivi.
1. Copiare il valore per **Stringa di connessione - chiave primaria** e salvarlo. Usare questo valore per configurare il runtime IoT Edge nella sezione successiva. 

