---
title: Definire un nuovo tipo di dispositivo in Azure IoT Central | Microsoft Docs
description: Questa esercitazione illustra come creare un nuovo tipo di dispositivo nell'applicazione Azure IoT Central. Il creatore definisce i dati di telemetria, lo stato, le proprietà e i comandi per il tipo.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 660b2ead146695657ae13444cb7936eff8224f3a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099509"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Esercitazione: Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra come usare un modello di dispositivo per definire un nuovo tipo di dispositivo nell'applicazione Microsoft Azure IoT Central. Un modello di dispositivo definisce le funzionalità del dispositivo. Le funzionalità includono i dati di telemetria inviati dal dispositivo, le proprietà del dispositivo e i comandi a cui un dispositivo risponde.

In questa esercitazione si crea un modello di dispositivo di tipo **sensore ambientale**. Un dispositivo di tipo sensore ambientale:

* Invia dati di telemetria, ad esempio la temperatura.
* Segnala le proprietà specifiche del dispositivo, ad esempio il livello di luminosità.
* Risponde a comandi quali l'attivazione e la disattivazione.
* Segnala le proprietà generiche del dispositivo, ad esempio la versione del firmware e il numero di serie.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un nuovo modello di dispositivo.
> * Importare un modello di funzionalità di dispositivo.
> * Creare proprietà cloud.
> * Definire una visualizzazione per i dati di telemetria del dispositivo.
> * Pubblicare il modello di dispositivo.
> * Creare un dispositivo simulato per il modello di dispositivo.
> * Visualizzare il dispositivo simulato.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessaria un'applicazione Azure IoT Central. Se è stato completato l'avvio rapido [Creare un'applicazione Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), è possibile usare nuovamente l'applicazione creata nella guida. In caso contrario, completare i passaggi seguenti per creare un'applicazione Azure IoT Central vuota:

1. Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral).

