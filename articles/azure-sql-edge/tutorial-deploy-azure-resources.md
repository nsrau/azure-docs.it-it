---
title: Configurare le risorse per la distribuzione di un modello di Machine Learning in SQL Edge di Azure
description: Nella prima delle tre parti di questa esercitazione di SQL Edge di Azure sulla stima delle impurità del minerale di ferro, si installeranno i prerequisiti software e si configureranno le risorse di Azure necessarie per la distribuzione di un modello di Machine Learning in SQL Edge di Azure.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d83745db6c720a2fdc2260a07a4e3e66b1a0771d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422213"
---
# <a name="install-software-and-set-up-resources-for-the-tutorial"></a>Installare il software e configurare le risorse per l'esercitazione

In questa esercitazione in tre parti si creerà un modello di Machine Learning per stimare le impurità del minerale di ferro come percentuale di silice e quindi si distribuirà il modello in SQL Edge di Azure. Nella prima parte si installerà il software necessario e si distribuiranno le risorse di Azure.

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).
2. Installare Visual Studio 2019 con 
      * Azure IoT Edge Tools
      * Sviluppo multipiattaforma .NET Core
      * Strumenti di sviluppo contenitori
3. Installare [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio/)
4. Aprire Azure Data Studio e configurare Python per i notebook. Per informazioni dettagliate, vedere [Configurare Python per i notebook](/sql/azure-data-studio/sql-notebooks#configure-python-for-notebooks). Questo passaggio può richiedere alcuni minuti.
5. Installare la versione più recente dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-powershell/releases/tag/v3.5.0-February2020). Per gli script seguenti è necessaria la versione più recente di Azure PowerShell (3.5.0, febbraio 2020).
6. Impostare l'ambiente per eseguire e testare la soluzione IoT Edge ed effettuarne il debug installando [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/).
7. Installare Docker.
8. Scaricare il file [DACPAC](https://github.com/microsoft/sql-server-samples/tree/master/samples/demos/azure-sql-edge-demos/iron-ore-silica-impurities/DACPAC) che verrà usato nell'esercitazione. 

## <a name="deploy-azure-resources-using-powershell-script"></a>Distribuire le risorse di Azure usando lo script PowerShell

Distribuire le risorse di Azure richieste da questa esercitazione su SQL Edge di Azure. È possibile distribuirle usando uno script PowerShell o il portale di Azure. Questa esercitazione usa uno script PowerShell.

1. Importare i moduli necessari per eseguire lo script PowerShell in questa esercitazione.

   ```powershell
   Import-Module Az.Accounts -RequiredVersion 1.7.3
   Import-Module -Name Az -RequiredVersion 3.5.0
   Import-Module Az.IotHub -RequiredVersion 2.1.0
   Import-Module Az.Compute -RequiredVersion 3.5.0
   az extension add --name azure-iot
   az extension add --name azure-cli-ml
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]


2. Dichiarare le variabili richieste dallo script PowerShell.

   ```powershell
   $ResourceGroup = "<name_of_the_resource_group>"
   $IoTHubName = "<name_of_the_IoT_hub>"
   $location = "<location_of_your_Azure_Subscription>"
   $SubscriptionName = "<your_azure_subscription>"
   $NetworkSecGroup = "<name_of_your_network_security_group>"
   $StorageAccountName = "<name_of_your_storage_account>"
   ```

3. Dichiarare le variabili rimanenti.

   ```powershell
   $IoTHubSkuName = "S1"
   $IoTHubUnits = 4
   $EdgeDeviceId = "IronOrePredictionDevice"
   $publicIpName = "VMPublicIP"
   $imageOffer = "iot_edge_vm_ubuntu"
   $imagePublisher = "microsoft_iot_edge"
   $imageSku = "ubuntu_1604_edgeruntimeonly"
   $AdminAcc = "iotadmin"
   $AdminPassword = ConvertTo-SecureString "IoTAdmin@1234" -AsPlainText -Force
   $VMSize = "Standard_DS3"
   $NetworkName = "MyNet"
   $NICName = "MyNIC"
   $SubnetName = "MySubnet"
   $SubnetAddressPrefix = "10.0.0.0/24"
   $VnetAddressPrefix = "10.0.0.0/16"
   $MyWorkSpace = "SQLDatabaseEdgeDemo"
   $containerRegistryName = $ResourceGroup + "ContRegistry"
   ```

4. Per iniziare a creare asset, accedere ad Azure.

   ```powershell
   Login-AzAccount
   
   az login
   ```

5. Impostare l'ID sottoscrizione di Azure.

   ```powershell
   Select-AzSubscription -Subscription $SubscriptionName
   az account set --subscription $SubscriptionName
   ```

6. Creare il gruppo di risorse se non esiste già.

   ```powershell
   $rg = Get-AzResourceGroup -Name $ResourceGroup
   if($rg -eq $null)
   {
       Write-Output("Resource Group $ResourceGroup does not exist, creating Resource Gorup")
       New-AzResourceGroup -Name $ResourceGroup -Location $location
   }
   else
   {
       Write-Output ("Resource Group $ResourceGroup exists")
   }
   ```

7. Creare l'account di archiviazione e il contenitore dell'account di archiviazione nel gruppo di risorse.

   ```powershell
   $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
   if ($sa -eq $null)
   {
       New-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName -SkuName Standard_LRS -Location $location -Kind Storage
       $sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storagekey = Get-AzStorageAccountKey -ResourceGroupName $ResourceGroup -Name $StorageAccountName 
       $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $storagekey[0].Value
       New-AzStorageContainer -Name "sqldatabasedacpac" -Context $storageContext 
   }
   else
   {
      Write-Output ("Storage Account $StorageAccountName exists in Resource Group $ResourceGroup")     
   }
   ```

8. Caricare il file `dacpac` del database nell'account di archiviazione e generare un URL di firma di accesso condiviso per il BLOB. **Prendere nota dell'URL di firma di accesso condiviso per il BLOB `dacpac` del database.**

   ```powershell
   $file = Read-Host "Please Enter the location to the zipped Database DacPac file:"
   Set-AzStorageBlobContent -File $file -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context
   $DacpacFileSASURL = New-AzStorageBlobSASToken -Container "sqldatabasedacpac" -Blob "SQLDatabasedacpac.zip" -Context $sa.Context -Permission r -StartTime (Get-Date).DateTime -ExpiryTime (Get-Date).AddMonths(12) -FullUri
   ```

9. Creare un registro contenitori di Azure in questo gruppo di risorse.

   ```powershell
   $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   if ($containerRegistry -eq $null)
   {
       New-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName -Sku Standard -Location $location -EnableAdminUser 
       $containerRegistry = Get-AzContainerRegistry -ResourceGroupName $ResourceGroup -Name $containerRegistryName 
   }
   else
   {
       Write-Output ("Container Registry $containerRegistryName exists in Resource Group $ResourceGroup")
   }
   ```

10. Creare il gruppo di sicurezza di rete nel gruppo di risorse.

    ```powershell
    $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup 
    if($nsg -eq $null)
    {
        Write-Output("Network Security Group $NetworkSecGroup does not exist in the resource group $ResourceGroup")
    
        $rule1 = New-AzNetworkSecurityRuleConfig -Name "SSH" -Description "Allow SSH" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22
        $rule2 = New-AzNetworkSecurityRuleConfig -Name "SQL" -Description "Allow SQL" -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1600
        New-AzNetworkSecurityGroup -Name $NetworkSecGroup -ResourceGroupName $ResourceGroup -Location $location -SecurityRules $rule1, $rule2
    
        $nsg = Get-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroup -Name $NetworkSecGroup
    }
    else
    {
        Write-Output ("Network Security Group $NetworkSecGroup exists in the resource group $ResourceGroup")
    }
    ```

11. Creare una macchina virtuale di Azure abilitata con SQL Edge. Questa macchina virtuale fungerà da dispositivo perimetrale.

    ```powershell
    $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
    If($AzVM -eq $null)
    {
        Write-Output("The Azure VM with Name- $EdgeVMName is not present in the Resource Group- $ResourceGroup ")
    
        $SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
        $Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroup -Location $location -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
        $publicIp = New-AzPublicIpAddress -Name $publicIpName -ResourceGroupName $ResourceGroup -AllocationMethod Static -Location $location  
        $NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroup -Location $location -SubnetId $Vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $publicIp.Id
         
        ##Set-AzNetworkInterfaceIpConfig -Name "ipconfig1"  -NetworkInterface $NIC -PublicIpAddress $publicIp
    
        $Credential = New-Object System.Management.Automation.PSCredential ($AdminAcc, $AdminPassword);
    
        $VirtualMachine = New-AzVMConfig -VMName $EdgeDeviceId -VMSize $VMSize
        $VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $EdgeDeviceId -Credential $Credential
        $VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id  
        $VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $imagePublisher -Offer $imageOffer -Skus $imageSku -Version latest 
        $VirtualMachine = Set-AzVMPlan -VM $VirtualMachine -Name $imageSku -Publisher $imagePublisher -Product $imageOffer
    
        $AzVM = New-AzVM -ResourceGroupName $ResourceGroup -Location $location -VM $VirtualMachine -Verbose
        $AzVM = Get-AzVM -ResourceGroupName $ResourceGroup -Name $EdgeDeviceId
          
    }
    else
    {
        Write-Output ("The Azure VM with Name- $EdgeDeviceId is present in the Resource Group- $ResourceGroup ")
    }
    ```

12. Creare un hub IoT nel gruppo di risorse.

    ```powershell
    $iotHub = Get-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName
    If($iotHub -eq $null)
    {
        Write-Output("IoTHub $IoTHubName does not exists, creating The IoTHub in the resource group $ResourceGroup")
        New-AzIotHub -ResourceGroupName $ResourceGroup -Name $IoTHubName -SkuName $IoTHubSkuName -Units $IoTHubUnits -Location $location -Verbose
    }
    else
    {
        Write-Output ("IoTHub $IoTHubName present in the resource group $ResourceGroup") 
    }
    ```

13. Aggiungere un dispositivo perimetrale all'hub IoT. Questo passaggio crea solo l'identità digitale del dispositivo.

    ```powershell
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    If($deviceIdentity -eq $null)
    {
        Write-Output("The Edge Device with DeviceId- $EdgeDeviceId is not registered to the IoTHub- $IoTHubName ")
        Add-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId -EdgeEnabled  
    }
    else
    {
        Write-Output ("The Edge Device with DeviceId- $EdgeDeviceId is registered to the IoTHub- $IoTHubName")
    }
    $deviceIdentity = Get-AzIotHubDevice -ResourceGroupName $ResourceGroup -IotHubName $IoTHubName -DeviceId $EdgeDeviceId
    ```

14. Ottenere la stringa di connessione primaria del dispositivo. Questa stringa sarà necessaria in seguito per la macchina virtuale. Il comando seguente usa l'interfaccia della riga di comando di Azure per le distribuzioni.

    ```powershell
    $deviceConnectionString = az iot hub device-identity show-connection-string --device-id $EdgeDeviceId --hub-name $IoTHubName --resource-group $ResourceGroup --subscription $SubscriptionName
    $connString = $deviceConnectionString[1].Substring(23,$deviceConnectionString[1].Length-24)
    $connString
    ```

15. Aggiornare la stringa di connessione nel file di configurazione di IoT Edge sul dispositivo perimetrale. I comandi seguenti usano l'interfaccia della riga di comando di Azure per le distribuzioni.

    ```powershell
    $script = "/etc/iotedge/configedge.sh '" + $connString + "'"
    az vm run-command invoke -g $ResourceGroup -n $EdgeDeviceId  --command-id RunShellScript --script $script
    ```

16. Creare un'area di lavoro di Azure Machine Learning nel gruppo di risorse.

    ```powershell
    az ml workspace create -w $MyWorkSpace -g $ResourceGroup
    ```


## <a name="next-steps"></a>Passaggi successivi

* [Configurare i moduli e le connessioni IoT Edge](tutorial-set-up-iot-edge-modules.md)
