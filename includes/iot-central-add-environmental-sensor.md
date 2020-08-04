---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002047"
---
## <a name="create-a-device-template"></a>Creare un modello di dispositivo

Nel computer locale creare una cartella denominata `environmental-sensor`.

Scaricare il file JSON del [Modello di funzionalità sensore ambientale](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) e salvarlo nella cartella `environmental-sensor`.

Usare un editor di testo e sostituire le due istanze di `{YOUR_COMPANY_NAME_HERE}` con il nome dell'azienda nel file `EnvironmentalSensorInline.capabilitymodel.json` scaricato. Usare solo i caratteri a-z, A-Z, 0-9 e il carattere di sottolineatura.

Nell'applicazione Azure IoT Central creare un modello di dispositivo denominato *sensore ambientale* importando il file del modello di funzionalità di dispositivo `EnvironmentalSensorInline.capabilitymodel.json`:

![Modello di dispositivo con modello di funzionalità di dispositivo importato](./media/iot-central-add-environmental-sensor/device-template.png)

Il modello di funzionalità di dispositivo include due interfacce: l'interfaccia **Informazioni sul dispositivo** standard e l'interfaccia **Sensore ambientale** personalizzata. L'interfaccia **Sensore ambientale** definisce le funzionalità seguenti:

| Type | Nome visualizzato | Descrizione |
| ---- | ------------ | ----------- |
| Proprietà | Stato del dispositivo     | Stato del dispositivo. Sono disponibili due stati, online/offline. |
| Proprietà (scrivibile) | Nome del cliente    | Nome del cliente che attualmente gestisce il dispositivo. |
| Proprietà (scrivibile) | Livello luminosità | Livello di luminosità dell'illuminazione nel dispositivo. Può essere specificato come 1 (alto), 2 (medio), 3 (basso). |
| Telemetria | Temperatura | Temperatura corrente rilevata dal dispositivo. |
| Telemetria | Umidità    | Umidità corrente rilevata dal dispositivo. |
| Comando | blink          | Attiva il lampeggiamento del LED sul dispositivo per l'intervallo di tempo specificato. |
| Comando | turnon         | Accende il LED sul dispositivo. |
| Comando | turnoff        | Spegne il LED sul dispositivo. |
| Comando | rundiagnostics | Questo comando asincrono avvia un'esecuzione di diagnostica nel dispositivo. |

Per personalizzare la modalità di visualizzazione della proprietà **Stato del dispositivo** nell'applicazione IoT Central, selezionare **Personalizza** nel modello di dispositivo. Espandere la voce **Stato del dispositivo**, immettere _Online_ come **Nome True** e _Offline_ come **Nome False**. Salvare quindi le modifiche:

![Personalizzare il modello di dispositivo](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Creare viste

Le visualizzazioni consentono di interagire con i dispositivi connessi all'applicazione IoT Central. Le visualizzazioni possono ad esempio mostrare dati di telemetria e le proprietà oppure consentire la modifica di proprietà scrivibili e cloud. Le visualizzazioni fanno parte di un modello di dispositivo.

Per aggiungere alcune visualizzazioni predefinite al modello di dispositivo **Sensore ambientale**, passare al modello di dispositivo, selezionare **Visualizzazioni** e quindi selezionare il riquadro **Genera visualizzazioni predefinite**. Assicurarsi che le opzioni **Panoramica** e **Informazioni su** siano impostate su **Sì** e quindi selezionare **Genera visualizzazioni dashboard predefinite**. Nel modello sono ora definite due visualizzazioni predefinite.

L'interfaccia **Sensore ambientale** include due proprietà scrivibili, ovvero **Nome cliente** e **Livello luminosità**. Per creare una visualizzazione, è possibile modificare queste proprietà:

1. Selezionare **Visualizzazioni** e quindi il riquadro **Modifica dei dati del dispositivo e del cloud**.

1. Immettere _Properties_ come nome del modulo.

1. Selezionare le proprietà del dispositivo **Brightness Level** (Livello luminosità) e **Nome cliente**. Fare quindi clic su **Aggiungi sezione**.

1. Salvare le modifiche.

![Aggiungere una visualizzazione per abilitare la modifica delle proprietà](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>Pubblicare il modello

Prima di aggiungere un dispositivo che usa il modello di dispositivo **Sensore ambientale**, è necessario pubblicarlo.

Nel modello di dispositivo selezionare **Pubblica**. Nel pannello **Pubblica questo modello di dispositivo nell'applicazione** selezionare **Pubblica**.

Per verificare che il modello sia pronto per l'uso, passare alla pagina **Dispositivi** nell'applicazione IoT Central. La sezione **Dispositivi** visualizza un elenco dei dispositivi pubblicati nell'applicazione:

![Modelli pubblicati nella pagina Dispositivi](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale al modello di dispositivo creato nella sezione precedente:

1. Nella pagina **Dispositivi** selezionare il modello di dispositivo **Sensore ambientale**.

1. Selezionare **+ Nuovo**.

1. Nella finestra di dialogo **Creare un nuovo dispositivo** verificare che **Sensore ambientale** sia il tipo di modello e che l'opzione **Simulare questo dispositivo?** sia impostata su **No**.

1. Selezionare quindi **Crea**.

Fare clic sul nome del dispositivo e quindi selezionare **Connetti**. Prendere nota delle informazioni di connessione del dispositivo nella pagina **Connessione del dispositivo**: **Ambito ID**, **ID dispositivo** e **Chiave primaria**. Questi valori saranno necessari per la creazione del codice del dispositivo:

![Informazioni di connessione del dispositivo](./media/iot-central-add-environmental-sensor/device-connection.png)
