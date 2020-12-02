---
title: Come distribuire le macchine virtuali in Azure Stack Edge Pro tramite il portale di Azure
description: Informazioni su come creare e gestire macchine virtuali in Azure Stack Edge Pro tramite il portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/02/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 89ef196cb5a124b8b1100871c408400f3fceef5c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467164"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite il portale di Azure

È possibile creare e gestire macchine virtuali (VM) in un dispositivo Azure Stack Edge usando portale di Azure, modelli, Azure PowerShell cmdlet e tramite gli script dell'interfaccia della riga di comando di Azure/Python. Questo articolo descrive come creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge usando il portale di Azure. 

Questo articolo si applica a Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e a dispositivi Mini R Azure Stack Edge. 

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione della VM

Il riepilogo di alto livello del flusso di lavoro di distribuzione è il seguente:

1. Abilitare un'interfaccia di rete per il calcolo nel dispositivo Azure Stack Edge. Viene creato un commutire virtuale nell'interfaccia di rete specificata.
1. Abilitare la gestione cloud delle macchine virtuali da portale di Azure.
1. Caricare un disco rigido virtuale in un account di archiviazione di Azure usando Storage Explorer. 
1. Usare il disco rigido virtuale caricato per scaricare il disco rigido virtuale nel dispositivo e creare un'immagine di macchina virtuale dal disco rigido virtuale. 
1. Usare le risorse create nei passaggi precedenti:
    1. Immagine di macchina virtuale creata.
    1. VSwitch associato all'interfaccia di rete in cui è stato abilitato il calcolo.
    1. Subnet associata a VSwitch.

    E creare o specificare le risorse seguenti inline:
    1. Nome macchina virtuale, scegliere le dimensioni della macchina virtuale supportate, le credenziali di accesso per la macchina virtuale. 
    1. Creare nuovi dischi dati o alleghi i dischi dati esistenti.
    1. Configurare l'indirizzo IP statico o dinamico per la macchina virtuale. Se si specifica un indirizzo IP statico, scegliere da un indirizzo IP libero nell'intervallo di subnet dell'interfaccia di rete abilitata per il calcolo.

    Usare le risorse indicate sopra per creare una macchina virtuale.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a creare e gestire le macchine virtuali nel dispositivo tramite il portale di Azure, assicurarsi che:

1. Sono state completate le impostazioni di rete nel dispositivo Azure Stack Edge Pro, come descritto in [passaggio 1: configurare Azure stack dispositivo Edge Pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

    1. È stata abilitata un'interfaccia di rete per il calcolo. L'indirizzo IP di questa interfaccia di rete viene usato per creare uno switch virtuale per la distribuzione della VM. Nell'interfaccia utente locale del dispositivo passare a **calcolo**. Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale.

        > [!IMPORTANT] 
        > È possibile configurare una sola porta per il calcolo.

    1. Abilitare il calcolo nell'interfaccia di rete. Azure Stack Edge Pro crea e gestisce una rete virtuale corrispondente a tale interfaccia di rete.

1. Si ha accesso a un disco rigido virtuale Windows o Linux che si userà per creare l'immagine di macchina virtuale per la macchina virtuale che si vuole creare.

## <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

Seguire questa procedura per creare una macchina virtuale nel dispositivo Azure Stack Edge.

### <a name="add-a-vm-image"></a>Aggiungere un'immagine di macchina virtuale

1. Caricare un disco rigido virtuale in un account di archiviazione di Azure. Attenersi alla procedura descritta in [caricare un disco rigido virtuale con Azure Storage Explorer](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).

1. Nella portale di Azure passare alla risorsa Azure Stack Edge per il dispositivo Azure Stack Edge. Passare alle **macchine virtuali > di calcolo Edge**.

    ![Aggiungi immagine VM 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Selezionare **macchine virtuali** per passare alla pagina **Panoramica** . **Abilitare** la gestione del cloud delle macchine virtuali.
    ![Aggiungi immagine VM 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Il primo passaggio consiste nell'aggiungere un'immagine di macchina virtuale. È già stato caricato un disco rigido virtuale nell'account di archiviazione nel passaggio precedente. Questo VHD verrà usato per creare un'immagine di macchina virtuale.

    Selezionare **Aggiungi immagine** per scaricare il disco rigido virtuale dall'account di archiviazione e aggiungere al dispositivo. Il processo di download richiede diversi minuti a seconda delle dimensioni del disco rigido virtuale e della larghezza di banda Internet disponibile per il download. 

    ![Aggiungi immagine macchina virtuale 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Nel pannello **Aggiungi immagine** immettere i parametri seguenti. Selezionare **Aggiungi**.


    |Parametro  |Descrizione  |
    |---------|---------|
    |Scarica da BLOB di archiviazione    |Passare al percorso del BLOB di archiviazione nell'account di archiviazione in cui è stato caricato il disco rigido virtuale.         |
    |Scarica in    | Impostato automaticamente sul dispositivo corrente in cui si distribuisce la macchina virtuale.        |
    |Salva immagine con nome      | Nome dell'immagine di macchina virtuale che si sta creando dal disco rigido virtuale caricato nell'account di archiviazione.        |
    |Tipo di sistema operativo     |Scegliere Windows o Linux come sistema operativo del disco rigido virtuale che si userà per creare l'immagine di macchina virtuale.         |
   

    ![Aggiungi immagine di macchina virtuale 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Il disco rigido virtuale viene scaricato e viene creata l'immagine di macchina virtuale. Per il completamento della creazione dell'immagine sono necessari alcuni minuti. Viene visualizzata una notifica per il corretto completamento dell'immagine di macchina virtuale.

    ![Aggiungi immagine VM 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Al termine della creazione dell'immagine di macchina virtuale, questa viene aggiunta all'elenco di immagini nel pannello **Immagini** .
    ![Aggiungere l'immagine di macchina virtuale 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Il pannello **distribuzioni** viene aggiornato per indicare lo stato della distribuzione.

    ![Aggiungi immagine VM 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    La nuova immagine aggiunta viene visualizzata anche nella pagina **Panoramica** .
    ![Aggiungi immagine VM 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Aggiungere una macchina virtuale

Seguire questa procedura per creare una macchina virtuale dopo aver creato un'immagine di macchina virtuale.

1. Nella pagina **Panoramica** selezionare **Aggiungi macchina virtuale**.

    ![Aggiungi macchina virtuale 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Nella scheda **nozioni di base** immettere i parametri seguenti.


    |Parametro |Descrizione  |
    |---------|---------|
    |Nome macchina virtuale     |         |
    |Immagine     | Consente di selezionare le immagini di macchina virtuale disponibili sul dispositivo.        |
    |Dimensione     | Scegliere tra le [dimensioni delle macchine virtuali supportate](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#supported-vm-sizes).        |
    |Nome utente     | Usare il nome utente predefinito *azureuser*.        |
    |Tipo di autenticazione    | Scegliere una chiave pubblica SSH o una password definita dall'utente.       |
    |Password     | Immettere una password per accedere alla macchina virtuale. La password deve avere una lunghezza di almeno 12 caratteri e soddisfare i [requisiti di complessità](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)definiti.        |
    |Conferma password    | Immettere di nuovo la password.        |


    ![Aggiungi macchina virtuale 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Selezionare **Avanti: Dischi**.

1. Nella scheda **dischi** si collegheranno i dischi alla macchina virtuale. 
    
    1. È possibile scegliere di **creare e alleghi un nuovo disco** o di **alleghi un disco esistente**.

        ![Aggiungi macchina virtuale 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Selezionare **Crea e Connetti un nuovo disco**. Nel pannello **Crea nuovo disco** specificare un nome per il disco e le dimensioni in Gib.

        ![Aggiungi macchina virtuale 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  Ripetere l'operazione precedente per aggiungere altri dischi. Una volta creati, i dischi vengono visualizzati nella scheda **dischi** .

        ![Aggiungi macchina virtuale 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Al termine, selezionare **Avanti: Rete**.

1. Nella scheda **rete** viene configurata la connettività di rete per la macchina virtuale.

    
    |Parametro  |Descrizione |
    |---------|---------|
    |Rete virtuale    | Nell'elenco a discesa selezionare il Commuter virtuale creato sul dispositivo Azure Stack Edge quando è stato abilitato il calcolo nell'interfaccia di rete.    |
    |Subnet     | Questo campo viene popolato automaticamente con la subnet associata all'interfaccia di rete in cui è stata abilitata la funzionalità di calcolo.         |
    |Indirizzo IP     | Fornire un indirizzo IP statico o dinamico per la macchina virtuale. L'indirizzo IP statico deve essere un indirizzo IP libero disponibile dall'intervallo di subnet specificato.        |

    ![Aggiungi macchina virtuale 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Selezionare **Avanti: Rivedi e crea**.

1. Nella scheda **revisione e creazione** esaminare le specifiche per la macchina virtuale e selezionare **Crea**.

    ![Aggiungi macchina virtuale 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. Viene avviata la creazione della macchina virtuale e possono essere necessari fino a 20 minuti. È possibile passare alle **distribuzioni** per monitorare la creazione della macchina virtuale.

    ![Aggiungi macchina virtuale 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. Dopo aver creato la macchina virtuale, la pagina **Panoramica** viene aggiornata per visualizzare la nuova macchina virtuale.

    ![Aggiungi macchina virtuale 9](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Selezionare la macchina virtuale appena creata per passare alle **macchine virtuali**.

    ![Aggiungi macchina virtuale 10](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Selezionare la macchina virtuale per visualizzare i dettagli. 

    ![Aggiungi macchina virtuale 11](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Connettersi a una macchina virtuale

A seconda del fatto che sia stata creata una macchina virtuale Windows o Linux, la procedura per la connessione può essere diversa. Non è possibile connettersi alle macchine virtuali distribuite nel dispositivo tramite il portale di Azure. Per connettersi alla VM Linux o Windows, è necessario seguire questa procedura.

### <a name="connect-to-linux-vm"></a>Connettersi a una VM Linux

Seguire questa procedura per connettersi a una VM Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Connettersi a una macchina virtuale Windows

Per connettersi a una macchina virtuale Windows, seguire questa procedura.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come amministrare il dispositivo Azure Stack Edge Pro, vedere[usare l'interfaccia utente Web locale per amministrare un Azure stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