1. Se si ha una sottoscrizione di Azure, accedere con le credenziali usate per tale sottoscrizione, altrimenti accedere usando un account Microsoft:

    ![Immettere l'account dell'organizzazione](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Per iniziare a creare una nuova applicazione Azure IoT Central, selezionare **Nuova applicazione**.

Per creare una nuova applicazione Azure IoT Central che usa le funzionalità di anteprima, tra cui Plug and Play IoT:

1. Scegliere **Versione di valutazione**. Non è necessaria una sottoscrizione di Azure per creare una versione di valutazione dell'applicazione.

    Per altre informazioni su directory e sottoscrizioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Scegliere **Anteprima applicazione**.

1. È facoltativamente possibile scegliere un nome descrittivo per l'applicazione, ad esempio **Condizionatori Contoso**. Azure IoT Central genera un prefisso URL univoco. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

1. Se si sta creando una versione di valutazione dell'applicazione, è necessario specificare i dettagli di contatto.

1. Selezionare **Create** (Crea).

    ![Pagina Crea applicazione di Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

È anche necessaria una copia locale del file **EnvironmentalSensorInline.capabilitymodel.json** che contiene il modello di funzionalità di dispositivo [Plug and Play IoT](../iot-pnp/overview-iot-plug-and-play.md). È possibile scaricarlo [qui](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Fare clic con il pulsante destro del mouse sulla pagina e scegliere **Salva con nome**.

Dopo aver scaricato il file, aprirlo in un editor di testo e sostituire le due istanze di `<YOUR_COMPANY_NAME_HERE>` con il proprio nome. Usare solo i caratteri a-z, A-Z, 0-9 e il carattere di sottolineatura.

## <a name="create-a-template"></a>Creare un modello

L'utente creatore può creare e modificare i modelli di dispositivo nell'applicazione. Dopo la pubblicazione di un modello di dispositivo, è possibile generare un dispositivo simulato o connettere dispositivi reali che implementano il modello di dispositivo. I dispositivi simulati consentono di testare il comportamento dell'applicazione prima di collegare un dispositivo reale.

Per aggiungere un nuovo modello di dispositivo all'applicazione, passare alla pagina **Modelli di dispositivo**. A tale scopo, selezionare la scheda **Modelli di dispositivo** nel menu di spostamento a sinistra.

![Pagina Modelli di dispositivo](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Aggiungere un modello di funzionalità di dispositivo

Ci sono diverse opzioni per la creazione di un modello di funzionalità di dispositivo in IoT Central. È possibile scegliere di creare un modello personalizzato da zero, eseguire l'importazione da un file, selezionare in un catalogo di dispositivi o connettere un dispositivo Plug and Play IoT tramite una connessione del dispositivo, in cui il modello di funzionalità di dispositivo è stato pubblicato nel repository pubblico. In questa esercitazione si importa un modello di funzionalità di dispositivo da un file.

La procedura seguente illustra come importare il modello di funzionalità per un dispositivo di tipo **sensore ambientale**. Questi dispositivi inviano dati di telemetria, ad esempio la temperatura, all'applicazione:

1. Per aggiungere un nuovo modello di dispositivo, selezionare **+ Nuovo** nella pagina **Modelli di dispositivo**.

1. Scegliere **Personalizzato** dall'elenco di modelli.

1. Immettere **Sensore ambientale** come nome per il modello di dispositivo.

1. Scegliere **Importa modello di funzionalità** per creare un nuovo modello di funzionalità di dispositivo da un file JSON. Passare alla cartella in cui è stato salvato il file **EnvironmentalSensorInline.capabilitymodel.json** nel computer locale. Selezionare il file **EnvironmentalSensorInline.capabilitymodel.json** e quindi **Apri**. Il modello di funzionalità del sensore ambientale include le interfacce **Sensore ambientale** e **Informazioni dispositivo**:

    ![Modello di funzionalità di dispositivo di tipo sensore ambientale](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Queste interfacce definiscono le funzionalità di un dispositivo di tipo **sensore ambientale**. Le funzionalità includono i dati di telemetria inviati dal dispositivo, le proprietà segnalate dal dispositivo e i comandi a cui un dispositivo risponde.

### <a name="add-cloud-properties"></a>Aggiungere proprietà cloud

Un modello di dispositivo può includere proprietà cloud. Le proprietà cloud esistono solo nell'applicazione IoT Central e non vengono mai inviate a o ricevute da un dispositivo.

1. Selezionare **Proprietà cloud** e quindi **+ Aggiungi proprietà cloud**. Usare le informazioni nella tabella seguente per aggiungere proprietà cloud al modello di dispositivo.

    | Nome visualizzato      | Tipo semantico | SCHEMA |
    | ----------------- | ------------- | ------ |
    | Data ultimo utilizzo | Nessuna          | Data   |
    | Nome del cliente     | Nessuna          | string |

1. Fare clic su **Salva** per salvare le modifiche:

    ![Proprietà cloud](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Aggiungere personalizzazioni

Usare le personalizzazioni quando è necessario modificare un'interfaccia o aggiungere funzionalità specifiche di IoT Central a una funzionalità che non richiede la creazione di una nuova versione del modello di funzionalità di dispositivo. È possibile personalizzare i campi quando il modello di funzionalità è in uno stato di bozza o pubblicato. È possibile personalizzare solo i campi che non compromettono la compatibilità dell'interfaccia. Ad esempio, è possibile:

- Personalizzare il nome visualizzato e le unità di una funzionalità.
- Aggiungere un colore predefinito da usare quando il valore viene visualizzato in un grafico.
- Specificare i valori iniziale, minimo e massimo per una proprietà.

Non è possibile personalizzare il tipo o il nome della funzionalità.


## <a name="create-views"></a>Creare viste

Il creatore può personalizzare un'applicazione in modo da visualizzare le informazioni sul dispositivo sensore ambientale utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i sensori ambientali connessi all'applicazione. È possibile creare due tipi di visualizzazioni che un operatore può usare per interagire con i dispositivi:

* Moduli per visualizzare e modificare le proprietà del dispositivo e cloud.
* Dashboard per visualizzare i dispositivi.

### <a name="generate-default-views"></a>Generare le visualizzazioni predefinite

La generazione di visualizzazioni predefinite è un modo rapido per iniziare a visualizzare le informazioni importanti sul dispositivo. Per il modello di dispositivo possono essere generate fino a tre visualizzazioni predefinite:

* La visualizzazione **Comandi** consente all'operatore di inviare comandi al dispositivo.
* La visualizzazione **Panoramica** usa grafici e metriche per visualizzare i dati di telemetria del dispositivo.
* La visualizzazione **Informazioni** mostra le proprietà del dispositivo.

Dopo aver selezionato **Genera visualizzazioni predefinite**, le visualizzazioni vengono aggiunte automaticamente nella sezione **Visualizzazioni** del modello di dispositivo.

### <a name="configure-a-view-to-visualize-devices"></a>Configurare una visualizzazione per visualizzare i dispositivi

Un dashboard del dispositivo consente a un operatore di visualizzare un dispositivo usando grafici e metriche. I creatori possono definire le informazioni visualizzate sul dashboard di un dispositivo. È possibile definire più dashboard per i dispositivi. Per creare un dashboard per visualizzare i dati di telemetria del sensore ambientale, selezionare **Visualizzazioni** e quindi **Visualizzazione del dispositivo**:

1. Le proprietà del dispositivo, le proprietà cloud, le opzioni statiche e i dati di telemetria sono elencati in **Proprietà**. È possibile trascinare e rilasciare questi elementi nella visualizzazione. Trascinare la proprietà **Brightness Level** (Livello luminosità) nella visualizzazione. È possibile configurare il riquadro usando l'icona a forma di ingranaggio.

1. Per aggiungere un grafico che traccia i dati di telemetria, selezionare **Umidità** e **Temperatura** e quindi selezionare **Combina**. Per visualizzare questo grafico in un formato diverso, ad esempio come grafico a torta o grafico a barre, selezionare il pulsante **Modifica visualizzazione** nella parte superiore del riquadro.

1. Fare clic su **Salva** per salvare la visualizzazione:

È possibile aggiungere altri riquadri che mostrano proprietà o valori di telemetria diversi. È anche possibile aggiungere testo statico, collegamenti e immagini. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro e trascinare il riquadro in una nuova posizione o ridimensionarlo.

### <a name="add-a-device-form"></a>Aggiungere un modulo di dispositivo

Un modulo di dispositivo consente a un operatore di modificare le proprietà del dispositivo scrivibili e le proprietà cloud. Il creatore può definire più moduli e scegliere le proprietà del dispositivo e cloud da visualizzare in ogni modulo. È anche possibile visualizzare le proprietà del dispositivo di sola lettura in un modulo.

Per creare un modulo per la visualizzazione e la modifica delle proprietà del sensore ambientale:

1. Passare a **Viste** nel modello **Sensore ambientale**. Selezionare il riquadro **Modifica dei dati del dispositivo e del cloud** per aggiungere una nuova vista.

1. Immettere il nome di modulo **Proprietà sensore ambientale**.

1. Trascinare le proprietà cloud **Nome cliente** e **Data ultima assistenza** nella sezione esistente del modulo.

1. Selezionare le proprietà del dispositivo **Brightness Level** (Livello luminosità) e **Stato dispositivo**. Selezionare quindi **Aggiungi sezione**. Modificare il titolo della sezione in **Proprietà sensore**. Selezionare **Applica**.

1. Selezionare le proprietà del dispositivo **Modello del dispositivo**, **Versione del software**, **Produttore** e **Produttore processore**. Selezionare quindi **Aggiungi sezione**. Modificare il titolo della sezione in **Proprietà dispositivo**. Selezionare **Applica**.

1. Fare clic su **Salva** per salvare la visualizzazione.

## <a name="publish-device-template"></a>Pubblicare il modello di dispositivo

Prima di poter creare un sensore ambientale simulato o di connettere un sensore ambientale reale, è necessario pubblicare il modello di dispositivo.

Per pubblicare un modello di dispositivo:

1. Passare al modello di dispositivo dalla pagina **Modelli di dispositivo**.

1. Selezionare **Pubblica**.

1. Nella finestra di dialogo **Publish a Device Template** (Pubblica un modello di dispositivo) scegliere **Pubblica**:

    ![Modello pubblicato](media/tutorial-define-device-type-pnp/publishedmodel.png)

Dopo la pubblicazione, un modello di dispositivo viene visualizzato nella pagina **Dispositivi** ed è visibile all'operatore. In un modello di dispositivo pubblicato non è possibile modificare un modello di funzionalità di dispositivo senza creare una nuova versione. È tuttavia possibile apportare aggiornamenti a proprietà cloud, personalizzazioni e visualizzazioni in un modello di dispositivo pubblicato senza creare una nuova versione. Dopo aver apportato le modifiche, selezionare **Pubblica** per eseguire il push delle modifiche all'operatore.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare un nuovo modello di dispositivo
* Importare un modello di funzionalità di dispositivo.
* Creare proprietà cloud.
* Creare personalizzazioni.
* Definire una visualizzazione per i dati di telemetria del dispositivo.
* Pubblicare il modello di dispositivo.

Ora che è stato creato un modello di dispositivo nell'applicazione Azure IoT Central, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Aggiungere un dispositivo](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
