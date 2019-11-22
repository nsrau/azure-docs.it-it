---
title: "Esercitazione: Personalizzare il dashboard dell'operatore in Azure IoT Central"
description: Questa esercitazione illustra come personalizzare il dashboard dell'operatore in un'applicazione di IoT Central e gestire i dispositivi.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 0dcabada522bf8189d163936551ea94cb4dd2657
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112909"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Esercitazione:  Personalizzare il dashboard dell'operatore e gestire i dispositivi in Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione illustra agli sviluppatori come personalizzare il dashboard dell'operatore in un'applicazione di analisi dei punti vendita di Azure IoT Central. Gli operatori potranno usare il dashboard personalizzato per eseguire l'applicazione e gestire i dispositivi collegati.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Modificare il nome del dashboard
> * Personalizzare i riquadri di immagine nel dashboard
> * Disporre i riquadri per modificare il layout
> * Aggiungere riquadri di telemetria per visualizzare le condizioni
> * Aggiungere riquadri di proprietà per visualizzare i dettagli dei dispositivi
> * Aggiungere riquadri di comando per eseguire comandi

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, lo sviluppatore deve completare la prima esercitazione che illustra come creare l'applicazione di analisi dei punti vendita in Azure IoT Central:

* [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md) (esercitazione obbligatoria)

## <a name="change-the-dashboard-name"></a>Modificare il nome del dashboard
Per personalizzare il dashboard dell'operatore, modificare il dashboard predefinito nell'applicazione. Facoltativamente, è possibile creare nuovi dashboard aggiuntivi. Il primo passaggio per personalizzare il dashboard nell'applicazione consiste nel modificarne il nome.

1. Passare al sito Web di [gestione applicazioni di Azure IoT Central](https://aka.ms/iotcentral).

1. Aprire l'applicazione di monitoraggio delle condizioni creata nell'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md).

