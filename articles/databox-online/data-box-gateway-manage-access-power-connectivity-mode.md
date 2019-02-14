---
title: Accesso, avvio/arresto e modalità di connessione per il dispositivo Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per il dispositivo Azure Data Box Gateway che permette di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: 0ad94799320e25d88f616117f1bfcf9f0513aadf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873020"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway-preview"></a>Gestire l'accesso, l'avvio/arresto e la modalità di connessione per Azure Data Box Gateway (anteprima)

Questo articolo descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per Azure Data Box Gateway. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto

> [!IMPORTANT]
> Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione.

## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso al dispositivo Data Box Gateway viene controllato tramite l'uso di una password amministratore del dispositivo. È possibile modificare la password amministratore tramite l'interfaccia utente Web locale. È anche possibile reimpostare la password amministratore del dispositivo nel portale di Azure.

### <a name="change-device-administrator-password"></a>Modificare la password amministratore del dispositivo

Per modificare la password amministratore del dispositivo, completare questi passaggi nell'interfaccia utente locale.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Fare clic su **Cambia password**.
 
### <a name="reset-device-administrator-password"></a>Reimpostare la password amministratore del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Fare clic su **Reimposta**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre alla modalità normale predefinita, il dispositivo può anche essere eseguito in modalità parzialmente disconnessa o disconnessa. Ognuna di queste modalità viene descritta di seguito:

- **Parzialmente disconnesso**: in questa modalità il dispositivo non può caricare dati nelle condivisioni, ma può essere gestito tramite il portale di Azure.

    Questa modalità viene in genere usata in caso di rete satellitare a consumo e l'obiettivo consiste nel ridurre al minimo il consumo di larghezza di banda di rete. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso**: in questa modalità il dispositivo è completamente disconnesso dal cloud e le operazioni di caricamento e download nel cloud sono entrambe disabilitate. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, completare questi passaggi:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Disabilitare **Cloud upload and download** (Caricamento e download nel cloud).
3. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Per eseguire il dispositivo in modalità parzialmente disconnessa, disabilitare **Azure portal management** (Gestione portale di Azure). A questo punto, il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo virtuale e fisico tramite l'interfaccia utente Web locale. Prima di riavviare, si consiglia di portare offline le condivisioni sull'host e quindi il dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Fare clic su **Arresta** o **Riavvia** in base all'operazione desiderata.

    ![Impostazioni di alimentazione](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta una conferma, fare clic su **Sì** per continuare.

> [!NOTE]
> Se si arresta il dispositivo virtuale, è necessario avviare il dispositivo tramite la gestione dell'hypervisor.
