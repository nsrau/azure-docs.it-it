---
title: Personalizzare le visualizzazioni dell'operatore in Azure IoT Central | Microsoft Docs
description: Come creatore, l'utente può personalizzare le visualizzazioni dell'operatore nell'applicazione Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: dc3543af0a10c83d8ca212957a6345c8a70cd58b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279942"
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
> * Configurare il dashboard dell'applicazione predefinito
> * Visualizzare in anteprima il dashboard dell'applicazione predefinito come operatore

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, si consiglia di completare le due esercitazioni precedenti:

* [Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type.md).
* [Configurare le regole e le azioni per il dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurare il dashboard del dispositivo

I creatori possono definire le informazioni visualizzate sul dashboard di un dispositivo. Nell'esercitazione su come [definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type.md) sono stati aggiunti un grafico a linee e altre informazioni al dashboard di **Connected Air Conditioner**.

1. Per modificare il modello di dispositivo **Connected Air Conditioner**, scegliere **Modelli di dispositivo** nel menu di spostamento a sinistra:

    ![Pagina Modelli di dispositivo](media/tutorial-customize-operator/devicetemplates.png)

2. Per personalizzare il dashboard del dispositivo, selezionare il modello di dispositivo **Connected Air Conditioner (1.0.0)** creato nell'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type.md).

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

    ![Aggiungere indicatori KPI](media/tutorial-customize-operator/addkpi.png)

5. Selezionare **Salva**. È ora possibile visualizzare il riquadro dell'indicatore KPI nel dashboard:

    ![Riquadro dell'indicatore KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro. È possibile trascinare il riquadro in una nuova posizione o modificarne le dimensioni.

## <a name="configure-your-settings-layout"></a>Configurare il layout delle impostazioni

I creatori possono anche configurare la visualizzazione delle impostazioni del dispositivo da parte dell'operatore. La scheda delle impostazioni verrà usata da un operatore per configurare un dispositivo, ad esempio per impostare la temperatura di destinazione per il condizionatore connesso.

1. Per modificare il layout delle impostazioni per il condizionatore connesso, scegliere la scheda **Impostazioni**.

2. È possibile spostare e ridimensionare i riquadri delle impostazioni:

    ![Modificare il layout delle impostazioni](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurare il layout delle proprietà

Oltre ai dashboard e alle impostazioni, è possibile configurare anche la visualizzazione delle proprietà del dispositivo da parte dell'operatore. La scheda delle proprietà verrà usata da un operatore per gestire i metadati del dispositivo, ad esempio per visualizzare il numero di serie di un dispositivo o per aggiornare i dettagli di contatto del produttore.

1. Per modificare il layout delle proprietà per il condizionatore connesso, scegliere la scheda **Proprietà**.

2. È possibile spostare e ridimensionare i campi delle proprietà:

    ![Modificare il layout delle proprietà](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Visualizzare il dispositivo in anteprima

Usare la pagina **Modelli di dispositivo** per personalizzare le schede Dashboard, Impostazioni e Proprietà per un operatore. Usare la pagina **Device Explorer** per visualizzare e usare il modello di dispositivo.

1. Per visualizzare e usare come operatore il modello del condizionatore connesso, passare alla pagina **Device Explorer** e scegliere il dispositivo simulato generato da IoT Central dal modello:

    ![Visualizzare e usare il modello del dispositivo](media/tutorial-customize-operator/usetemplate.png)

2. Per aggiornare la località del dispositivo, scegliere **Proprietà** e modificare il valore nel relativo riquadro. Selezionare quindi **Salva**:

    ![Modificare il valore di una proprietà](media/tutorial-customize-operator/editproperty.png)

3. Per inviare un'impostazione al condizionatore connesso, scegliere **Impostazioni**, modificare il valore di un'impostazione in un riquadro e scegliere **Aggiorna**:

    ![Inviare un'impostazione al dispositivo](media/tutorial-customize-operator/sendsetting.png)

    Quando il dispositivo invia un acknowledgement per il nuovo valore dell'impostazione, quest'ultima viene visualizzata come **sincronizzata** nel riquadro.

4. Gli operatori possono visualizzare il dashboard del dispositivo come configurato dal creatore:

    ![Visualizzazione dell'operatore del dashboard del dispositivo](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Configurare il dashboard predefinito

Quando un creatore o un operatore accede a un'applicazione Azure IoT Central, vede il dashboard dell'applicazione. I creatori possono configurare i contenuti del dashboard predefinito in modo che includa quelli più utili e pertinenti per gli operatori.

> [!NOTE]
> Anche gli utenti possono creare dashboard personali e sceglierne uno come predefinito.

1. Per personalizzare il dashboard dell'applicazione predefinito, passare alla pagina **Dashboard** e selezionare **Modifica** in alto a destra nella pagina. Viene visualizzato un pannello con una raccolta di oggetti che è possibile aggiungere al dashboard.

    ![Pagina Dashboard](media/tutorial-customize-operator/builderhome.png)

2. Per personalizzare il dashboard, aggiungere riquadri da **Libreria**. Scegliere **Collegamento** e aggiungere i dettagli del sito Web dell'organizzazione. Quindi scegliere **Salva**:

    ![Aggiungere un collegamento al dashboard](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > È anche possibile aggiungere collegamenti alle pagine all'interno dell'applicazione Azure IoT Central. Ad esempio, è possibile aggiungere un collegamento al dashboard o alla pagina di impostazioni del dispositivo.

3. Facoltativamente, scegliere **Immagine** e caricare un'immagine da visualizzare nel dashboard. Un'immagine può avere un URL a cui si viene reindirizzati quando la si seleziona:

    ![Aggiungere un'immagine al dashboard](media/tutorial-customize-operator/addimage.png)

    Per altre informazioni, vedere [Come preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Visualizzare il dashboard in anteprima

Per visualizzare il dashboard dell'applicazione in anteprima come operatore, selezionare **Fine** in alto a destra nella pagina.

![Attivare/Disattivare la Modalità progettazione](media/tutorial-customize-operator/operatorviewhome.png)

È possibile selezionare i riquadri di collegamento e immagine per passare agli URL impostati dal creatore.

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

* [Monitorare i dispositivi come operatore](tutorial-monitor-devices.md)
* [Aggiungere un nuovo dispositivo all'applicazione come operatore e sviluppatore del dispositivo](tutorial-add-device.md)