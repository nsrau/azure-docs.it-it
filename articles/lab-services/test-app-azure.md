---
title: Come testare l'app in Azure | Microsoft Docs
description: Informazioni su come creare una condivisione file in un lab e montarla nel computer locale; su come creare una macchina virtuale nel lab, quindi distribuire le applicazioni desktop o Web alla condivisione file e testarli.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: 9ca1c4a1ed1841f82bc386cff48315dd15ef91bb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730410"
---
# <a name="test-your-app-in-azure"></a>Testare l'app in Azure 
Questo articolo illustra i passaggi necessari a testare l'applicazione in Azure con DevTest Labs. In primo luogo, impostare una condivisione file all'interno di un lab e montarlo come unità nel computer di sviluppo locale e come macchina virtuale all'interno di un lab. È quindi possibile utilizzare Visual Studio 2017 per distribuire l'app per la condivisione file in modo che sia possibile eseguirla sulla macchina virtuale nel lab.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti 
1. [Creare una sottoscrizione di Azure](https://azure.microsoft.com/free/) se non se ne possiede già una e accedere al [portale di Azure](https://portal.azure.com).
2. Seguire le istruzioni contenute in [questo articolo](devtest-lab-create-lab.md) per creare un lab tramite Azure DevTest Labs. Aggiungere il lab alla dashboard in modo da poterla ritrovare facilmente al prossimo accesso. Azure DevTest Labs consente di creare rapidamente risorse in Azure riducendo al minimo gli sprechi e controllando i costi. Per altre informazioni su DevTest Labs, vedere la [panoramica](devtest-lab-overview.md). 
3. Creare un account di Archiviazione di Azure nel gruppo di risorse del lab seguendo le istruzioni dell'articolo: [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md). All'interno della pagina **Creare un account di archiviazione**, selezionare **Usa esistente** per **Gruppo di risorse** e selezionare il **gruppo di risorse del lab**. 
4. Creare una condivisione file in archiviazione di Azure seguendo le istruzioni dell'articolo [Creare una condivisione file in File di Azure](../storage/files/storage-how-to-create-file-share.md). 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montare la condivisione file nel computer locale
1. Nel computer locale, usare lo script preso dalla sezione [Salvataggio permanente delle credenziali della condivisione file di Azure in Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) dell'articolo [Usare una condivisione file di Azure con Windows](../storage/files/storage-how-to-use-files-windows.md). 
2. Usare quindi il comando `net use` per montare la condivisione file nel computer. Il comando di esempio è il seguente: specificare il nome di archiviazione di Azure e il nome della condivisione file prima di eseguire il comando. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Creare una VM nel lab
1. Alla pagina **Condivisione file**, selezionare il **gruppo di risorse** all'interno del menu di navigazione nella parte superiore. Viene visualizzata la pagina del **Gruppo di risorse**. 
    
    ![Selezionare il gruppo di risorse dal menu di navigazione](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Alla pagina **Gruppo di risorse**, selezionare il **lab** creato in DevTest Labs.

    ![Selezionare il lab](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Nella pagina **DevTest Labs** selezionare **+ Aggiungi** sulla barra degli strumenti. 

    ![Aggiungere un pulsante per il lab](media/test-app-in-azure/add-button-in-lab.png)
4. Nella pagina **Scegli una base**, cercare **smalldisk** e selezionare **[smalldisk] Windows Server 2016 Data Center**. 

    ![Scegliere small disk Windows server](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. All'interno della pagina **Macchina virtuale**, specificare **nome macchina virtuale**, **nome utente**, **password** e selezionare **Crea**.    
    
    ![Creare una pagina macchina virtuale](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montare la condivisione file nella macchina virtuale
1. Dopo averla creata, selezionare la **macchina virtuale** dall'elenco.    

    ![Selezionare il lab della macchina virtuale](media/test-app-in-azure/select-lab-vm.png)
2. Selezionare **Connetti** sulla barra degli strumenti per connettersi alla macchina virtuale. 
3. [Installare Azure PowerShell](/powershell/azure/install-az-ps).
4. Seguire le istruzioni nella sezione Montare la condivisione file. 

## <a name="publish-your-app-from-visual-studio"></a>Pubblicare l'app Web da Visual Studio
In questa sezione, verrà mostrato come pubblicare l'app da Visual Studio a una macchina virtuale di test nel cloud.

1. Creare un'applicazione desktop o Web usando Visual Studio 2017.
2. Creare l'app.
3. Per pubblicare l'app, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**. 
4. In **Pubblicazione guidata**, immettere l'**unità** mappata alla condivisione file.

    **App desktop:**

    ![App desktop](media/test-app-in-azure/desktop-app.png)

    **App Web:**

    ![app Web](media/test-app-in-azure/web-app.png)

1. Selezionare **Avanti** per completare il flusso di lavoro e selezionare **Fine**. Al termine della procedura guidata, Visual Studio compila l'applicazione e la pubblica nella condivisione file. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testare l'app all'interno della macchina virtuale di test nel lab

1. Esplorare la pagina macchina virtuale per la macchina virtuale nel lab. 
2. Selezionare **Avvia** sulla barra degli strumenti per avviare la macchina virtuale se questa si trova in stato di arresto. È possibile impostare i criteri di avvio automatico e di arresto automatico per la macchina virtuale per evitare di effettuare ogni volta queste due operazioni manualmente. 
3. Selezionare **Connessione**.

    ![Informazioni sulla pagina macchina virtuale](media/test-app-in-azure/virtual-machine-page.png)
4. All'interno della macchina virtuale, avviare **Esplora file**e selezionare **Computer** per trovare la condivisione file.

    ![Trovare la condivisione nella macchina virtuale](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Se per qualsiasi motivo non si riesce a trovare la condivisione file nella macchina virtuale o nel computer locale, è possibile rimontarla, eseguendo il comando `net use`. Viene visualizzato il comando `net use` all'interno della procedura guidata di **Connessione** di **Condivisione file** nel portale di Azure.
1. Aprire la condivisione file e verificare la visualizzazione dell'app distribuita in precedenza da Visual Studio. 

    ![Apri condivisione su macchina virtuale](media/test-app-in-azure/open-file-share.png)

    È ora possibile accedere e testare l'app all'interno della macchina virtuale di test creata in Azure.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per informazioni sull'utilizzo di macchine virtuali in un lab. 

- [Aggiungere una VM a un lab](devtest-lab-add-vm.md)
- [Riavviare una macchina virtuale di lab](devtest-lab-restart-vm.md)
- [Ridimensionare una macchina virtuale di lab](devtest-lab-resize-vm.md)
