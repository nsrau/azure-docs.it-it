---
title: File di inclusione
description: File di inclusione
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d8f8021925ac9c9e427dd6571ecaa1a30c85497
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180769"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Successivamente, creare un'identità del dispositivo e salvare la chiave per un uso successivo. Questa identità del dispositivo viene usata dall'applicazione di simulazione per inviare messaggi all'hub IoT. Questa funzionalità non è disponibile in PowerShell o quando si usa un modello di Azure Resource Manager. La procedura seguente illustra la creazione del dispositivo simulato usando il [portale di Azure](https://portal.azure.com).

1. Aprire il [portale di Azure](https://portal.azure.com) ed eseguire l'accesso all'account di Azure.

2. Selezionare **Gruppi di risorse** e quindi scegliere il proprio gruppo di risorse. Questa esercitazione usa **ContosoResources**.

3. Nell'elenco delle risorse selezionare l'hub IoT. Questa esercitazione usa **ContosoTestHub**. Selezionare **dispositivi IoT** dal riquadro Hub.

4. Selezionare **+ Aggiungi**. Nel riquadro Aggiungi dispositivo, immettere l'ID del dispositivo. Questa esercitazione usa **Contoso-Test-Device**. Lasciare vuote le chiavi e controllare **Genera chiavi automaticamente**. Assicurarsi che **Connetti dispositivo all'hub IoT** sia abilitata. Selezionare **Salva**.

   ![Schermata Aggiungi dispositivi](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Ora che è stato creato, selezionare il dispositivo per visualizzare le chiavi generate. Selezionare l'icona Copia sulla chiave primaria e salvarla in un punto, ad esempio nel Blocco note per la fase di test di questa esercitazione.

   ![Dettagli del dispositivo, comprese le chiavi](./media/iot-hub-include-create-simulated-device-portal/device-details.png)