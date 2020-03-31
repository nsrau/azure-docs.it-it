---
title: Definire un nuovo tipo di dispositivo gateway in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come definire un nuovo tipo di dispositivo gateway IoT nell'applicazione Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7bb386d39f53331e77bee4d863c431c2e3655f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79298836"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Definire un nuovo tipo di dispositivo gateway IoT nell'applicazione Azure IoT Central

Questa esercitazione illustra come usare un modello di dispositivo gateway per definire un dispositivo gateway nell'applicazione IoT Central. Vengono quindi configurati diversi dispositivi downstream che si connettono all'applicazione IoT Central tramite il dispositivo gateway. 

In questa esercitazione viene creato un modello di dispositivo gateway **Edifici intelligenti**. Un dispositivo gateway **Edifici intelligenti** ha relazioni con altri dispositivi downstream.

![Diagramma della relazione tra il dispositivo gateway e i dispositivi downstream](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Oltre a consentire ai dispositivi downstream di comunicare con l'applicazione IoT Central, un dispositivo gateway può:

* Inviare i propri dati di telemetria, ad esempio la temperatura.
* Rispondere agli aggiornamenti di proprietà scrivibili eseguiti da un operatore. Ad esempio, un operatore potrebbe modificare l'intervallo di invio dei dati di telemetria.
* Rispondere ai comandi, ad esempio il riavvio del dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario [creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Creare modelli di dispositivo downstream

Questa esercitazione usa modelli di dispositivo per un dispositivo **S1 Sensor** e un dispositivo **RS40 Occupancy Sensor** per generare dispositivi downstream simulati.

Per creare un modello di dispositivo per un dispositivo **S1 Sensor**:

1. Nel riquadro sinistro selezionare **Modelli di dispositivo**. Selezionare quindi **+** per iniziare ad aggiungere il modello.

1. Scorrere verso il basso finché non viene visualizzato il riquadro di **S1 Sensor**. Selezionare il riquadro e quindi selezionare **Avanti: Personalizza**.

1. Nella pagina **Verifica** selezionare **Crea** per aggiungere il modello di dispositivo all'applicazione. 

Per creare un modello di dispositivo per un dispositivo ***RS40 Occupancy Sensor**:

1. Nel riquadro sinistro selezionare **Modelli di dispositivo**. Selezionare quindi **+** per iniziare ad aggiungere il modello.

1. Scorrere verso il basso finché non viene visualizzato il riquadro del dispositivo ***RS40 Occupancy Sensor**. Selezionare il riquadro e quindi selezionare **Avanti: Personalizza**.

1. Nella pagina **Verifica** selezionare **Crea** per aggiungere il modello di dispositivo all'applicazione. 

Sono ora disponibili i modelli di dispositivo per i due tipi di dispositivi downstream:

![Modelli di dispositivo per i dispositivi downstream](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Creare un modello di dispositivo gateway

In questa esercitazione si creerà da zero un modello di dispositivo per un dispositivo gateway. Questo modello verrà usato più avanti per creare un dispositivo gateway simulato nell'applicazione.

Per aggiungere un nuovo modello di dispositivo gateway all'applicazione:

1. Nel riquadro sinistro selezionare **Modelli di dispositivo**. Selezionare quindi **+** per iniziare ad aggiungere il modello.

1. Nella pagina **Selezionare il tipo di modello** selezionare il riquadro **Dispositivo IoT** e quindi selezionare **Avanti: Personalizza**.

1. Nella pagina **Personalizza dispositivo** selezionare la casella di controllo **Dispositivo gateway**.

1. Nella pagina **Rivedi** selezionare **Crea**. 

1. Immettere **Dispositivo gateway per edifici intelligenti** come nome del modello.

1. Nella pagina **Crea un modello di funzionalità** selezionare il riquadro **Personalizzato**.

1. Selezionare **+** per aggiungere un'interfaccia.  Scegliere l'interfaccia standard **Informazioni sul dispositivo**.

### <a name="add-relationships"></a>Aggiungere relazioni

A questo punto occorre aggiungere relazioni ai modelli per i modelli di dispositivo downstream:

1. Nel modello **Dispositivo gateway per edifici intelligenti** selezionare **Relazioni**.

1. Selezionare **+ Aggiungi relazione**. Immettere **Sensore ambientale** come nome visualizzato e selezionare **S1 Sensor** come destinazione.

1. Selezionare di nuovo **+ Aggiungi relazione**. Immettere **Sensore di occupazione** come nome visualizzato e selezionare **RS40 Occupancy Sensor** come destinazione.

1. Selezionare **Salva**.

![Modello Dispositivo gateway per edifici intelligenti con relazioni](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo gateway può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo.

Per aggiungere proprietà cloud al modello **Dispositivo gateway per edifici intelligenti**:

1. Nel modello **Dispositivo gateway per edifici intelligenti** selezionare **Proprietà cloud**.

1.  Usare le informazioni nella tabella seguente per aggiungere due proprietà cloud al modello di dispositivo gateway.

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | nessuno          | Data   |
    | Nome del cliente     | nessuno          | string |

2. Selezionare **Salva**.

### <a name="create-views"></a>Creare viste

Il creatore può personalizzare un'applicazione in modo da visualizzare le informazioni sul dispositivo sensore ambientale utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i sensori ambientali connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi.

Per generare le visualizzazioni predefinite per il modello **Dispositivo gateway per edifici intelligenti**:

1. Nel modello **Dispositivo gateway per edifici intelligenti** selezionare **Visualizzazioni**.

1. Selezionare il riquadro **Genera visualizzazioni predefinite** e assicurarsi che tutte le opzioni siano selezionate.

1. Selezionare **Genera visualizzazioni dashboard predefinite**.

## <a name="publish-the-device-template"></a>Pubblicare il modello di dispositivo

Prima di creare un dispositivo gateway simulato o connettere un dispositivo gateway reale, è necessario pubblicare il modello di dispositivo.

Per pubblicare il modello di dispositivo gateway:

1. Selezionare il modello **Dispositivo gateway per edifici intelligenti** nella pagina **Modelli di dispositivo**.

2. Selezionare **Pubblica**.

3. Nella finestra di dialogo **Publish a Device Template** (Pubblica un modello di dispositivo) scegliere **Pubblica**.

Dopo la pubblicazione, un modello di dispositivo viene visualizzato nella pagina **Dispositivi** ed è visibile all'operatore. In un modello di dispositivo pubblicato non è possibile modificare un modello di funzionalità di dispositivo senza creare una nuova versione. È tuttavia possibile apportare aggiornamenti a proprietà cloud, personalizzazioni e visualizzazioni in un modello di dispositivo pubblicato. Questi aggiornamenti non comportano la creazione di una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.

## <a name="create-the-simulated-devices"></a>Creare i dispositivi simulati

Questa esercitazione usa dispositivi downstream simulati e un dispositivo gateway simulato.

Per creare un dispositivo gateway simulato:

1. Nella pagina **Dispositivi** selezionare **Dispositivo gateway per edifici intelligenti** nell'elenco dei modelli di dispositivo.

1. Selezionare **+** per iniziare ad aggiungere un nuovo dispositivo.

1. Lasciare invariati i valori generati per **ID dispositivo** e **Nome dispositivo**. Verificare che l'opzione **Simulato** sia impostata su **Sì**. Selezionare **Create** (Crea).

Per creare un dispositivo downstream simulato:

1. Nella pagina **Dispositivi** selezionare **RS40 Occupancy Sensor** nell'elenco dei modelli di dispositivo.

1. Selezionare **+** per iniziare ad aggiungere un nuovo dispositivo.

1. Lasciare invariati i valori generati per **ID dispositivo** e **Nome dispositivo**. Verificare che l'opzione **Simulato** sia impostata su **Sì**. Selezionare **Create** (Crea).

1. Nella pagina **Dispositivi** selezionare **S1 Sensor** nell'elenco dei modelli di dispositivo.

1. Selezionare **+** per iniziare ad aggiungere un nuovo dispositivo.

1. Lasciare invariati i valori generati per **ID dispositivo** e **Nome dispositivo**. Verificare che l'opzione **Simulato** sia impostata su **Sì**. Selezionare **Create** (Crea).

![Dispositivi simulati nell'applicazione](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Aggiungere le relazioni dei dispositivi downstream a un dispositivo gateway

Ora che i dispositivi simulati sono disponibili nell'applicazione, è possibile creare le relazioni tra i dispositivi downstream e il dispositivo gateway:

1. Nella pagina **Dispositivi** selezionare **S1 Sensor** nell'elenco dei modelli di dispositivo e quindi selezionare il dispositivo simulato **S1 Sensor**.

1. Selezionare **Connetti al gateway**.

1. Nella finestra di dialogo **Connect to a gateway** (Connetti a un gateway) selezionare il modello **Dispositivo gateway per edifici intelligenti** e quindi selezionare l'istanza simulata creata in precedenza.

1. Selezionare **Aggiungi**.

1. Nella pagina **Dispositivi** selezionare **RS40 Occupancy Sensor** nell'elenco dei modelli di dispositivo e quindi selezionare il dispositivo simulato **RS40 Occupancy Sensor**.

1. Selezionare **Connetti al gateway**.

1. Nella finestra di dialogo **Connect to a gateway** (Connetti a un gateway) selezionare il modello **Dispositivo gateway per edifici intelligenti** e quindi selezionare l'istanza simulata creata in precedenza.

1. Selezionare **Aggiungi**.

Entrambi i dispositivi downstream simulati sono ora connessi al dispositivo gateway simulato. Se si passa alla visualizzazione **Dispositivi downstream** del dispositivo gateway, è possibile vedere i dispositivi downstream correlati:

![Visualizzazione Dispositivi downstream](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Selezionare il modello di dispositivo gateway e l'istanza del dispositivo gateway e selezionare **Aggiungi**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

* Creare un nuovo gateway IoT come modello di dispositivo.
* Creare proprietà cloud.
* Creare personalizzazioni.
* Definire una visualizzazione per i dati di telemetria del dispositivo.
* Aggiungere relazioni.
* Pubblicare il modello di dispositivo.

> [!NOTE]
> La generazione di codice basata su VS Code non è attualmente supportata per i dispositivi gateway modellati in IoT Central.

Successivamente, sarà possibile:

> [!div class="nextstepaction"]
> [Connettere un dispositivo](tutorial-connect-device.md)
