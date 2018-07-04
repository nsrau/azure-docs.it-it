---
title: Gestire i dispositivi nell'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come gestire i dispositivi nell'applicazione Azure IoT Central come operatore.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cf803c03d266f2a400e47fc551dea62936456177
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937619"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gestire i dispositivi nell'applicazione Azure IoT Central

L'articolo illustra come un operatore può gestire i dispositivi nell'applicazione Azure IoT Central. Un operatore può:

- Usare la pagina **Explorer** per visualizzare, aggiungere ed eliminare dispositivi connessi all'applicazione Azure IoT Central.
- Mantenere aggiornato un inventario di dispositivi.
- Mantenere aggiornati i metadati dei dispositivi modificando i valori archiviati nelle proprietà dei dispositivi.
- Controllare il comportamento dei dispositivi mediante l'aggiornamento di un'impostazione per un dispositivo specifico nella pagina **Settings** (Impostazioni).

## <a name="view-your-devices"></a>Visualizzare i dispositivi

Per visualizzare un singolo dispositivo:

1. Selezionare **Explorer** nel menu di spostamento a sinistra. Verrà visualizzato un elenco di [modelli di dispositivo](howto-set-up-template.md).

1. Scegliere un **modello di dispositivo** nel riquadro di sinistra.

1. Nel riquadro di destra verrà visualizzato un elenco di dispositivi creati usando questo modello di dispositivo. Scegliere un singolo dispositivo per visualizzare la pagina **Device Details** (Dettagli dispositivo) per questo dispositivo:

    [![Pagina Device Details (Dettagli dispositivo)](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Aggiungere un dispositivo

Per aggiungere un dispositivo all'applicazione Azure IoT Central:

1. Selezionare **Explorer** nel menu di spostamento a sinistra.

1. Scegliere il modello di dispositivo da cui si vuole creare un dispositivo.

1. Scegliere + **New** (Nuovo).

1. Scegliere **Real** (Reale) oppure **Simulated** (Simulato). Un dispositivo reale indica un dispositivo fisico che si connette a un'applicazione Azure IoT Central. Un dispositivo simulato contiene dati di esempio generati automaticamente da Azure IoT Central. In questo esempio si usa un dispositivo reale. Scegliere **Real** (Reale) per passare alla pagina **Device Details** (Dettagli dispositivo) per il nuovo dispositivo.


## <a name="import-devices"></a>Importare dispositivi

Per connettere un numero elevato di dispositivi all'applicazione, Azure IoT Central consente l'importazione in blocco dei dispositivi tramite un file CSV. 

Requisiti del file CSV:
1. Il file CSV deve contenere una sola colonna con gli ID dispositivo.

1. Il file non deve avere intestazioni.


Per registrare in blocco i dispositivi nell'applicazione:

1. Selezionare **Explorer** nel menu di spostamento a sinistra.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole creare in blocco i dispositivi.

 >   [!NOTE] 
    Se non si dispone ancora di un modello di dispositivo è possibile importare i dispositivi in **Dispositivi senza associazione** e registrarli senza modello. Dopo aver importato i dispositivi, è possibile associarli con un modello come passaggio successivo.

1. Fare clic su **Importa**.

    [![Azione di importazione](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Selezionare il file CSV contenente l'elenco di ID dispositivo da importare.

1. Al termine del caricamento del file viene avviata l'importazione dei dispositivi. È possibile tenere traccia dello stato di importazione nella parte superiore della griglia di dispositivi.

1. Al termine dell'importazione viene visualizzato un messaggio di operazione riuscita nella griglia di dispositivi.

    [![Importazione riuscita](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Se l'importazione di dispositivi ha esito negativo, verrà visualizzato un messaggio di errore nella griglia di dispositivi. Verrà generato un file di log con tutti gli errori, che può essere scaricato facendo clic sul messaggio di errore.


**Associazione di dispositivi con un modello**

Se si registrano i dispositivi avviando l'importazione sotto **Dispositivi Unassociated devices**, i dispositivi vengono creati senza l'associazione di modelli dispositivo. Il dispositivo deve essere associato a un modello per esplorare i dati e altri dettagli sul dispositivo. Per associare i dispositivi a un modello attenersi a questa procedura:
1. Selezionare **Explorer** nel menu di spostamento a sinistra.
1. Nel pannello sinistro scegliere **Dispositivi senza associazione**.
    [![Dispositivi senza associazione](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Selezionare i dispositivi da associare al modello.
1. Fare clic sull'opzione **Associa**.
    [![Associa dispositivi](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Scegliere il modello dall'elenco dei modelli disponibili e fare clic sul pulsante **Associa**.
1. I dispositivi selezionati verranno spostati nel rispettivo modello selezionato.

 >   [!NOTE] 
    Dopo aver associato un dispositivi a un modello, non è possibile rimuovere l'associazione o associarlo a un modello diverso.

## <a name="export-devices"></a>Esportare dispositivi

Per eseguire il provisioning di dispositivi per la connessione a IoT Central, sarà necessaria la stringa di connessione del dispositivo che viene generata da IoT Central. È possibile usare la funzionalità di esportazione per ottenere le stringhe di connessione e le altre proprietà dei dispositivi in blocco dall'applicazione. Esportazione crea un file CSV con l'identità del dispositivo, il nome del dispositivo e la stringa di connessione primaria per tutti i dispositivi selezionati.

Per esportare in blocco i dispositivi dall'applicazione:
1. Selezionare **Explorer** nel menu di spostamento a sinistra.

1. Nel riquadro di sinistra scegliere il modello di dispositivo per il quale si vuole esportare i dispositivi.

1. Selezionare i dispositivi che si desidera esportare, quindi scegliere sull'azione **Esportare**.

    [![Esportare](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Il processo di esportazione verrà avviato e sarà possibile tenere traccia dello stato nella parte superiore della griglia. 

1. Dopo aver completato l'esportazione, viene visualizzato un messaggio di operazione completata insieme a un collegamento per scaricare il file generato.

1. Fare clic sul **messaggio di operazione completata** per scaricare il file in una cartella sul disco.

    [![Esportazione riuscita](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Il file CSV esportato conterrà le seguenti informazioni:
    1. NOME
    1. ID dispositivo
    1. Stringa di connessione primaria


## <a name="delete-a-device"></a>Eliminare un dispositivo

Per eliminare un dispositivo simulato o reale dall'applicazione Azure IoT Central:

1. Scegliere **Explorer** nel menu di spostamento.

1. Scegliere il modello di dispositivo del dispositivo da eliminare.

1. Selezionare la casella accanto al dispositivo da eliminare.

1. Scegliere **Elimina**.

## <a name="change-a-device-setting"></a>Modificare un'impostazione di dispositivo

Le impostazioni controllano il comportamento di un dispositivo. In altre parole consentono di specificare gli input per il dispositivo. È possibile visualizzare e aggiornare le impostazioni di dispositivo nella pagina **Device Details** (Dettagli dispositivo).

1. Scegliere **Explorer** nel menu di spostamento.

1. Scegliere il modello di dispositivo del dispositivo di cui si vuole modificare le impostazioni.

1. Scegliere la scheda **Impostazioni** . Vengono visualizzate tutte le impostazioni del dispositivo e i relativi valori correnti. Per ogni impostazione è possibile verificare se il dispositivo è ancora in fase di sincronizzazione.

1. Modificare le impostazioni usando i valori desiderati. È possibile modificare più impostazioni allo stesso tempo e aggiornarle tutte contemporaneamente.

1. Scegliere **Update** (Aggiorna). I valori vengono inviati al dispositivo. Quando il dispositivo conferma la modifica dell'impostazione, l'impostazione torna allo stato **sincronizzato**.

## <a name="change-a-property"></a>Modificare una proprietà

Le proprietà sono i metadati associati al dispositivo, ad esempio città e numero di serie. È possibile visualizzare e aggiornare le proprietà nella pagina **Device Details** (Dettagli dispositivo).

1. Scegliere **Explorer** nel menu di navigazione.

1. Scegliere il modello di dispositivo del dispositivo di cui si vuole modificare le proprietà.

1. Scegliere la scheda **Properties** (Proprietà) in cui vengono visualizzate tutte le proprietà.

1. Modificare le proprietà usando i valori desiderati. È possibile modificare più proprietà immediatamente e aggiornarle tutte contemporaneamente.

1. Scegliere **Update** (Aggiorna).

> [!NOTE]
> Non è possibile modificare il valore delle _proprietà del dispositivo_. Le proprietà del dispositivo vengono impostate dal dispositivo e sono di sola lettura all'interno dell'applicazione Azure IoT Central.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire i dispositivi nell'applicazione Azure IoT Central, il passaggio successivo suggerito è:

> [!div class="nextstepaction"]
> [Come usare i set di dispositivi](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