1. Selezionare **Edit** (Modifica) sulla barra degli strumenti del dashboard. In modalità di modifica è possibile personalizzare l'aspetto, il layout e il contenuto del dashboard.

    ![Azure IoT Central - Modificare il dashboard](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. Facoltativamente, nascondere il riquadro sinistro in modo da ottenere un'area di lavoro più ampia per modificare il dashboard.

1. Immettere un nome descrittivo per il dashboard in **Dashboard name** (Nome dashboard). In questa esercitazione viene usata una società fittizia denominata Contoso e il nome del dashboard di esempio è *Contoso dashboard*. 

1. Selezionare **Salva**. Le modifiche apportate al dashboard verranno salvate e la modalità di modifica verrà disabilitata.

    ![Azure IoT Central - Modificare il nome del dashboard](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Personalizzare i riquadri di immagine nel dashboard
Il dashboard di un'applicazione di Azure IoT Central è costituito da uno o più riquadri. Un riquadro è un'area rettangolare per la visualizzazione del contenuto di un dashboard. È possibile associare ai riquadri vari tipi di contenuto e anche trascinare, rilasciare e ridimensionare i riquadri per personalizzare il layout. Sono disponibili diversi tipi di riquadri per la visualizzazione del contenuto. I riquadri di immagine contengono immagini a cui è possibile aggiungere un URL per consentire agli utenti di fare clic su di esse. I riquadri di etichetta visualizzano testo normale. I riquadri di markdown contengono contenuto formattato e consentono di impostare un'immagine, un URL, un titolo e codice markdown di cui viene eseguito il rendering come HTML. I riquadri di telemetria, di proprietà o di comando visualizzano dati specifici dei dispositivi. 

In questa sezione si apprenderà come personalizzare i riquadri di immagine nel dashboard.

Per personalizzare il riquadro che visualizza un'immagine di marchio nel dashboard:

1. Selezionare **Edit** (Modifica) sulla barra degli strumenti del dashboard. 

1. Selezionare **Configure** (Configura) nel riquadro che visualizza l'immagine del marchio Northwind. 

    ![Azure IoT Central - Modificare l'immagine di un marchio](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. Modificare il valore di **Title** (Titolo). Il titolo viene visualizzato quando un utente fa passare il puntatore del mouse sull'immagine.

1. Selezionare **Image** (Immagine). Verrà visualizzata una finestra di dialogo che consente di caricare un'immagine personalizzata. 

1. Facoltativamente, specificare un URL per l'immagine.

1. Selezionare **Update configuration** (Aggiorna configurazione). Il pulsante **Update configuration** (Aggiorna configurazione) salva le modifiche apportate al dashboard e mantiene abilitata la modalità di modifica.

    ![Azure IoT Central - Salvare l'immagine di un marchio](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. Facoltativamente, selezionare **Configure** (Configura) nel riquadro con il titolo **Documentation** e specificare un URL per accedere a contenuto di supporto. 

Per personalizzare il riquadro di immagine che visualizza una mappa delle zone dei sensori nel punto vendita:

1. Selezionare **Configure** (Configura) nel riquadro che visualizza la mappa predefinita delle zone dei sensori. 

1. Selezionare **Image** (Immagine) e usare la finestra di dialogo per caricare un'immagine personalizzata di una mappa delle zone del punto vendita. 

1. Selezionare **Update configuration** (Aggiorna configurazione).

    ![Azure IoT Central - Salvare la mappa del punto vendita](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    La mappa di esempio del punto vendita Contoso mostra quattro zone: due zone delle casse, una zona per l'abbigliamento e i prodotti per la cura personale e una zona per i generi alimentari e la gastronomia. In questa esercitazione verranno associati sensori a queste zone per fornire dati di telemetria.

    ![Azure IoT Central - Zone del punto vendita](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. Selezionare **Salva**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Disporre i riquadri per modificare il layout
Un passaggio importante nella personalizzazione di un dashboard consiste nel ridisporre i riquadri in modo da configurare una visualizzazione utile. Gli operatori dell'applicazione usano il dashboard per visualizzare i dati di telemetria dei dispositivi, gestire i dispositivi e monitorare le condizioni ambientali in un punto vendita. Azure IoT Central facilita il compito dello sviluppatore semplificando le attività da eseguire per creare un dashboard. In modalità di modifica è possibile aggiungere, spostare, ridimensionare ed eliminare rapidamente i riquadri del dashboard. La creazione del dashboard è ulteriormente facilitata dal modello di applicazione **In-store analytics - checkout** per il monitoraggio delle casse nel punto vendita. In questo modello viene presentato il layout di un dashboard funzionante, con i sensori connessi e riquadri che visualizzano i dati delle code alle casse e le condizioni ambientali.

In questa sezione si riorganizzerà il contenuto del dashboard nel modello di applicazione **In-store analytics - checkout** per realizzare un layout personalizzato.

Per rimuovere i riquadri che non si prevede di usare nell'applicazione:

1. Selezionare **Edit** (Modifica) sulla barra degli strumenti del dashboard. 

1. Selezionare **X Delete** (X Elimina) per rimuovere i riquadri seguenti: **Back to all zones**, **Visit store dashboard**, **Wait time** e tutti e tre i riquadri associati a **Checkout 3**. Nel dashboard del punto vendita di Contoso non vengono usati questi riquadri. 

    ![Azure IoT Central - Eliminare i riquadri](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. Scorrere per visualizzare i riquadri rimanenti del dashboard.

1. Selezionare **X Delete** (X Elimina) per rimuovere i riquadri seguenti: **Warm-up checkout zone**, **Cool-down checkout zone**, **Occupancy sensor settings**, **Thermostat sensor settings** ed **Environment conditions**. 

   ![Azure IoT Central - Eliminare i riquadri rimanenti](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. Selezionare **Salva**. La rimozione dei riquadri inutilizzati consente di liberare spazio nella pagina di modifica e semplifica la visualizzazione del dashboard per gli operatori.

1. Visualizzare le modifiche apportate al dashboard.

   ![Azure IoT Central - Dashboard dopo l'eliminazione dei riquadri](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

Dopo aver rimosso i riquadri inutilizzati, ridisporre i riquadri rimanenti per creare un layout organizzato. Il nuovo layout include spazio per i riquadri che verranno aggiunti in un passaggio successivo.

Per ridisporre i riquadri rimanenti:

1. Selezionare **Modifica**.

1. Selezionare il riquadro **Occupancy firmware** e trascinarlo a destra del riquadro della batteria di **Occupancy**.

1. Selezionare il riquadro **Thermostat firmware** e trascinarlo a destra del riquadro della batteria di **Thermostat**.

1. Selezionare **Salva**.

1. Visualizzare le modifiche apportate al layout. 

    ![Azure IoT Central - Riquadri delle batterie del firmware](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Aggiungere riquadri di telemetria per visualizzare le condizioni
Dopo aver personalizzato il layout del dashboard, è possibile aggiungere riquadri per visualizzare i dati di telemetria. Per creare un riquadro di telemetria, selezionare un modello di dispositivo e un'istanza del dispositivo, quindi selezionare i dati di telemetria specifici del dispositivo da visualizzare nel riquadro. Il modello di applicazione **In-store analytics - checkout** include diversi riquadri di telemetria nel dashboard. I quattro riquadri nelle due zone delle casse visualizzano i dati di telemetria restituiti dal sensore di presenza simulato. Il riquadro **People traffic** mostra i valori relativi alle due zone delle casse. 

In questa sezione si aggiungeranno altri due riquadri di telemetria per mostrare i dati relativi alle condizioni ambientali che vengono restituiti dai sensori RuuviTag aggiunti nell'esercitazione [Creare un'applicazione di analisi dei punti vendita in Azure IoT Central](./tutorial-in-store-analytics-create-app-pnp.md). 

Per aggiungere riquadri per visualizzare i dati delle condizioni ambientali restituiti dai sensori RuuviTag:

1. Selezionare **Modifica**.

1. Selezionare `RuuviTag` nell'elenco **Device template** (Modello di dispositivo). 

1. Selezionare uno dei due sensori RuuviTag in **Device instance** (Istanza del dispositivo). Nel punto vendita Contoso di esempio selezionare `Zone 1 Ruuvi` per creare un riquadro di telemetria per la Zona 1. 

1. Selezionare `Relative humidity` e `temperature` nell'elenco **Telemetry** (Telemetria). Questi sono gli elementi di telemetria visualizzati per ogni zona nel riquadro.

1. Selezionare **Combine** (Combina). 

    ![Azure IoT Central - Aggiungere il riquadro RuuviTag 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    Verrà visualizzato un nuovo riquadro per mostrare i dati di telemetria combinati di umidità e temperatura per il sensore selezionato. 

1. Selezionare **Configure** (Configura) nel nuovo riquadro per il sensore RuuviTag. 

1. Modificare il valore di **Title** (Titolo) in *Zone 1 environment*. 

1. Selezionare **Update configuration** (Aggiorna configurazione).

1. Ripetere i passaggi precedenti per creare un riquadro per la seconda istanza del sensore. Impostare **Title** (Titolo) su *Zone 2 environment* e quindi selezionare **Update configuration** (Aggiorna configurazione).

1. Trascinare il riquadro con il titolo **Zone 2 environment** al di sotto del riquadro **Thermostat firmware**. 

1. Trascinare il riquadro con il titolo **Zone 1 environment** al di sotto del riquadro **People traffic**. 

1. Selezionare **Salva**. Il dashboard visualizzerà i dati di telemetria delle zone nei due nuovi riquadri.

    ![Azure IoT Central - Tutti i riquadri RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

Per modificare il riquadro **People traffic** in modo da mostrare i dati di telemetria solo per le due zone delle casse:

1. Selezionare **Modifica**. 

1. Selezionare **Configure** (Configura) nel riquadro **People traffic**.

1. In **Telemetry** (Telemetria) selezionare **count 1**, **count 2** e **count 3**. 

1. Selezionare **Update configuration** (Aggiorna configurazione). La configurazione esistente nel riquadro verrà cancellata. 

1. Selezionare di nuovo **Configure** (Configura) nel riquadro **People traffic**.

1. In **Telemetry** (Telemetria) selezionare **count 1** e **count 2**. 

1. Selezionare **Update configuration** (Aggiorna configurazione). 

1. Selezionare **Salva**.  Il dashboard aggiornato visualizzerà i conteggi solo per le due zone delle casse specificate, in base ai dati restituiti dal sensore di presenza simulato.

    ![Azure IoT Central - Afflusso dei clienti su due corsie](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Aggiungere riquadri di proprietà per visualizzare i dettagli dei dispositivi
Gli operatori dell'applicazione usano il dashboard per gestire i dispositivi e monitorare lo stato. Aggiungere un riquadro per ogni sensore RuuviTag per consentire agli operatori di visualizzare la versione del software. 

Per aggiungere un riquadro di proprietà per ogni RuuviTag:

1. Selezionare **Modifica**.

1. Selezionare `RuuviTag` nell'elenco **Device template** (Modello di dispositivo). 

1. Selezionare uno dei due sensori RuuviTag in **Device instance** (Istanza del dispositivo). Nel punto vendita Contoso di esempio selezionare `Zone 1 Ruuvi` per creare un riquadro di telemetria per la Zona 1. 

1. Selezionare **Properties > Software version** (Proprietà > Versione software).

1. Selezionare **Combine** (Combina). 

1. Selezionare **Configure** (Configura) nel riquadro appena creato con il titolo **Software version**. 

1. Modificare il valore di **Title** (Titolo) in *Ruuvi 1 software version*.

1. Selezionare **Update configuration** (Aggiorna configurazione). 

1. Trascinare il riquadro con il titolo **Ruuv 1 software version** al di sotto del riquadro **Zone 1 environment**.

1. Ripetere i passaggi precedenti per creare un riquadro di proprietà della versione del software per il secondo sensore RuuviTag. 

1. Selezionare **Salva**.  

    ![Azure IoT Central - Riquadri di proprietà per i sensori RuuviTag](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Aggiungere riquadri di comando per eseguire comandi
Gli operatori dell'applicazione usano il dashboard anche per gestire i dispositivi eseguendo comandi. È possibile aggiungere riquadri nel dashboard in modo da eseguire comandi predefiniti in un dispositivo. In questa sezione si aggiungerà un riquadro di comando per consentire agli operatori di riavviare il gateway Rigado. 

Per aggiungere un riquadro di comando per riavviare il gateway:

1. Selezionare **Modifica**. 

1. Selezionare `C500` nell'elenco **Device template** (Modello di dispositivo). Questo è il modello per il gateway Rigado C500. 

1. Selezionare l'istanza del gateway in **Device instance** (Istanza del dispositivo).

1. Selezionare **Command > Reboot** (Comando > Riavvia) e trascinarlo nel dashboard accanto alla mappa del punto vendita. 

1. Selezionare **Salva**. 

1. Visualizzare il dashboard di Contoso completato. 

    ![Azure IoT Central - Personalizzazione del dashboard completata](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. Facoltativamente, selezionare il riquadro **Reboot** per eseguire il comando di riavvio sul gateway.

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

* Modificare il nome del dashboard
* Personalizzare i riquadri di immagine nel dashboard
* Disporre i riquadri per modificare il layout
* Aggiungere riquadri di telemetria per visualizzare le condizioni
* Aggiungere riquadri di proprietà per visualizzare i dettagli dei dispositivi
* Aggiungere riquadri di comando per eseguire comandi

Ora che è stato personalizzato il dashboard nell'applicazione di analisi dei punti vendita di Azure IoT Central, il passaggio successivo consigliato è il seguente:

> [!div class="nextstepaction"]
> [Esportare i dati e visualizzare informazioni dettagliate](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
