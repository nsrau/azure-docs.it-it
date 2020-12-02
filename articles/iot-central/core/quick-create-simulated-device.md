---
title: "Avvio rapido: Aggiungere un dispositivo simulato all'applicazione Azure IoT Central"
description: Questa guida di avvio rapido mostra come creare un modello di dispositivo e aggiungere un dispositivo simulato all'applicazione IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f8d366554634444db16eb3292f100540f3808e8a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992869"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Guida introduttiva: Aggiungere un dispositivo simulato all'applicazione IoT Central

*Questo articolo è rivolto a operatori, autori e amministratori.*

Un modello di dispositivo definisce le funzionalità di un dispositivo che si connette all'applicazione IoT Central. Le funzionalità includono i dati di telemetria inviati dal dispositivo, le proprietà del dispositivo e i comandi a cui un dispositivo risponde. Da un modello di dispositivo, un creatore o un operatore può aggiungere dispositivi reali e simulati a un'applicazione. I dispositivi simulati sono utili per testare il comportamento dell'applicazione IoT Central prima di connettere dispositivi reali.

In questa guida di avvio rapido si aggiunge un modello di dispositivo per una scheda di sviluppo ESP32-Azure IoT Kit e si crea un dispositivo simulato. Per completare questo argomento di avvio rapido, non è necessario avere un dispositivo reale perché si userà una simulazione. Un dispositivo ESP32:

* Invia dati di telemetria, ad esempio la temperatura.
* Segnala proprietà specifiche del dispositivo, ad esempio la temperatura massima raggiunta da quando è stato riavviato.
* Risponde ai comandi, ad esempio per il riavvio.
* Segnala le proprietà generiche del dispositivo, ad esempio la versione del firmware e il numero di serie.

## <a name="prerequisites"></a>Prerequisiti

Completare la guida di avvio rapido [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md) per creare un'applicazione IoT Central usando il modello **App personalizzata > Applicazione personalizzata**.

## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Lo sviluppatore può creare e modificare i modelli di dispositivo nell'applicazione IoT Central. Dopo aver pubblicato un modello di dispositivo, è possibile generare il dispositivo simulato o connettere dispositivi reali dal modello di dispositivo. I dispositivi simulati consentono di testare il comportamento dell'applicazione prima di collegare un dispositivo reale.

Per aggiungere un nuovo modello di dispositivo all'applicazione, selezionare la scheda **Modelli di dispositivo** nel riquadro sinistro.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Screenshot che mostra un elenco vuoto di modelli di dispositivo":::

Un modello di dispositivo definisce:

* I dati di telemetria inviati dal dispositivo.
* Le proprietà del dispositivo.
* I comandi a cui il dispositivo risponde.

### <a name="add-a-device-template"></a>Aggiungere un modello di dispositivo

Sono disponibili diverse opzioni per aggiungere un modello di dispositivo all'applicazione IoT Central. È possibile creare un modello da zero, importarlo da un file o selezionare un dispositivo nell'apposito catalogo. IoT Central supporta anche un approccio *device-first*, per cui l'applicazione importa automaticamente un modello da un repository la prima volta che un dispositivo si connette.

In questa guida di avvio rapido scegliere un dispositivo dal catalogo per importare il relativo modello.

I passaggi seguenti illustrano come usare il catalogo di dispositivi per importare il modello per un dispositivo **ESP32**. Questi dispositivi inviano dati di telemetria, ad esempio la temperatura, all'applicazione:

1. Per aggiungere un nuovo modello di dispositivo, selezionare **+ Nuovo** nella pagina **Modelli di dispositivo**.

1. Nella pagina **Seleziona il tipo** scorrere in basso fino a trovare il riquadro **ESP32-Azure IoT Kit** nella sezione **Usa un modello di dispositivo preconfigurato**.

1. Selezionare il riquadro **ESP32-Azure IoT Kit** e quindi **Avanti: Review** (Avanti: Revisione).

1. Nella pagina **Rivedi** selezionare **Crea**.

1. Dopo alcuni secondi, è possibile visualizzare il nuovo modello di dispositivo:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Screenshot che mostra il modello di dispositivo ESP32":::

    Il nome del modello è **Sensor Controller**. Il modello include componenti come **Sensor Controller**, **SensorTemp** e l'**interfaccia Informazioni dispositivo**. I componenti definiscono le funzionalità di un dispositivo ESP32. Le funzionalità includono dati di telemetria, proprietà e comandi.

### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo. Per aggiungere due proprietà cloud:

1. Selezionare **Proprietà cloud** e quindi **+ Aggiungi proprietà cloud**. Usare le informazioni nella tabella seguente per aggiungere due proprietà cloud al modello di dispositivo:

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | nessuno          | Data   |
    | Nome del cliente     | nessuno          | string |

1. Fare clic su **Salva** per salvare le modifiche:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Screenshot che mostra due proprietà cloud":::

## <a name="views"></a>Viste

Lo sviluppatore può personalizzare un'applicazione in modo da visualizzare le informazioni pertinenti sul dispositivo per un operatore. Le personalizzazioni consentono all'operatore di gestire i dispositivi connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi, inclusi i dati di telemetria inviati.

### <a name="default-views"></a>Visualizzazioni predefinite

La visualizzazioni predefinite consentono di iniziare subito a visualizzare le informazioni importanti sul dispositivo. Per il modello di dispositivo possono essere generate fino a tre visualizzazioni predefinite:

