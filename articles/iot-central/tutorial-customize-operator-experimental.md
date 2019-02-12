---
title: Personalizzare le visualizzazioni dell'operatore in Azure IoT Central | Microsoft Docs
description: Come creatore, l'utente può personalizzare le visualizzazioni dell'operatore nell'applicazione Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/29/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 267a619fe32a8d4af0ee9cc8a5001d7a321c3098
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765150"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Esercitazione: Personalizzare la visualizzazione dell'operatore in Azure IoT Central (nuova interfaccia utente)

Questa esercitazione illustra ai creatori come personalizzare la visualizzazione dell'operatore dell'applicazione. Quando si apporta una modifica all'applicazione da creatori, è possibile visualizzare in anteprima la visualizzazione dell'operatore nell'applicazione Microsoft Azure IoT Central.

In questa esercitazione si personalizza l'applicazione in modo che visualizzi informazioni sul condizionatore connesso utili all'operatore. Le personalizzazioni consentono all'operatore di gestire i condizionatori connessi all'applicazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il dashboard del dispositivo
> * Configurare il layout delle impostazioni del dispositivo
> * Configurare il layout delle proprietà del dispositivo
> * Visualizzare in anteprima il dispositivo come operatore
> * Configurare l'home page predefinita
> * Visualizzare in anteprima l'home page predefinita come operatore

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, si consiglia di completare le due esercitazioni precedenti:

