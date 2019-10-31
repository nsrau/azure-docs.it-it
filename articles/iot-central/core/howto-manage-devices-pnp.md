---
title: Gestire i dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come gestire i dispositivi nell'applicazione Azure IoT Central come operatore.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b5e4e27e059cdc84370bbf7dbf7c6bc651b1968e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177360"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>Gestire i dispositivi nell'applicazione IoT Central di Azure (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

L'articolo illustra come un operatore può gestire i dispositivi nell'applicazione Azure IoT Central. Un operatore può:

- Usare la pagina **dispositivi** per visualizzare, aggiungere ed eliminare i dispositivi connessi all'applicazione IoT Central di Azure.
- Mantenere aggiornato un inventario di dispositivi.
- Per aggiornare i metadati del dispositivo, modificare i valori archiviati nelle proprietà del dispositivo dalle viste.
- Controllare il comportamento dei dispositivi aggiornando un'impostazione in un dispositivo specifico dalle visualizzazioni.

## <a name="view-your-devices"></a>Visualizzare i dispositivi

Per visualizzare un singolo dispositivo:

1. Scegliere **dispositivi** nel riquadro sinistro. Qui viene visualizzato un elenco di tutti i dispositivi e dei modelli di dispositivo.

1. Scegliere un modello di dispositivo.

1. Nel riquadro di destra della pagina **dispositivi** viene visualizzato un elenco di dispositivi creati da tale modello di dispositivo. Scegliere un singolo dispositivo per visualizzare la pagina dei dettagli dispositivo per questo dispositivo:

    ![Pagina dei dettagli dispositivo](./media/howto-manage-devices-pnp/devicelist.png)


## <a name="add-a-device"></a>Aggiungere un dispositivo

Per aggiungere un dispositivo all'applicazione Azure IoT Central:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo da cui si vuole creare un dispositivo.

1. Scegliere + **New** (Nuovo).

1. Attivare **o** **disattivare**l'interruttore **simulato** . Un dispositivo reale indica un dispositivo fisico che si connette a un'applicazione Azure IoT Central. Un dispositivo simulato contiene dati di esempio generati automaticamente da Azure IoT Central.

1. Fare clic su **Create**(Crea).

1. Questo dispositivo viene ora visualizzato nell'elenco dei dispositivi per questo modello. Selezionare il dispositivo per visualizzare la pagina dei dettagli del dispositivo che contiene tutte le visualizzazioni del dispositivo.

## <a name="import-devices"></a>Importare dispositivi

Per connettere un numero elevato di dispositivi all'applicazione, è possibile importare in blocco i dispositivi da un file CSV. Il file CSV deve includere le colonne e le intestazioni seguenti:

* **IOTC_DeviceID**: l'ID dispositivo deve contenere solo lettere minuscole.
* **IOTC_DeviceName**: questa colonna è facoltativa.

Per registrare in blocco i dispositivi nell'applicazione:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi.

    > [!NOTE]
    > Se non si dispone ancora di un modello di dispositivo, è possibile importare **i dispositivi in tutti i dispositivi** e registrarli senza un modello. Dopo aver importato i dispositivi, è possibile eseguirne la migrazione a un modello.

1. Selezionare **Importa**.

    ![Azione di importazione](./media/howto-manage-devices-pnp/bulkimport1a.png)


1. Selezionare il file CSV contenente l'elenco di ID dispositivo da importare.

1. Al termine del caricamento del file viene avviata l'importazione dei dispositivi. È possibile tenere traccia dello stato dell'importazione nel pannello operazioni del dispositivo. Questo pannello viene visualizzato automaticamente dopo l'avvio dell'importazione oppure è possibile accedervi tramite l'icona a campana nell'angolo superiore destro.

1. Al termine dell'importazione, nel pannello operazioni dispositivo viene visualizzato un messaggio di operazione completata.

    ![Importazione riuscita](./media/howto-manage-devices-pnp/bulkimport3a.png)


Se l'operazione di importazione del dispositivo non riesce, viene visualizzato un messaggio di errore nel pannello operazioni del dispositivo. Verrà generato un file di log scaricabile in cui vengono acquisiti tutti gli errori.

**Migrazione di dispositivi a un modello**

Se si registrano i dispositivi avviando l'importazione in **tutti i dispositivi**, i dispositivi vengono creati senza alcuna associazione di modelli di dispositivo. I dispositivi devono essere associati a un modello per esplorare i dati e altri dettagli relativi al dispositivo. Per associare i dispositivi a un modello attenersi a questa procedura:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel pannello sinistro scegliere **tutti i dispositivi**:

    ![Dispositivi non associati](./media/howto-manage-devices-pnp/unassociateddevices1a.png)


1. Usare il filtro sulla griglia per determinare se il valore nella colonna **modello dispositivo** è "non associato" per i dispositivi.

1. Selezionare i dispositivi da associare a un modello:

1. Selezionare **Esegui migrazione**:

    ![Associare i dispositivi](./media/howto-manage-devices-pnp/unassociateddevices2a.png)


1. Scegliere il modello dall'elenco dei modelli disponibili e selezionare **migrazione**.

1. I dispositivi selezionati verranno associati al modello di dispositivo scelto.


## <a name="export-devices"></a>Esportare dispositivi

Per connettere un dispositivo reale a IoT Central, è necessario disporre della stringa di connessione corrispondente. È possibile esportare i dettagli del dispositivo in blocco per ottenere le informazioni necessarie per creare stringhe di connessione del dispositivo. Il processo di esportazione crea un file CSV con l'identità del dispositivo, il nome del dispositivo e le chiavi per tutti i dispositivi selezionati.

Per esportare in blocco i dispositivi dall'applicazione:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Nel riquadro sinistro scegliere il modello di dispositivo da cui si desidera esportare i dispositivi.

1. Selezionare i dispositivi che si desidera esportare, quindi selezionare l'azione **Esporta** .

    ![Esportazione](./media/howto-manage-devices-pnp/export1a.png)


1. Verrà avviato il processo di esportazione. È possibile tenere traccia dello stato usando il pannello operazioni del dispositivo.

1. Al termine dell'esportazione verrà visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.

1. Selezionare il collegamento **Scarica file** per scaricare il file in una cartella locale sul disco.

    ![Esportazione riuscita](./media/howto-manage-devices-pnp/export2a.png)


1. Il file CSV esportato contiene le colonne relative all'ID di dispositivo, al nome di dispositivo, alle chiavi di dispositivo e alle identificazioni personali del certificato X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Per altre informazioni sulle stringhe di connessione e sulla connessione di dispositivi reali all'applicazione IoT Central, vedere [connettività dei dispositivi in Azure IOT Central](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="delete-a-device"></a>Eliminare un dispositivo

Per eliminare un dispositivo simulato o reale dall'applicazione Azure IoT Central:

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo da eliminare.

1. Usare gli strumenti di filtro per filtrare e cercare i dispositivi. Selezionare la casella accanto ai dispositivi da eliminare.

1. Scegliere **Elimina**. È possibile tenere traccia dello stato di questa eliminazione nel pannello operazioni del dispositivo.

## <a name="change-a-property"></a>Modificare una proprietà

Le proprietà del cloud sono i metadati del dispositivo associati al dispositivo, ad esempio la città e il numero di serie. Le proprietà scrivibili controllano il comportamento di un dispositivo. In altre parole consentono di specificare gli input per il dispositivo.  Le proprietà del dispositivo sono impostate dal dispositivo e sono di sola lettura all'interno IoT Central. È possibile visualizzare e aggiornare le proprietà nelle visualizzazioni dei **Dettagli del dispositivo** .

1. Scegliere **dispositivi** nel riquadro sinistro.

1. Scegliere il modello di dispositivo del dispositivo di cui si desidera modificare le proprietà e selezionare il dispositivo di destinazione.

1. Scegliere la vista che contiene le proprietà per il dispositivo. questa visualizzazione consente di immettere valori e selezionare **Salva** nella parte superiore della pagina. Qui vengono visualizzate le proprietà del dispositivo e i relativi valori correnti. Le proprietà del cloud e le proprietà scrivibili hanno campi modificabili, mentre le proprietà del dispositivo sono di sola lettura. Per le proprietà scrivibili, è possibile visualizzare lo stato di sincronizzazione nella parte inferiore del campo. 

1. Modificare le proprietà con i valori necessari. È possibile modificare più proprietà alla volta e aggiornarle tutte contemporaneamente.

1. Scegliere **Salva**. Se sono state salvate proprietà scrivibili, i valori vengono inviati al dispositivo. Quando il dispositivo conferma la modifica della proprietà scrivibile, lo stato torna a **sincronizzato**. Se è stata salvata una proprietà cloud, il valore viene aggiornato.


## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi nell'applicazione Azure IoT Central, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Come usare i gruppi di dispositivi](tutorial-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