* La visualizzazione **Comandi** consente all'operatore di inviare comandi al dispositivo.
* La visualizzazione **Panoramica** usa grafici e metriche per visualizzare i dati di telemetria del dispositivo.
* La visualizzazione **Informazioni** mostra le proprietà del dispositivo.

Selezionare il nodo **Visualizzazioni** nel modello di dispositivo. Come si può notare, quando è stato aggiunto il modello, IoT Central ha generato automaticamente una visualizzazione **Panoramica** e una visualizzazione **Informazioni**.

Per aggiungere un nuovo modulo **Gestisci dispositivo** che un operatore può usare per gestire il dispositivo:

1. Selezionare il nodo **Visualizzazioni** e quindi il riquadro **Modifica dei dati del dispositivo e del cloud** per aggiungere una nuova visualizzazione.

1. Cambiare il nome del modulo in **Gestisci dispositivo**.

1. Selezionare le proprietà cloud **Nome cliente** e **Last Service Date** (Data ultimo utilizzo) e la proprietà **Target Temperature** (Temperatura obiettivo). Selezionare quindi **Aggiungi sezione**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Screenshot che mostra un nuovo modulo aggiunto al modello di dispositivo":::

1. Selezionare **Salva** per salvare il nuovo modulo.

## <a name="publish-device-template"></a>Pubblicare il modello di dispositivo

Prima di creare un dispositivo simulato o connettere un dispositivo reale, è necessario pubblicare il modello di dispositivo. Anche se IoT Central ha pubblicato il modello quando è stato creato, è necessario pubblicare la versione aggiornata.

Per pubblicare un modello di dispositivo:

1. Passare al modello di dispositivo **Sensor Controller** nella pagina **Modelli di dispositivo**.

1. Selezionare **Pubblica**:

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="Screenshot che mostra la posizione dell'icona Pubblica":::

1. Nella finestra di dialogo **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

Dopo la pubblicazione, il modello di dispositivo è visibile nella pagina **Dispositivi**. In un modello di dispositivo pubblicato non è possibile modificare un modello di dispositivo senza creare una nuova versione. È tuttavia possibile modificare le proprietà cloud, le personalizzazioni e le visualizzazioni senza creare una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.

## <a name="add-a-simulated-device"></a>Aggiungere un dispositivo simulato

Per aggiungere un dispositivo simulato all'applicazione, usare il modello di dispositivo **ESP32** creato.

1. Per aggiungere un nuovo dispositivo in qualità di operatore, scegliere **Dispositivi** nel riquadro di sinistra. La scheda **Dispositivi** include l'opzione **Tutti i dispositivi** e il modello di dispositivo **Sensor Controller** per il dispositivo ESP32. Selezionare **Sensor Controller**.

1. Per aggiungere un dispositivo DevKit simulato, selezionare **+ Nuovo**. Usare il valore di **ID dispositivo** suggerito o immetterne uno personalizzato. Un ID dispositivo può contenere lettere, numeri e il carattere `-`. È anche possibile immettere un nome per il nuovo dispositivo. Assicurarsi che l'opzione **Simulare questo dispositivo** sia impostata su **Sì** e quindi selezionare **Crea**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Screenshot che mostra il dispositivo Sensor Controller simulato":::

È ora possibile interagire con le visualizzazioni create per il modello di dispositivo usando dati simulati:

1. Selezionare il dispositivo simulato nella pagina **Dispositivi**

    * La visualizzazione **Panoramica** mostra un tracciato dei dati di telemetria simulati:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Screenshot che mostra la pagina di panoramica per il dispositivo simulato":::

    * La visualizzazione **Informazioni** mostra i valori delle proprietà.

    * La visualizzazione **Comandi** consente di eseguire comandi, come **reboot**, nel dispositivo.

    * La visualizzazione **Gestisci dispositivi** è il modulo creato per consentire all'operatore di gestire il dispositivo.

    * La visualizzazione **Dati non elaborati** consente di visualizzare i dati di telemetria non elaborati e i valori delle proprietà inviati dal dispositivo. Questa visualizzazione risulta utile per il debug dei dispositivi.

## <a name="use-a-simulated-device-to-improve-views"></a>Usare un dispositivo simulato per migliorare le visualizzazioni

Dopo aver creato un nuovo dispositivo simulato, il creatore può usare questo dispositivo per continuare a migliorare e arricchire le visualizzazioni per il modello di dispositivo.

1. Scegliere **Modelli di dispositivo** nel riquadro sinistro e selezionare il modello **Sensor Controller**.

1. Selezionare la visualizzazione da modificare, ad esempio **Panoramica** o creare una nuova visualizzazione. Selezionare **Configura dispositivo in anteprima** e quindi **Seleziona da un dispositivo in esecuzione**. Qui è possibile scegliere di non creare un dispositivo di anteprima, di usare un dispositivo reale configurato per i test oppure di usare un dispositivo esistente aggiunto in IoT Central.

1. Scegliere il dispositivo simulato nell'elenco. Selezionare **Applica**. È ora possibile visualizzare lo stesso dispositivo simulato nell'esperienza di creazione di visualizzazioni dei modelli di dispositivo. Questa visualizzazione è utile per i grafici e altri elementi visivi.

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="Screenshot che mostra un dispositivo in anteprima configurato":::

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un modello di dispositivo **Sensor Controller** per un dispositivo ESP32 ed è stato aggiunto un dispositivo simulato all'applicazione.

Per altre informazioni sul monitoraggio dei dispositivi connessi all'applicazione, continuare con l'avvio rapido:

> [!div class="nextstepaction"]
> [Configurare regole e azioni](./quick-configure-rules.md)
