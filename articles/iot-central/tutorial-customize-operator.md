---
title: Personalizzare le visualizzazioni dell'operatore in Azure IoT Central | Microsoft Docs
description: Come creatore, l'utente può personalizzare le visualizzazioni dell'operatore nell'applicazione Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 070a6ee4bc712b2dbec598cdb9be5c324895c033
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154982"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Esercitazione: Personalizzare la visualizzazione dell'operatore in Azure IoT Central

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

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, si consiglia di completare le due esercitazioni precedenti:

* [Definire un nuovo tipo di dispositivo nell'applicazione Azure IoT Central](tutorial-define-device-type.md).
* [Configurare le regole e le azioni per il dispositivo](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurare il dashboard del dispositivo

I creatori possono definire le informazioni visualizzate sul dashboard di un dispositivo. Nell'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type.md) sono stati aggiunti un grafico a linee e altre informazioni al dashboard del **Condizionatore connesso 1**.

1. Per modificare il modello di dispositivo **Condizionatore connesso**, scegliere **Explorer** nel menu di spostamento a sinistra:

    ![Pagina Explorer](media/tutorial-customize-operator/explorer.png)

2. Per iniziare a personalizzare il dashboard del condizionatore connesso, selezionare il modello di dispositivo **Condizionatore connesso (1.0.0)**. Scegliere il dispositivo **Condizionatore connesso 1** creato nell'esercitazione [Definire un nuovo tipo di dispositivo nell'applicazione](tutorial-define-device-type.md):

    ![Selezionare il condizionatore connesso](media/tutorial-customize-operator/selectdevice.png)

    Dall'interno di un dispositivo, ad esempio **Condizionatore connesso 1**, è possibile selezionare **Modifica modello** per apportare una modifica al modello sottostante. Per altre informazioni, vedere [Creare una nuova versione del modello di dispositivo](howto-version-devicetemplate.md).

3. Per modificare il dashboard, scegliere **Dashboard** e selezionare **Modifica modello**:

    ![Pagina dashboard del modello di dispositivo](media/tutorial-customize-operator/dashboard.png)

4. Per aggiungere un riquadro Indicatore di prestazioni chiave (KPI) al dashboard, scegliere **KPI**:

    ![Aggiungere indicatori KPI](media/tutorial-customize-operator/addkpi.png)

    Usare le informazioni nella tabella seguente per definire gli indicatori KPI:

    | Impostazione     | Valore |
    | ----------- | ----- |
    | NOME        | Temperatura massima |
    | Misura | temperatura |
    | Aggregazione | Massima |
    | Intervallo di tempo  | 1 settimana precedente |

5. Scegliere **Salva**. È ora possibile visualizzare il riquadro dell'indicatore KPI nel dashboard:

    ![Riquadro dell'indicatore KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Per spostare o ridimensionare un riquadro nel dashboard, spostare il puntatore del mouse sul riquadro. È possibile trascinare il riquadro in una nuova posizione o modificarne le dimensioni:

    ![Modificare il layout del dashboard](media/tutorial-customize-operator/dashboardlayout.png)

7. Dopo aver apportato tutte le modifiche desiderate, fare clic su **Fine**.

## <a name="configure-your-settings-layout"></a>Configurare il layout delle impostazioni

I creatori possono anche configurare la visualizzazione delle impostazioni del dispositivo da parte dell'operatore. Un operatore usa la pagina di impostazioni del dispositivo per configurarlo. Un operatore usa ad esempio la pagina delle impostazioni per impostare la temperatura desiderata per il condizionatore connesso.

1. Per modificare il layout delle impostazioni per il condizionatore connesso, scegliere **Impostazioni** e selezionare **Modifica modello**:

    ![Pagina Impostazioni](media/tutorial-customize-operator/settings.png)

2. È possibile spostare e ridimensionare i riquadri delle impostazioni:

    ![Modificare il layout delle impostazioni](media/tutorial-customize-operator/settingslayout.png)

3. Dopo aver apportato tutte le modifiche desiderate, fare clic su **Fine**.

> [!NOTE]
> In modalità **Modifica modello** non è possibile modificare i valori delle impostazioni.

## <a name="configure-your-properties-layout"></a>Configurare il layout delle proprietà

Oltre ai dashboard e alle impostazioni, è possibile configurare anche la visualizzazione delle proprietà del dispositivo da parte dell'operatore. Un operatore usa la pagina delle proprietà del dispositivo per gestirne i metadati. Ad esempio, può usare la pagina delle proprietà per visualizzare il numero di serie di un dispositivo o per aggiornare i dettagli di contatto del produttore.

1. Per modificare il layout delle proprietà per il condizionatore connesso, scegliere **Proprietà** e selezionare **Modifica modello**:

    ![Pagina Proprietà](media/tutorial-customize-operator/properties.png)

2. È possibile spostare e ridimensionare i campi delle proprietà:

    ![Modificare il layout delle proprietà](media/tutorial-customize-operator/propertieslayout.png)

3. Dopo aver apportato tutte le modifiche desiderate, fare clic su **Fine**.

> [!NOTE]
> In modalità **Modifica modello** non è possibile modificare i valori delle proprietà.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Visualizzare in anteprima il condizionatore connesso come operatore

In modalità **Modifica modello** è possibile personalizzare il dashboard e le pagine di impostazioni e proprietà per un operatore. Se non si lavora in modalità **Modifica modello**, è possibile visualizzare l'applicazione come operatore.

1. Per visualizzare il condizionatore connesso come operatore è necessario fare clic su **Fine** per interrompere la modifica del modello e tornare alla visualizzazione dell'operatore per il dispositivo.

2. Per aggiornare la posizione del dispositivo, modificare il valore nel riquadro Posizione e scegliere **Salva**:

    ![Modificare il valore di una proprietà](media/tutorial-customize-operator/editproperty.png)

3. Per inviare un'impostazione al condizionatore connesso, scegliere **Impostazioni**, modificare il valore di un'impostazione in un riquadro e scegliere **Aggiorna**:

    ![Inviare un'impostazione al dispositivo](media/tutorial-customize-operator/sendsetting.png)

    Quando il dispositivo invia un acknowledgement per il nuovo valore dell'impostazione, quest'ultima viene visualizzata come **sincronizzata** nel riquadro.

4. Gli operatori possono visualizzare il dashboard del dispositivo come configurato dal creatore:

    ![Visualizzazione dell'operatore del dashboard del dispositivo](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Configurare l'home page predefinita

Quando un creatore o un operatore accede a un'applicazione Azure IoT Central, vedrà l'home page. I creatori possono configurare i contenuti dell'home page in modo che includa quelli più utili per gli operatori.

1. Per personalizzare l'home page predefinita, passare alla pagina **Home** e selezionare **Modifica** in alto a destra nella pagina. Selezionando **Modifica** verrà visualizzato un pannello dal lato destro con un elenco di oggetti che è possibile aggiungere alla home page.

    ![Pagina Application Builder (Generatore applicazioni)](media/tutorial-customize-operator/builderhome.png)

2. Per personalizzare l'home page, aggiungere riquadri dalla **Raccolta**. Scegliere **Collegamento** e aggiungere i dettagli del sito Web dell'organizzazione. Quindi scegliere **Salva**:

    ![Aggiungere un collegamento all'home page](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > È anche possibile aggiungere collegamenti alle pagine all'interno dell'applicazione Azure IoT Central. Ad esempio, è possibile aggiungere un collegamento al dashboard o alla pagina di impostazioni del dispositivo.

3. Facoltativamente, scegliere **Immagine** e caricare un'immagine da visualizzare nell'home page. Un'immagine può avere un URL a cui essere reindirizzato facendo clic su di essa:

    ![Aggiungere un'immagine all'home page](media/tutorial-customize-operator/addimage.png)

    Per altre informazioni, vedere [Come preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Visualizzare in anteprima l'home page predefinita come operatore

Per visualizzare l'home page in anteprima come operatore e smettere di eseguire modifiche, selezionare **Fine** in alto a destra nella pagina

![Attivare/Disattivare la Modalità progettazione](media/tutorial-customize-operator/operatorviewhome.png)

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

* [Monitorare i dispositivi come operatore](tutorial-monitor-devices.md)
* [Aggiungere un nuovo dispositivo all'applicazione come operatore e sviluppatore del dispositivo](tutorial-add-device.md)
