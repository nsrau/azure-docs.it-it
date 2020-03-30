---
title: Gestire i dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come gestire i dispositivi nell'applicazione Azure IoT Central come operatore.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157943"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gestire i dispositivi nell'applicazione Azure IoT Central



L'articolo illustra come un operatore può gestire i dispositivi nell'applicazione Azure IoT Central. Un operatore può:

- Usare la pagina Dispositivi per visualizzare, aggiungere ed eliminare dispositivi connessi all'applicazione Azure IoT Central.Use the **Devices** page to view, add, and delete devices connected to your Azure IoT Central application.
- Mantenere aggiornato un inventario di dispositivi.
- Mantenere aggiornati i metadati del dispositivo modificando i valori archiviati nelle proprietà del dispositivo dalle visualizzazioni.
- Controlla il comportamento dei tuoi dispositivi aggiornando un'impostazione su un dispositivo specifico dalle tue visualizzazioni.

## <a name="view-your-devices"></a>Visualizzare i dispositivi

Per visualizzare un singolo dispositivo:

1. Scegliere **Dispositivi** nel riquadro sinistro. Qui viene visualizzato un elenco di tutti i dispositivi e dei modelli di dispositivo.

1. Scegliere un modello di dispositivo.

1. Nel riquadro destro della pagina **Dispositivi** viene visualizzato un elenco dei dispositivi creati da tale modello di dispositivo. Scegliere un singolo dispositivo per visualizzare la pagina dei dettagli dispositivo per questo dispositivo:

    ![Pagina dei dettagli dispositivo](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Aggiungere un dispositivo

Per aggiungere un dispositivo all'applicazione Azure IoT Central:

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo da cui si vuole creare un dispositivo.

1. Scegliere + **New** (Nuovo).

1. Attivare **o** **disattivare**l'interruttore **Simulato** . Un dispositivo reale indica un dispositivo fisico che si connette a un'applicazione Azure IoT Central. Un dispositivo simulato contiene dati di esempio generati automaticamente da Azure IoT Central.

1. Fare clic su **Crea**.

1. Questo dispositivo viene ora visualizzato nell'elenco dei dispositivi per questo modello. Selezionare il dispositivo per visualizzare la pagina dei dettagli del dispositivo che contiene tutte le visualizzazioni per il dispositivo.

## <a name="import-devices"></a>Importare dispositivi

Per connettere un numero elevato di dispositivi all'applicazione, è possibile importare in blocco i dispositivi da un file CSV. Il file CSV deve includere le colonne e le intestazioni seguenti:

* **IOTC_DeviceID**: l'ID dispositivo deve contenere solo lettere minuscole.
* **IOTC_DeviceName**: questa colonna è facoltativa.

Per registrare in blocco i dispositivi nell'applicazione:

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi.

    > [!NOTE]
    > Se non si dispone ancora di un modello di dispositivo, è possibile importare i dispositivi in **Tutti i dispositivi** e registrarli senza un modello. Dopo aver importato i dispositivi, è possibile eseguirne la migrazione a un modello.

1. Selezionare **Importa**.

    ![Azione di importazione](./media/howto-manage-devices/bulkimport1a.png)


1. Selezionare il file CSV contenente l'elenco di ID dispositivo da importare.

1. Al termine del caricamento del file viene avviata l'importazione dei dispositivi. Potete tenere traccia dello stato dell'importazione nel pannello Operazioni dispositivo. Questo pannello viene visualizzato automaticamente dopo l'avvio dell'importazione oppure è possibile accedervi tramite l'icona a campana nell'angolo in alto a destra.

1. Al termine dell'importazione, viene visualizzato un messaggio di operazione riuscita nel pannello Operazioni dispositivo.

    ![Importazione riuscita](./media/howto-manage-devices/bulkimport3a.png)


Se l'operazione di importazione del dispositivo non riesce, viene visualizzato un messaggio di errore nel pannello Operazioni dispositivo. Verrà generato un file di log scaricabile in cui vengono acquisiti tutti gli errori.

**Migrazione dei dispositivi a un modelloMigrating devices to a template**

Se si registrano i dispositivi avviando l'importazione in **Tutti i dispositivi**, i dispositivi vengono creati senza alcuna associazione di modello di dispositivo. I dispositivi devono essere associati a un modello per esplorare i dati e altri dettagli relativi al dispositivo. Per associare i dispositivi a un modello attenersi a questa procedura:

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Nel pannello a sinistra, scegliere **Tutti i dispositivi:**

    ![Dispositivi non associati](./media/howto-manage-devices/unassociateddevices1a.png)


1. Utilizzare il filtro sulla griglia per determinare se il valore nella colonna Modello di **dispositivo** è "Non associato" per uno qualsiasi dei dispositivi.

1. Selezionare i dispositivi da associare a un modello:

1. Selezionare **Migra**:

    ![Associare i dispositivi](./media/howto-manage-devices/unassociateddevices2a.png)


1. Scegliere il modello dall'elenco dei modelli disponibili e selezionare **Migra**.

1. I dispositivi selezionati verranno associati al modello di dispositivo scelto.


## <a name="export-devices"></a>Esportare dispositivi

Per connettere un dispositivo reale a IoT Central, è necessario disporre della stringa di connessione corrispondente. È possibile esportare i dettagli del dispositivo in blocco per ottenere le informazioni necessarie per creare stringhe di connessione del dispositivo. Il processo di esportazione crea un file CSV con l'identità del dispositivo, il nome del dispositivo e le chiavi per tutti i dispositivi selezionati.

Per esportare in blocco i dispositivi dall'applicazione:

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Nel riquadro sinistro scegliere il modello di dispositivo da cui si desidera esportare i dispositivi.

1. Selezionare i dispositivi che si desidera esportare, quindi selezionare l'azione **Esporta.**

    ![Esportazione](./media/howto-manage-devices/export1a.png)


1. Verrà avviato il processo di esportazione. È possibile tenere traccia dello stato utilizzando il pannello Operazioni dispositivo.

1. Al termine dell'esportazione verrà visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.

1. Selezionare il collegamento **Scarica file** per scaricare il file in una cartella locale sul disco.

    ![Esportazione riuscita](./media/howto-manage-devices/export2a.png)


1. Il file CSV esportato contiene le colonne relative all'ID di dispositivo, al nome di dispositivo, alle chiavi di dispositivo e alle identificazioni personali del certificato X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Per altre informazioni sulle stringhe di connessione e sulla connessione di dispositivi reali all'applicazione IoT Central, vedere [Connettività dei dispositivi in Azure IoT Central.](concepts-get-connected.md)

## <a name="delete-a-device"></a>Eliminare un dispositivo

Per eliminare un dispositivo simulato o reale dall'applicazione Azure IoT Central:

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo da eliminare.

1. Utilizzare gli strumenti di filtro per filtrare e cercare i dispositivi. Seleziona la casella accanto ai dispositivi da eliminare.

1. Scegliere **Elimina**. È possibile tenere traccia dello stato di questa eliminazione nel pannello Operazioni dispositivo.

## <a name="change-a-property"></a>Modificare una proprietà

Le proprietà cloud sono i metadati del dispositivo associati al dispositivo, ad esempio città e numero di serie. Le proprietà scrivibili controllano il comportamento di un dispositivo. In altre parole consentono di specificare gli input per il dispositivo.  Device properties are set by the device and are read-only within IoT Central. È possibile visualizzare e aggiornare le proprietà nelle visualizzazioni **Dettagli dispositivo** per il dispositivo.

1. Scegliere **Dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo di cui si desidera modificare le proprietà e selezionare il dispositivo di destinazione.

1. Scegliere la visualizzazione che contiene le proprietà per il dispositivo, questa visualizzazione consente di immettere valori e selezionare **Salva** nella parte superiore della pagina. Qui vengono visualizzate le proprietà del dispositivo e i relativi valori correnti. Le proprietà cloud e le proprietà scrivibili hanno campi modificabili, mentre le proprietà del dispositivo sono di sola lettura. Per le proprietà scrivibili, è possibile visualizzarne lo stato di sincronizzazione nella parte inferiore del campo. 

1. Modificare le proprietà in base ai valori necessari. È possibile modificare più proprietà contemporaneamente e aggiornarle tutte contemporaneamente.

1. Scegliere **Salva**. Se sono state salvate le proprietà scrivibili, i valori vengono inviati al dispositivo. Quando il dispositivo conferma la modifica per la proprietà scrivibile, lo stato torna a **synced**. Se è stata salvata una proprietà cloud, il valore viene aggiornato.


## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi nell'applicazione Azure IoT Central, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Come usare i gruppi di dispositiviHow to use device groups](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
