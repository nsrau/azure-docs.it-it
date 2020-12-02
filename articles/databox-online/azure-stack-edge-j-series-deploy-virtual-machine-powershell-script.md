---
title: Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell script
description: Viene descritto come creare e gestire macchine virtuali (VM) in un dispositivo Azure Stack Edge Pro usando uno script di Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 957274e6080ab4b77e6528386fe0b24b2d877219
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447311"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Distribuire le VM sul dispositivo GPU Azure Stack Edge Pro tramite Azure PowerShell script

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questa esercitazione descrive come creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge Pro usando uno script di Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge Pro usando questo script, è necessario assicurarsi di aver completato i prerequisiti elencati nei passaggi seguenti:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Per Azure Stack dispositivo Edge Pro tramite l'interfaccia utente Web locale

1. Sono state completate le impostazioni di rete nel dispositivo Azure Stack Edge Pro, come descritto in [Passaggio 1: Configurare il dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. È stata abilitata un'interfaccia di rete per il calcolo. L'indirizzo IP di questa interfaccia di rete viene usato per creare uno switch virtuale per la distribuzione della VM. I passaggi seguenti illustrano il processo:

    1. Passare alle **impostazioni di calcolo**. Selezionare l'interfaccia di rete che si userà per creare uno switch virtuale.

        > [!IMPORTANT] 
        > È possibile configurare una sola porta per il calcolo.

    2. Abilitare il calcolo nell'interfaccia di rete. Azure Stack Edge Pro crea e gestisce una rete virtuale corrispondente a tale interfaccia di rete.

3. Sono stati creati e installati tutti i certificati sul dispositivo Azure Stack Edge Pro e nell'archivio radice attendibile del client. Seguire la procedura descritta in [Passaggio 2: Creare e installare i certificati](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Per il client Windows

1. Il protocollo VIP (Virtual Internet Protocol) dei servizi coerenti di Azure è stato definito nella pagina della **rete** nell'interfaccia utente Web locale del dispositivo. È necessario aggiungere questo indirizzo VIP a:

    - Il file host nel client OPPURE
    - La configurazione del server DNS
    
    > [!IMPORTANT]
    > È consigliabile modificare la configurazione del server DNS per la risoluzione dei nomi di endpoint.

    1. Avviare il **Blocco note** come amministratore (per salvare il file sono necessari privilegi di amministratore), quindi aprire il file **hosts** nella cartella `C:\Windows\System32\Drivers\etc`.
    
        ![File hosts di Esplora risorse](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Aggiungere le voci seguenti al file **hosts** sostituendo i valori con quelli appropriati per il dispositivo:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Per l'account di archiviazione, è possibile specificare un nome che si desidera venga usato dallo script in un secondo momento per creare un nuovo account di archiviazione. Lo script non controlla se l'account di archiviazione è esistente.

    3. Usare l'immagine seguente per riferimento. Salvare il file **hosts**.

        ![File hosts nel Blocco note](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Scaricare lo script di PowerShell](https://aka.ms/ase-vm-powershell) usato in questa procedura.

3. Verificare che il client Windows esegua PowerShell 5,0 o versione successiva.

4. Verificare che nel `Azure.Storage Module version 4.5.0` sistema sia installato. È possibile ottenere questo modulo dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Per installare questo modulo, digitare:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Per verificare la versione del modulo installato, digitare:

    `Get-InstalledModule -name Azure.Storage`

    Per disinstallare qualsiasi altro modulo della versione, digitare:

    `Uninstall-Module -Name Azure.Storage`

5. [Scaricare AzCopy 10](../storage/common/storage-use-azcopy-v10.md#download-azcopy) nel client Windows. Prendere nota di questa posizione perché verrà passata come parametro durante l'esecuzione dello script.

6. Verificare che il client Windows esegua TLS 1,2 o versione successiva.


## <a name="create-a-vm"></a>Creare una macchina virtuale

1. Eseguire PowerShell come amministratore.
2. Passare alla cartella in cui è stato scaricato lo script nel client.  
3. Usare questo comando per eseguire lo script:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Ecco gli esempi quando viene eseguito lo script per creare una macchina virtuale Windows e una VM Linux.

    **Per una macchina virtuale Windows:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Per una VM Linux:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Per pulire le risorse create dallo script, usare i comandi seguenti:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Passaggi successivi

[Distribuire macchine virtuali con i cmdlet di Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)