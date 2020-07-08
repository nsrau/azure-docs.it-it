---
title: Caricare un file VHD in Azure DevTest Labs usando PowerShell | Microsoft Docs
description: Questo articolo fornisce una procedura dettagliata che illustra come caricare un file VHD in Azure DevTest Labs usando PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ff8ee15f35765027db1407f05c108739bd97433e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480780"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Caricare un file VHD nell'account di archiviazione del lab usando PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs è possibile usare i file VHD per creare immagini personalizzate da utilizzare per il provisioning di macchine virtuali. La procedura seguente illustra come usare PowerShell per caricare un file VHD nell'account di archiviazione di un lab. Dopo avere caricato il file VHD, vedere la [sezione Passaggi successivi](#next-steps) per un elenco di articoli che illustrano come creare un'immagine personalizzata dal file VHD caricato. Per altre informazioni sui dischi e dischi rigidi virtuali in Azure, vedere [Introduzione ai dischi gestiti](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come caricare un file VHD in Azure DevTest Labs usando PowerShell. 

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **tutti i servizi**, quindi selezionare **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel pannello del lab selezionare **Configurazione**. 

1. Nel pannello **Configurazione** del lab selezionare **Immagini personalizzate (dischi rigidi virtuali)**.

1. Nel pannello **Immagini personalizzate** selezionare **+Aggiungi**. 

1. Nel pannello **Immagine personalizzata** selezionare **VHD**.

1. Nel pannello **VHD** selezionare l'opzione **Carica un file VHD con PowerShell**.

    ![Carica un file VHD con PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Nel pannello **Carica un'immagine con PowerShell** copiare in un editor di testo lo script di PowerShell generato.

1. Modificare il parametro **LocalFilePath** del cmdlet **Add-AzureVhd** in modo che punti al percorso del file VHD che si vuole caricare.

1. Al prompt di PowerShell eseguire il cmdlet **Add-AzureVhd** (con il parametro **LocalFilePath** modificato).

> [!WARNING] 
> 
> Il processo di caricamento di un file VHD può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando il portale di Azure](devtest-lab-create-template.md)
- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
