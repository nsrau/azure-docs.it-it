---
title: Aggiungere un dispositivo Azure IoT Edge in Azure IoT Central | Microsoft Docs
description: Un operatore può aggiungere un dispositivo Azure IoT Edge all'applicazione Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892637"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Esercitazione: Aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central (funzionalità in anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come aggiungere e configurare un *dispositivo Azure IoT Edge* nell'applicazione Microsoft Azure IoT Central. In questa esercitazione è stata scelta una macchina virtuale Linux abilitata per Azure IoT Edge da Azure Marketplace.

Questa esercitazione è costituita da due parti:

* La prima illustrerà agli operatori come eseguire il provisioning cloud-first di un dispositivo Azure IoT Edge.
* Successivamente, si apprenderà come eseguire il provisioning device-first di un dispositivo Azure IoT Edge.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un nuovo dispositivo Azure IoT Edge
> * Configurare il dispositivo Azure IoT Edge per facilitare il provisioning tramite la chiave di firma di accesso condiviso
> * Visualizzare i dashboard e l'integrità dei moduli in IoT Central
> * Inviare comandi a un modulo in esecuzione nel dispositivo Azure IoT Edge
> * Inviare proprietà a un modulo in esecuzione nel dispositivo Azure IoT Edge

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'applicazione Azure IoT Central. Seguire i passaggi di questa guida di avvio rapido per la [creazione di un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Abilitare un gruppo di registrazione di Azure IoT Edge
Abilitare le chiavi di firma di accesso condiviso per il gruppo di registrazione di Azure IoT Edge dalla pagina di amministrazione.

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Provisioning di dispositivi Azure IoT Edge cloud-first   
In questa sezione verrà creato un nuovo dispositivo Azure IoT Edge usando il **modello di sensore ambientale** e si effettuerà il provisioning di un dispositivo. Fare clic su Dispositivi nel riquadro di spostamento a sinistra e fare clic su modello di sensore ambientale. 

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Fare clic su **+ nuovo** e immettere un ID e un nome per il dispositivo. 

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Il dispositivo passa alla modalità **Registrato**.

![Modello di dispositivo - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Distribuire una macchina virtuale Linux abilitata per Azure IoT Edge

>Note: è possibile scegliere di usare qualsiasi computer o dispositivo. Sistema operativo: Linux o Windows

Per questa esercitazione è stata scelta una macchina virtuale Linux abilitata per Azure IoT, che può essere creata in Azure. Si verrà reindirizzati ad [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview). Fare clic sul pulsante **Scarica adesso**. 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Fare clic su **Continue** (Continua)

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Si verrà reindirizzati al portale di Azure. Fare clic sul pulsante **Crea**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selezionare la sottoscrizione, creare preferibilmente un nuovo gruppo di risorse, selezionare Stati Uniti occidentali 2 per la disponibilità della macchina virtuale e immettere nome utente e password. Ricordare che nome utente e password saranno necessari per i passaggi successivi. Fare clic su **Rivedi e crea**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Una volta convalidata, fare clic su **Crea**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

La creazione delle risorse richiede alcuni minuti. Fare clic su Vai a **Risorsa**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Effettuare il provisioning della macchina virtuale come dispositivo Azure IoT Edge 

In Supporto e risoluzione dei problemi nel riquadro di spostamento a sinistra fare clic su Console seriale

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Verrà visualizzata una schermata simile alla seguente

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Premere INVIO, specificare nome utente e password, quindi premere INVIO. 

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Per eseguire un comando come amministratore/root, eseguire il comando: **sudo su –**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Controllare la versione del runtime Azure IoT Edge. La versione corrente disponibile a livello generale è la versione 1.0.8

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installare vim editor o usare nano se si preferisce. 

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Modificare file config.yaml di Azure IoT Edge

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Scorrere verso il basso e impostare come commento la parte della stringa di connessione del file YAML. 

**Prima**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Dopo** (premere ESC e premere la a minuscola per avviare la modifica)

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Rimuovere il commento nella parte della chiave simmetrica del file YAML. 

**Prima**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Dopo**

![Macchina virtuale Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Passare a IoT Central e ottenere l'ID ambito, l'ID dispositivo e la chiave simmetrica del dispositivo Azure IoT Edge ![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Passare alla casella Linux e sostituire l'ID ambito e l'ID registrazione con l'ID dispositivo e la chiave simmetrica

Premere **ESC**, digitare **:wq!** e premere **INVIO** per salvare le modifiche

Riavviare Azure IoT Edge per elaborare le modifiche e premere **INVIO**

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digitare: **iotedge list**. L'operazione richiederà qualche minuto e verranno distribuiti tre moduli

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Esplora dispositivi in IoT Central 

In IoT Central il dispositivo passerà allo stato di provisioning effettuato

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

La scheda Moduli visualizza lo stato del dispositivo e del modulo in IoT Central 

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Le proprietà cloud verranno visualizzate in un modulo (dal modello di dispositivo creato nei passaggi precedenti). Immettere i valori e fare clic su **Salva**. 

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Riquadro Dashboard

![Connessione dispositivo](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Questa esercitazione illustra come:

* Aggiungere un nuovo dispositivo Azure IoT Edge
* Configurare il dispositivo Azure IoT Edge per facilitare il provisioning tramite la chiave di firma di accesso condiviso
* Visualizzare i dashboard e l'integrità dei moduli in IoT Central
* Inviare comandi a un modulo in esecuzione nel dispositivo Azure IoT Edge
* Inviare proprietà a un modulo in esecuzione nel dispositivo Azure IoT Edge

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi Azure IoT Edge in Azure IoT Central, il passaggio successivo consigliato è:

<!-- Next how-tos in the sequence -->

Come configurare un gateway trasparente, seguire questa esercitazione

> [!div class="nextstepaction"]
> [Configurare un gateway trasparente](../../iot-edge/how-to-create-transparent-gateway.md)
