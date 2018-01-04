---
title: Caricare un file VHD in Azure DevTest Labs usando Esplora archivi di Microsoft Azure | Microsoft Docs
description: Caricare un file VHD nell'account di archiviazione del lab usando Esplora archivi di Microsoft Azure
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 25675aae77fbe2610fe416210de9a306c1c09f3d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Caricare un file VHD nell'account di archiviazione del lab usando Esplora archivi di Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs è possibile usare i file VHD per creare immagini personalizzate da usare per il provisioning di macchine virtuali. In questo articolo viene illustrato come usare [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) per caricare un file VHD nell'account di archiviazione di un lab. Dopo avere caricato il file VHD, vedere la [sezione Passaggi successivi](#next-steps) per un elenco di articoli che illustrano come creare un'immagine personalizzata dal file VHD caricato. Per altre informazioni sui dischi e sui dischi rigidi virtuali in Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come caricare un file VHD in DevTest Labs usando [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Scaricare e installare la versione più recente di Esplora archivi di Microsoft Azure](http://www.storageexplorer.com).

1. Ottenere il nome dell'account di archiviazione del lab usando il portale di Azure:

    1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
    
    1. Nell'elenco dei lab selezionare il lab desiderato.  
    
    1. Nel pannello del lab selezionare **Configurazione**. 
    
    1. Nel pannello **Configurazione** del lab selezionare **Immagini personalizzate (dischi rigidi virtuali)**.
    
    1. Nel pannello **Immagini personalizzate** selezionare **+Aggiungi**. 
    
    1. Nel pannello **Immagine personalizzata** selezionare **VHD**.
    
    1. Nel pannello **VHD** selezionare l'opzione **Carica un file VHD con PowerShell**.
    
        ![Carica un file VHD con PowerShell][0]
    
    1. Nel pannello **Upload an image using PowerShell** (Carica un'immagine usando PowerShell) è visualizzata una chiamata al cmdlet **Add-AzureVhd**. Il primo parametro (*Destination*) contiene il nome dell'account di archiviazione per il lab nel formato seguente:
    
        https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/... 

    1. Prendere nota del nome dell'account di archiviazione usato nei passaggi successivi.
    
1. Connettersi a un account di sottoscrizione di Azure usando Esplora archivi.

    > [!TIP] 
    > 
    > Esplora archivi supporta diverse opzioni di connessione. In questa sezione viene illustrato come connettersi a un account di archiviazione associato alla sottoscrizione di Azure. Per visualizzare altre opzioni di connessione supportate da Esplora archivi, vedere l'articolo [Introduzione a Esplora archivi](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Aprire Esplora archivi.
    
    1. In Esplora archivi, selezionare **Azure Account settings** (Impostazioni account Azure). 
    
        ![Azure Account settings][1]
    
    1. Nel riquadro sinistro vengono visualizzati gli account Microsoft a cui si è connessi. Per connettersi a un altro account, selezionare **Aggiungi un account**e seguire le istruzioni nelle finestre di dialogo per accedere con un account Microsoft associato ad almeno una sottoscrizione di Azure attiva.
    
        ![Aggiungi un account][2]
    
    1. Dopo avere effettuato l'acceso con un account Microsoft, il riquadro sinistro verrà popolato con le sottoscrizioni di Azure associate a quell'account. Selezionare le sottoscrizioni di Azure da utilizzare e quindi selezionare **Applica**. Selezionando **Tutte le sottoscrizioni** , viene alternata la selezione di tutte o di nessuna delle sottoscrizioni di Azure elencate.
    
        ![Selezionare le sottoscrizioni di Azure][3]
    
    1. Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.
    
        ![Sottoscrizioni di Azure selezionate][4]

1. Individuare l'account di archiviazione del lab:

    1. Nel riquadro sinistro di Esplora archivi, individuare ed espandere il nodo per la sottoscrizione di Azure proprietaria del lab.
    
    1. Nel nodo della sottoscrizione, espandere **Account di archiviazione**.

    1. Espandere il nodo dell'account di archiviazione del lab per mostrare i nodi per **Contenitori BLOB**, **Condivisioni file**, **Code** e **Tabelle**.
    
    1. Espandere il nodo **Contenitori BLOB**.
    
    1. Selezionare il contenitore BLOB dei caricamenti per visualizzarne il contenuto nel riquadro di destra.
        
        ![Caricare una directory][5]

1. Caricare il file VHD tramite Esplora archivi:

    1. Nel riquadro di destra di Esplora archivi dovrebbe essere visualizzato un elenco dei BLOB nel contenitore BLOB dei **caricamenti** dell'account di archiviazione del lab. Sulla barra degli strumenti dell'editor di blob, selezionare **Carica** 
        
        ![Pulsante Carica][6]
    
    1. Dal menu a discesa **Carica**, selezionare **Carica file...**.
    
    1. Nella finestra di dialogo **Carica file**, selezionare i puntini di sospensione.
        
        ![Selezionare il file][8]  

    1. Nella finestra di dialogo **Seleziona file da caricare** selezionare il file VHD desiderato, quindi selezionare **Apri**.
    
    1. Verrà nuovamente visualizzata la finestra di dialogo **Carica file**, modificare **Tipo BLOB** in **BLOB di pagine**.
    
    1. Selezionare **Carica**.

        ![Selezionare il file][9]  
    
    1. Il riquadro **Log attività** di Esplora archivi mostra lo stato di download e i collegamenti per annullare il caricamento. Il processo di caricamento di un file VHD può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione. 

        ![Stato upload-file][10]  

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando il portale di Azure](devtest-lab-create-template.md)
- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
