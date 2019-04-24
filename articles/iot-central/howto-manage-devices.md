---
title: Gestire i dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come gestire i dispositivi nell'applicazione Azure IoT Central come operatore.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a723ca6d69d45613636c7659e6566cec19b31d65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364176"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gestire i dispositivi nell'applicazione Azure IoT Central

L'articolo illustra come un operatore può gestire i dispositivi nell'applicazione Azure IoT Central. Un operatore può:

- Usare la pagina **Device Explorer** per visualizzare, aggiungere ed eliminare dispositivi connessi all'applicazione Azure IoT Central.
- Mantenere aggiornato un inventario di dispositivi.
- Mantenere aggiornati i metadati dei dispositivi modificando i valori archiviati nelle proprietà dei dispositivi.
- Controllare il comportamento dei dispositivi mediante l'aggiornamento di un'impostazione per un dispositivo specifico nella pagina **Settings** (Impostazioni).

## <a name="view-your-devices"></a>Visualizzare i dispositivi

Per visualizzare un singolo dispositivo:

1. Selezionare **Device Explorer** nel menu di spostamento a sinistra. Verrà visualizzato un elenco di [modelli di dispositivo](howto-set-up-template.md).

1. Scegliere un modello di dispositivo dall'elenco **Modelli**.

1. Nel riquadro di destra della pagina **Device Explorer** verrà visualizzato un elenco di dispositivi creati a partire da tale modello di dispositivo. Scegliere un singolo dispositivo per visualizzare la pagina dei dettagli dispositivo per questo dispositivo:

    ![Pagina dei dettagli dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Aggiungere un dispositivo

Per aggiungere un dispositivo all'applicazione Azure IoT Central:

1. Selezionare **Device Explorer** nel menu di spostamento a sinistra.

1. Scegliere il modello di dispositivo da cui si vuole creare un dispositivo.

1. Scegliere + **New** (Nuovo).

1. Scegliere **Real** (Reale) oppure **Simulated** (Simulato). Un dispositivo reale indica un dispositivo fisico che si connette a un'applicazione Azure IoT Central. Un dispositivo simulato contiene dati di esempio generati automaticamente da Azure IoT Central.

## <a name="import-devices"></a>Importare dispositivi

Per connettere un numero elevato di dispositivi all'applicazione, è possibile importare in blocco i dispositivi da un file CSV. Il file CSV deve includere le colonne e le intestazioni seguenti:

* **IOTC_DeviceID**: l'ID dispositivo deve contenere solo lettere minuscole.
* **IOTC_DeviceName**: questa colonna è facoltativa.

Per registrare in blocco i dispositivi nell'applicazione:

1. Selezionare **Device Explorer** nel menu di spostamento a sinistra.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi.

    > [!NOTE]
    > Se non si dispone ancora di un modello di dispositivo, è possibile importare i dispositivi elencati in **Dispositivi non associati** e registrarli senza modello. Dopo aver importato i dispositivi, è possibile associarli a un modello.

1. Selezionare **Importa**.

    ![Azione di importazione](./media/howto-manage-devices/BulkImport1.png)

1. Selezionare il file CSV contenente l'elenco di ID dispositivo da importare.

1. Al termine del caricamento del file viene avviata l'importazione dei dispositivi. È possibile tenere traccia dello stato di importazione nella parte superiore della griglia di dispositivi.

1. Al termine dell'importazione viene visualizzato un messaggio di operazione riuscita nella griglia di dispositivi.

    ![Importazione riuscita](./media/howto-manage-devices/BulkImport3.png)

Se l'importazione di dispositivi ha esito negativo, verrà visualizzato un messaggio di errore nella griglia di dispositivi. Verrà generato un file di log scaricabile in cui vengono acquisiti tutti gli errori.

**Associazione di dispositivi con un modello**

Se si registrano i dispositivi avviando l'importazione sotto **Dispositivi non associati**, i dispositivi vengono creati senza l'associazione di modelli dispositivo. I dispositivi devono essere associati a un modello per esplorare i dati e altri dettagli relativi al dispositivo. Per associare i dispositivi a un modello attenersi a questa procedura:

1. Selezionare **Device Explorer** nel menu di spostamento a sinistra.

1. Nel pannello di sinistra scegliere **Dispositivi non associati**:

    ![Dispositivi non associati](./media/howto-manage-devices/UnassociatedDevices1.png)

1. Selezionare i dispositivi da associare a un modello:

1. Selezionare **associare**:

    ![Associare i dispositivi](./media/howto-manage-devices/UnassociatedDevices2.png)

1. Scegliere il modello dall'elenco dei modelli disponibili e selezionare **associare**.

1. I dispositivi selezionati verranno associati al modello di dispositivo scelto.

> [!NOTE]
> Dopo che un dispositivo è stato associato a un modello, non è possibile rimuovere l'associazione o associarlo a un modello diverso.

## <a name="export-devices"></a>Esportare dispositivi

Per connettere un dispositivo reale a IoT Central, è necessario disporre della stringa di connessione corrispondente. È possibile esportare i dettagli di dispositivi in blocco per ottenere le informazioni necessarie per creare stringhe di connessione di dispositivi. Il processo di esportazione crea un file CSV con l'identità del dispositivo, nome del dispositivo e le chiavi per tutti i dispositivi selezionati.

Per esportare in blocco i dispositivi dall'applicazione:

1. Selezionare **Device Explorer** nel menu di spostamento a sinistra.

1. Nel pannello di sinistra scegliere il modello di dispositivo a partire dal quale si vuole esportare i dispositivi.

1. Selezionare i dispositivi che si desidera esportare e quindi selezionare il **esportare** azione.

    ![Esportazione](./media/howto-manage-devices/Export1.png)

1. Verrà avviato il processo di esportazione. È possibile tenere traccia dello stato nella parte superiore della griglia.

1. Al termine dell'esportazione verrà visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.

1. Selezionare il **messaggio di operazione riuscita** per scaricare il file in una cartella sul disco locale.

    ![Esportazione riuscita](./media/howto-manage-devices/Export2.png)

1. Il file CSV esportato contiene le colonne relative all'ID di dispositivo, al nome di dispositivo, alle chiavi di dispositivo e alle identificazioni personali del certificato X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Visualizzare [connettività dei dispositivi in Azure IoT Central](concepts-connectivity.md), per altre informazioni sulle stringhe di connessione e di dispositivi reali che si connette all'applicazione IoT Central.

## <a name="delete-a-device"></a>Eliminare un dispositivo

Per eliminare un dispositivo simulato o reale dall'applicazione Azure IoT Central:

1. Selezionare **Device Explorer** nel menu di spostamento.

1. Scegliere il modello di dispositivo del dispositivo da eliminare.

1. Selezionare la casella accanto al dispositivo da eliminare.

1. Scegliere **Elimina**.

## <a name="change-a-device-setting"></a>Modificare un'impostazione di dispositivo

Le impostazioni controllano il comportamento di un dispositivo. In altre parole consentono di specificare gli input per il dispositivo. È possibile visualizzare e aggiornare le impostazioni di dispositivo nella pagina **Device Details** (Dettagli dispositivo).

1. Selezionare **Device Explorer** nel menu di spostamento.

1. Scegliere il modello di dispositivo del dispositivo di cui si vuole modificare le impostazioni.

1. Scegliere la scheda **Impostazioni** . Vengono visualizzate tutte le impostazioni del dispositivo e i relativi valori correnti. Per ogni impostazione è possibile verificare se il dispositivo è ancora in fase di sincronizzazione.

1. Modificare le impostazioni usando i valori desiderati. È possibile modificare più impostazioni allo stesso tempo e aggiornarle tutte contemporaneamente.

1. Scegliere **Update** (Aggiorna). I valori vengono inviati al dispositivo. Quando il dispositivo conferma la modifica dell'impostazione, viene ripristinato lo stato **sincronizzato**.

## <a name="change-a-property"></a>Modificare una proprietà

Le proprietà sono i metadati associati al dispositivo, ad esempio città e numero di serie. È possibile visualizzare e aggiornare le proprietà nella pagina **Device Details** (Dettagli dispositivo).

1. Selezionare **Device Explorer** nel menu di spostamento.

1. Scegliere il modello di dispositivo del dispositivo di cui si vuole modificare le proprietà.

1. Scegliere la scheda **Properties** (Proprietà) in cui vengono visualizzate tutte le proprietà.

1. Modificare le proprietà dell'applicazione usando i valori desiderati. È possibile modificare più proprietà immediatamente e aggiornarle tutte contemporaneamente. Scegliere **Update** (Aggiorna).

> [!NOTE]
> Non è possibile modificare il valore delle _proprietà del dispositivo_. Le proprietà del dispositivo vengono impostate dal dispositivo e sono di sola lettura all'interno dell'applicazione Azure IoT Central.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come gestire i dispositivi nell'applicazione Azure IoT Central, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Come usare i set di dispositivi](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
