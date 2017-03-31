---
title: "Creare una VM (classica) con più schede di interfaccia di rete - Azure PowerShell | Microsoft Docs"
description: "Informazioni su come creare una VM (classica) con più schede di interfaccia di rete mediante PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 6e2bb0e228aa28c79969cba07352061abbb47951
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Creare una macchina virtuale (classica) con più schede di interfaccia di rete usando PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

È possibile creare macchine virtuali (VM) in Azure e collegare più interfacce di rete (NIC) a ciascuna delle macchine virtuali. Più schede di interfaccia rete consentono la separazione dei tipi di traffico tra schede di interfaccia di rete. Ad esempio, una scheda di interfaccia di rete può comunicare con Internet, mentre un'altra comunica solo con le risorse interne non connesse a Internet. La possibilità di separare il traffico di rete tra più schede di interfaccia di rete è necessaria per molte appliance virtuali di rete, ad esempio soluzioni di ottimizzazione WAN e di distribuzione delle applicazioni.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come seguire questa procedura con il [modello di distribuzione Resource Manager](virtual-network-deploy-multinic-arm-ps.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per i server Web e un gruppo di risorse denominato *IaaSStory-BackEnd* per i server di database.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare i server di database, è necessario creare il gruppo di risorse *IaaSStory* con tutte le risorse richieste per questo scenario. Per creare le risorse, seguire questa procedura. Creare una rete virtuale seguendo la procedura riportata nell'articolo [Creare una rete virtuale](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Creare le macchine virtuali di back-end
Le macchine virtuali di back-end dipendono dalla creazione delle risorse seguenti:

* **Subnet Back-end**. I server del database formeranno parte di una subnet distinta, per separare il traffico. Lo script seguente prevede che questa subnet sia presente in una rete virtuale denominata *WTestVnet*.
* **Account di archiviazione per dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare la posizione di Azure che viene distribuita per supportare l'archiviazione premium.
* **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.

### <a name="step-1---start-your-script"></a>Passaggio 1 - avviare lo script
È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passaggio 2 - creare le risorse necessarie per le macchine virtuali
È necessario creare un nuovo servizio cloud e un account di archiviazione per i dischi di dati per tutte le macchine virtuali. È inoltre necessario specificare un'immagine e un account amministratore locale per le macchine virtuali. Per creare le risorse, seguire questa procedura:

1. Creare un nuovo servizio cloud

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Creare un account di archiviazione premium

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Impostare l'account di archiviazione creato in precedenza come account di archiviazione corrente per la sottoscrizione.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Selezionare un'immagine per la macchina virtuale.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Impostare le credenziali dell'account amministratore locale

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Passaggio 3 - creare delle macchine virtuali
È necessario utilizzare un ciclo per creare tutte le macchine virtuali che si desidera e creare le schede di rete e le macchine virtuali necessarie all'interno del ciclo. Per creare le schede di rete e le macchine virtuali, eseguire questa procedura.

1. Avviare un `for` ciclo per ripetere i comandi per la creazione di una macchina virtuale e di due schede di rete tutte le volte che lo si ritiene necessario, in base al valore della `$numberOfVMs` variabile.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Creare un `VMConfig` oggetto che specifica l'immagine, le dimensioni e il set di disponibilità per la macchina virtuale.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Eseguire il provisioning della macchina virtuale come macchina virtuale di Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.Password
    ```

4. Impostare il valore predefinito NIC e assegnare un indirizzo IP statico.

    ```powershell
    Set-AzureSubnet            -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP     -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Aggiungere una seconda scheda di rete per ogni macchina virtuale.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```

6. Creare dischi di dati per ogni macchina virtuale.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Creare ogni macchina virtuale e terminare il ciclo.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Passaggio 4 - eseguire lo script.
Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal prompt dei comandi **PowerShell** o **PowerShell ISE**. Verrà visualizzato l'output iniziale, come illustrato di seguito.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Compilare le informazioni necessarie nella richiesta di credenziali e fare clic su **OK**. Viene restituito l'output seguente.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

