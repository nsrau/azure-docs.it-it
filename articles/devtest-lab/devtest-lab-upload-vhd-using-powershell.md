---
title: Caricare un file VHD in Azure DevTest Labs usando PowerShell | Documentazione Microsoft
description: Caricare un file VHD nell&quot;account di archiviazione del lab usando PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 38d70ed302eeab912ce4fe33272f85e96f1b1eda
ms.openlocfilehash: 36edca0969eb2895777a85dc4ee826abeb42601f


---

# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Caricare un file VHD nell'account di archiviazione del lab usando PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs è possibile usare i file VHD per creare immagini personalizzate da utilizzare per il provisioning di macchine virtuali. La procedura seguente illustra come usare PowerShell per caricare un file VHD nell'account di archiviazione di un lab. Dopo avere caricato il file VHD, vedere la [sezione Passaggi successivi](#next-steps) per un elenco di articoli che illustrano come creare un'immagine personalizzata dal file VHD caricato. Per altre informazioni sui dischi e sui dischi rigidi virtuali in Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../virtual-machines/virtual-machines-linux-about-disks-vhds.md)

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come caricare un file VHD in Azure DevTest Labs usando PowerShell. 

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel pannello del lab selezionare **Configurazione**. 

1. Nel pannello **Configuration** (Configurazione) del lab selezionare **Custom images (VHDs)** (Immagini personalizzate (VHD)).

1. Nel pannello **Custom images** (Immagini personalizzate) selezionare **+Add** (+Aggiungi). 

1. Nel pannello **Custom image** (Immagine personalizzata) selezionare **VHD**.

1. Nel pannello **VHD** selezionare l'opzione **Upload a VHD using PowerShell** (Carica un file VHD con PowerShell).

    ![Carica un file VHD con PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Nel pannello **Upload an image using PowerShell** (Carica un'immagine con PowerShell) copiare in un editor di testo lo script di PowerShell generato.

1. Modificare il parametro **LocalFilePath** del cmdlet **Add-AzureVhd** in modo che punti al percorso del file VHD che si vuole caricare.

1. Al prompt di PowerShell eseguire il cmdlet **Add-AzureVhd** (con il parametro **LocalFilePath** modificato).

> [!WARNING] 
> 
> Il processo di caricamento di un file VHD può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando il portale di Azure](devtest-lab-create-template.md)
- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)



<!--HONumber=Jan17_HO2-->


