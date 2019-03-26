---
title: Accesso al dispositivo perimetrale casella dei dati di Microsoft Azure, potenza e la modalità di connettività | Microsoft Docs
description: Viene descritto come gestire l'accesso, potenza e la modalità di connettività per il dispositivo perimetrale finestra dati di Azure che consente di trasferire i dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 813563b500b9365289285a89536f2724fb87acad
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417803"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Gestire l'accesso, potenza e la modalità di connettività per il bordo casella dati di Azure

Questo articolo descrive come gestire la modalità di accesso, potenza e la connettività per il bordo casella dati di Azure. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto


## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso per dispositivo Edge casella dei dati viene controllato mediante l'utilizzo di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Seguire questi passaggi nell'interfaccia utente locale per modificare la password del dispositivo.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Selezionare **Modifica password**.
 
### <a name="reset-device-password"></a>Reimposta password dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Selezionare **reimpostare**.

    ![Reimposta password](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre la modalità sempre connessi predefinita, il dispositivo può anche eseguire in modalità connessa parzialmente o completamente disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Completamente connesso** -si tratta della modalità predefinita normale in cui opera il dispositivo. In questa modalità è abilitato il caricamento di cloud e il download dei dati. È possibile usare il portale di Azure o l'interfaccia utente web locale per gestire il dispositivo.

- **Parzialmente disconnesso** : In questa modalità, il dispositivo non è possibile caricare o scaricare qualsiasi condivisione di dati possono tuttavia essere gestiti tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Nell'elenco a discesa, selezionare la modalità che si desidera far funzionare il dispositivo in. È possibile selezionare **completamente connesso**, **parzialmente connessi**, e **completamente disconnessa**. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo fisico usando l'interfaccia utente web locale. Si consiglia di prima del riavvio, portare offline le condivisioni nel server di dati e quindi nel dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Selezionare **arresto** oppure **riavviare** a seconda che si desidera eseguire.

    ![Impostazioni di alimentazione](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta la conferma, selezionare **Sì** per procedere.

> [!NOTE]
> Se si arresta il dispositivo fisico, è necessario premere il pulsante di alimentazione sul dispositivo per attivarlo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire le condivisioni](data-box-edge-manage-shares.md).