* [Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
* [Configurare le regole e le azioni per il dispositivo](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="configure-your-device-dashboard"></a>Configurare il dashboard del dispositivo

I creatori possono definire le informazioni visualizzate sul dashboard di un dispositivo. Nell'esercitazione su come [definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) sono stati aggiunti un grafico a linee e altre informazioni al dashboard di **Connected Air Conditioner**.

1. Per modificare il modello di dispositivo **Connected Air Conditioner**, scegliere **Modelli di dispositivo** nel menu di spostamento a sinistra:

    ![Pagina Modelli di dispositivo](media/tutorial-customize-operator-experimental/devicetemplates.png)

2. Per personalizzare il dashboard del dispositivo, fare clic sul modello di dispositivo **Connected Air Conditioner (1.0.0)** creato nell'esercitazione su come [definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

3. Per modificare il dashboard, selezionare la scheda **Dashboard**.

4. Per aggiungere un riquadro Indicatore di prestazioni chiave (KPI) al dashboard, scegliere **KPI**:

    Usare le informazioni nella tabella seguente per definire gli indicatori KPI:

    | Impostazione     | Valore |
    | ----------- | ----- |
    | NOME        | Temperatura massima |
    | Intervallo di tempo  | 1 settimana precedente |
    | Tipo di misurazione | Telemetria |
    | Misura | temperatura |
    | Aggregazione | Massima |
    | Visibilità  | Attivato |

    ![Aggiungere indicatori KPI](media/tutorial-customize-operator-experimental/addkpi.png)

5. Fare clic su **Save**. È ora possibile visualizzare il riquadro dell'indicatore KPI nel dashboard:

    ![Riquadro dell'indicatore KPI](media/tutorial-customize-operator-experimental/temperaturekpi.png)

6. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro. È possibile trascinare il riquadro in una nuova posizione o modificarne le dimensioni.

## <a name="configure-your-settings-layout"></a>Configurare il layout delle impostazioni

I creatori possono anche configurare la visualizzazione delle impostazioni del dispositivo da parte dell'operatore. La scheda delle impostazioni verrà usata da un operatore per configurare un dispositivo, ad esempio per impostare la temperatura di destinazione per il condizionatore connesso.

1. Per modificare il layout delle impostazioni per il condizionatore connesso, scegliere la scheda **Impostazioni**.

2. È possibile spostare e ridimensionare i riquadri delle impostazioni:

    ![Modificare il layout delle impostazioni](media/tutorial-customize-operator-experimental/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurare il layout delle proprietà

Oltre ai dashboard e alle impostazioni, è possibile configurare anche la visualizzazione delle proprietà del dispositivo da parte dell'operatore. La scheda delle proprietà verrà usata da un operatore per gestire i metadati del dispositivo, ad esempio per visualizzare il numero di serie di un dispositivo o per aggiornare i dettagli di contatto del produttore.

1. Per modificare il layout delle proprietà per il condizionatore connesso, scegliere la scheda **Proprietà**.

2. È possibile spostare e ridimensionare i campi delle proprietà:

    ![Modificare il layout delle proprietà](media/tutorial-customize-operator-experimental/propertieslayout.png)

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Visualizzare in anteprima il condizionatore connesso come operatore

Usare la pagina **Modelli di dispositivo** per personalizzare le schede Dashboard, Impostazioni e Proprietà per un operatore. Usare la pagina **Device Explorer** per visualizzare e usare il modello di dispositivo.

1. Per visualizzare e usare come operatore il modello del condizionatore connesso, passare alla pagina **Device Explorer** e scegliere il dispositivo simulato generato da IoT Central dal modello:

    ![Visualizzare e usare il modello del dispositivo](media/tutorial-customize-operator-experimental/usetemplate.png)

2. Per aggiornare la località del dispositivo, scegliere **Proprietà** e modificare il valore nel relativo riquadro. Fare quindi clic su **Save** (Salva):

    ![Modificare il valore di una proprietà](media/tutorial-customize-operator-experimental/editproperty.png)

3. Per inviare un'impostazione al condizionatore connesso, scegliere **Impostazioni**, modificare il valore di un'impostazione in un riquadro e scegliere **Aggiorna**:

    ![Inviare un'impostazione al dispositivo](media/tutorial-customize-operator-experimental/sendsetting.png)

    Quando il dispositivo invia un acknowledgement per il nuovo valore dell'impostazione, quest'ultima viene visualizzata come **sincronizzata** nel riquadro.

4. Gli operatori possono visualizzare il dashboard del dispositivo come configurato dal creatore:

    ![Visualizzazione dell'operatore del dashboard del dispositivo](media/tutorial-customize-operator-experimental/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Configurare l'home page predefinita

Quando un creatore o un operatore accede a un'applicazione Azure IoT Central, vedrà l'home page. I creatori possono configurare i contenuti dell'home page in modo che includa quelli più utili per gli operatori.

1. Per personalizzare la home page predefinita, passare a **Home page** e selezionare **Modifica** in alto a destra nella pagina. A sinistra verrà visualizzato un pannello con un elenco degli oggetti che è possibile aggiungere a **Home page**:

    ![Pagina Application Builder (Generatore applicazioni)](media/tutorial-customize-operator-experimental/builderhome.png)

2. Per personalizzare **Home page**, aggiungere riquadri da **Libreria**. Scegliere **Collegamento** e aggiungere i dettagli del sito Web dell'organizzazione. Fare quindi clic su **Save** (Salva):

    ![Aggiungere un collegamento all'home page](media/tutorial-customize-operator-experimental/addlink.png)

    > [!NOTE]
    > È anche possibile aggiungere collegamenti alle pagine all'interno dell'applicazione Azure IoT Central. Ad esempio, è possibile aggiungere un collegamento al dashboard o alla pagina di impostazioni del dispositivo.

3. Facoltativamente, scegliere **Immagine** e caricare un'immagine da visualizzare nell'home page. Un'immagine può avere un URL a cui essere reindirizzato facendo clic su di essa:

    ![Aggiungere un'immagine all'home page](media/tutorial-customize-operator-experimental/addimage.png)

    Per altre informazioni, vedere [Come preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="preview-the-default-home-page-as-an-operator"></a>Visualizzare in anteprima l'home page predefinita come operatore

Per visualizzare in anteprima la home page come operatore, fare clic su **Chiudi** in alto a destra nella pagina:

![Attivare/Disattivare la Modalità progettazione](media/tutorial-customize-operator-experimental/operatorviewhome.png)

È possibile fare clic sui riquadri di collegamento e immagine per passare agli URL impostati come creatore.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come personalizzare la visualizzazione dell'applicazione da parte dell'operatore.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Configurare il dashboard del dispositivo
> * Configurare il layout delle impostazioni del dispositivo
> * Configurare il layout delle proprietà del dispositivo
> * Visualizzare in anteprima il dispositivo come operatore
> * Configurare l'home page predefinita
> * Visualizzare in anteprima l'home page predefinita come operatore

Ora che si è appreso come personalizzare la visualizzazione dell'applicazione da parte dell'operatore, i passaggi successivi suggeriti sono:

* [Monitorare i dispositivi come operatore](tutorial-monitor-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Aggiungere un nuovo dispositivo all'applicazione come operatore e sviluppatore del dispositivo](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
