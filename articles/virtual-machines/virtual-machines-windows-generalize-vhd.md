---
title: Generalizzare un disco rigido virtuale di Windows | Microsoft Docs
description: Informazioni su come usare Sysprep per generalizzare una VM Windows da usare con il modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a745d400-c8be-48b4-a891-4a18495ef3fd
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9e9d973ef36cde4f0a1ada2ba7bf7d01a8d8f687


---
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizzare una macchina virtuale Windows mediante Sysprep
Questa sezione illustra come generalizzare la macchina virtuale di Windows da usare come immagine. Sysprep rimuove anche tutte le informazioni sull'account personale e prepara la VM da usare come immagine. Per altre informazioni su Sysprep, vedere [Come usare Sysprep: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Assicurarsi che i ruoli server in esecuzione sulla macchina siano supportati da Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se si esegue Sysprep prima di caricare il disco rigido virtuale in Azure per la prima volta, verificare di aver [preparato la VM](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) prima di eseguire Sysprep. 
> 
> 

1. Accedere alla macchina virtuale Windows.
2. Aprire la finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.
3. Nella finestra di dialogo **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che la casella di controllo **Generalizza** sia selezionata.
4. In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
5. Fare clic su **OK**.
   
    ![Avvio di Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)
6. Al termine, Sysprep arresta la macchina virtuale. 

## <a name="next-steps"></a>Passaggi successivi
* Se la VM è locale, è possibile [caricare il disco rigido virtuale in Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Se la VM si trova già in Azure, è possibile [creare un'immagine dalla VM generalizzata](virtual-machines-windows-capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


