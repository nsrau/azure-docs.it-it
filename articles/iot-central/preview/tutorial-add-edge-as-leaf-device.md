---
title: Aggiungere un dispositivo Azure IoT Edge in Azure IoT Central | Microsoft Docs
description: Un operatore può aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406337"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Esercitazione: Aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come aggiungere e configurare un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central. Nell'esercitazione è stata scelta una macchina virtuale Linux abilitata per IoT Edge da Azure Marketplace.

Questa esercitazione è costituita da due parti:

* Nella prima si apprenderà, in qualità di operatore, come effettuare il provisioning cloud-first di un dispositivo IoT Edge.
* Successivamente, si apprenderà come effettuare il provisioning device-first di un dispositivo IoT Edge.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un nuovo dispositivo IoT Edge
> * Configurare il dispositivo IoT Edge per facilitare il provisioning tramite una chiave di firma di accesso condiviso
> * Visualizzare i dashboard e l'integrità dei moduli in IoT Central
> * Inviare comandi a un modulo in esecuzione nel dispositivo IoT Edge
> * Impostare proprietà in un modulo in esecuzione nel dispositivo IoT Edge

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'applicazione Azure IoT Central. Seguire [questa guida di avvio rapido per la creazione di un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Abilitare un gruppo di registrazione di Azure IoT Edge
Abilitare le chiavi di firma di accesso condiviso per il gruppo di registrazione di Azure IoT Edge dalla pagina **Amministrazione**.

![Screenshot della pagina Amministrazione con la voce Connessione dispositivo evidenziata](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Effettuare il provisioning di un dispositivo Azure IoT Edge cloud-first  
In questa sezione verrà creato un nuovo dispositivo Azure IoT Edge usando il modello di sensore ambientale e verrà effettuato il provisioning di un dispositivo. Select **Dispositivi** > **Environment Sensor Template** (Modello di sensore ambientale). 

![Screenshot della pagina Dispositivi con il modello di sensore ambientale evidenziato](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Fare clic su **+ Nuovo** e immettere l'ID dispositivo e il nome desiderato. Selezionare **Create** (Crea).

![Screenshot della finestra di dialogo Crea nuovo dispositivo, con le voci ID dispositivo e Crea evidenziate](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Il dispositivo passa alla modalità **Registrato**.

![Screenshot della pagina del modello di sensore ambientale con la voce Stato del dispositivo evidenziata](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Distribuire una macchina virtuale Linux abilitata per IoT Edge

> [!NOTE]
> è possibile scegliere di usare qualsiasi computer o dispositivo. Il sistema operativo può essere Linux o Windows.

Per questa esercitazione è stata scelta una macchina virtuale Linux abilitata per Azure IoT, creata in Azure. In [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) selezionare **SCARICA ADESSO**. 

![Screenshot di Azure Marketplace, con la voce SCARICA ADESSO evidenziata](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Selezionare **Continua**.

![Screenshot della finestra di dialogo Crea questa app in Azure con la voce Continua evidenziata](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Si verrà reindirizzati al portale di Azure. Selezionare **Create** (Crea).

![Screenshot del portale di Azure con la voce Crea evidenziata](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selezionare la **Sottoscrizione**, creare un nuovo gruppo di risorse e selezionare l'area **Stati Uniti occidentali 2** per la disponibilità della macchina virtuale. Immettere quindi le informazioni su utente e password. Queste informazioni saranno necessari per i passaggi successivi, quindi prenderne nota. Selezionare **Rivedi e crea**.

![Screenshot della pagina dei dettagli Crea macchina virtuale con varie opzioni evidenziate](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Dopo la convalida selezionare **Crea**.

![Screenshot della pagina Crea macchina virtuale con convalida superata e la voce Crea evidenziata](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

La creazione delle risorse richiede alcuni minuti. Selezionare **Vai alla risorsa**.

![Screenshot della pagina di completamento della distribuzione con la voce Vai alla risorsa evidenziata](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Effettuare il provisioning della macchina virtuale come dispositivo IoT Edge 

In **Supporto e risoluzione dei problemi** selezionare **Console seriale**.

![Screenshot delle opzioni di Supporto e risoluzione dei problemi, con la voce Console seriale evidenziata](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Verrà visualizzata una schermata simile alla seguente:

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Premere INVIO, specificare nome utente e password quando richiesto, quindi premere di nuovo INVIO. 

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Per eseguire un comando come amministratore (utente "ROOT"), immettere: **sudo su –**

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Controllare la versione del runtime IoT Edge. Al momento della stesura di questo articolo, la versione disponibile a livello generale è la versione 1.0.8.

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installare l'editor vim o, se si preferisce, usare nano. 

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Modificare il file config.yaml di IoT Edge.

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Scorrere verso il basso e impostare come commento la parte del file YAML relativa alla stringa di connessione. 

**Prima**

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Dopo** (premere ESC e premere la a minuscola per avviare la modifica)

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Rimuovere il commento nella parte del file YAML relativa alla chiave simmetrica. 

**Prima**

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Dopo**

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Passare a IoT Central. Ottenere l'ID ambito, l'ID dispositivo e la chiave simmetrica del dispositivo IoT Edge.
![Screenshot di IoT Central con varie opzioni di connessione del dispositivo evidenziate](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Passare al computer Linux e sostituire l'ID ambito e l'ID registrazione con l'ID dispositivo e la chiave simmetrica.

Premere ESC, digitare **:wq!** e premere INVIO per salvare le modifiche.

Riavviare IoT Edge per elaborare le modifiche e premere INVIO.

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Digitare **iotedge list**. Dopo alcuni minuti, verranno visualizzati tre moduli distribuiti.

![Screenshot della console](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Esplora dispositivi in IoT Central 

In IoT Central il dispositivo passerà allo stato Con provisioning.

![Screenshot delle opzioni dei dispositivi IoT Central, con la voce Stato del dispositivo evidenziata](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

La scheda **Moduli** mostrerà lo stato del dispositivo e del modulo in IoT Central. 

![Screenshot della scheda Moduli in IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Le proprietà cloud verranno visualizzate in un modulo, dal modello di dispositivo creato nei passaggi precedenti. Immettere i valori e fare clic su **Salva**. 

![Screenshot del modulo di dispositivo Edge Linux personale](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Ecco una visualizzazione presentata sotto forma di riquadro del dashboard.

![Screenshot dei riquadri del dashboard del dispositivo Edge Linux personale](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi IoT Edge in IoT Central, il passaggio successivo consigliato è:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Configurare un gateway trasparente](../../iot-edge/how-to-create-transparent-gateway.md)
