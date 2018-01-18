---
title: Filtrare il traffico di rete con i gruppi di sicurezza di rete e delle applicazioni di Azure (anteprima) | Microsoft Docs
description: Informazioni su come creare gruppi di sicurezza di rete e delle applicazioni (anteprima) per limitare il tipo di traffico di rete in entrata e in uscita delle macchine virtuali.
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ac9a1a8c59a26393d32f9c543e630c302b7ced9d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrare il traffico di rete con i gruppi di sicurezza di rete e delle applicazioni (anteprima)

In questa esercitazione, viene descritto come creare gruppi di sicurezza di rete per filtrare il traffico di rete da e verso i gruppi di macchine virtuali con i gruppi di sicurezza delle applicazioni. Per altre informazioni sui gruppi di sicurezza di rete e delle applicazioni, vedere l'articolo [Panoramica sulla sicurezza](security-overview.md). 

Le sezioni seguenti includono la procedura da eseguire per creare gruppi di sicurezza di rete con, l'[interfaccia della riga di comando di Azure](#azure-cli) e [Azure PowerShell](#powershell). Il risultato è lo stesso, indipendentemente dallo strumento usato per creare i gruppi di sicurezza di rete. Fare clic sul collegamento relativo a uno strumento per passare alla sezione corrispondente dell'esercitazione. 

In questo articolo viene descritta la procedura per creare i gruppi di sicurezza di rete tramite il modello di distribuzione di Gestione risorse, ovvero il modello di distribuzione consigliato durante la creazione dei gruppi di sicurezza di rete. Se si vuole creare un gruppo di sicurezza di rete classico, vedere l'articolo [Creare un gruppo di sicurezza di rete (classico)](virtual-networks-create-nsg-classic-ps.md). Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Questa esercitazione usa le funzioni del gruppo di sicurezza rete disponibili attualmente nella versione di anteprima. Le funzionalità nella versione di anteprima non offrono lo stesso livello di disponibilità e affidabilità delle funzionalità in versione della versione generale. Se si desidera implementare i gruppi di sicurezza di rete usando solo le funzionalità della versione generale, vedere [Creare un gruppo di sicurezza di rete](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

I comandi dell'interfaccia della riga di comando di Azure sono gli stessi, indipendentemente dall'esecuzione dei comandi stessi in Windows, Linux o macOS. Esistono tuttavia differenze di scripting tra le shell dei sistemi operativi. Gli script nei passaggi seguenti vengono eseguiti in una shell Bash. 

1. [Installare e configurare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Assicurarsi di usare la versione 2.0.18 o successiva dell'interfaccia della riga di comando di Azure immettendo il comando `az --version`. Se non si usa questa versione, installare la versione più recente.
3. Accedere ad Azure con il comando `az login`.
4. Registrare all'anteprima immettendo i comandi seguenti:
    
    ```azurecli
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    az provider register --namespace Microsoft.Network
    ``` 

5. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

    ```azurecli
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    > [!WARNING]
    > La registrazione può richiedere fino a un'ora. Non proseguire con i passaggi rimanenti finché non compare *Registered* per **state** nell'output restituito dal comando precedente. Se si continua prima di essersi registrati, i passaggi rimanenti avranno esito negativo.

6. Eseguire lo script bash seguente per creare un gruppo di risorse:

    ```azurecli
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Creare tre gruppi di sicurezza delle applicazioni, uno per ogni tipo di server:

    ```azurecli
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Creare un gruppo di sicurezza di rete:

    ```azurecli
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Creare regole di sicurezza entro il gruppo di sicurezza di rete, impostando i gruppi di sicurezza dell'applicazione come destinazione:
    
    ```azurecli    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Creare una rete virtuale: 
    
    ```azurecli
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Associare il gruppo di sicurezza di rete alla subnet nella rete virtuale:

    ```azurecli
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Creare tre interfacce di rete, uno per ogni tipo di server: 

    ```azurecli
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Solo la regola di sicurezza corrispondente creata nel passaggio 9 viene applicata all'interfaccia di rete, in base al gruppo di sicurezza delle applicazioni di cui fa parte l'interfaccia di rete. Ad esempio, solo la regola *AppRule* è efficace per *myNic2*, perché l'interfaccia di rete fa parte del gruppo di sicurezza delle applicazioni *AppServers* e la regola specifica il gruppo di sicurezza delle applicazioni *AppServers* come destinazione. Le regole *WebRule* e *DatabaseRule* non si applicano a *myNic2*, perché l'interfaccia di rete non fa parte dei gruppi di sicurezza delle applicazioni *WebServers* e *DatabaseServers*. Tuttavia, entrambe le regole *WebRule* e *AppRule* sono efficaci per *myNic1*, perché l'interfaccia di rete *myNic1* fa parte di entrambi i gruppi di sicurezza delle applicazioni *WebServers* e *AppServers* e le regole specificano i gruppi di sicurezza delle applicazioni *WebServers* e *AppServers* come destinazioni. 

13. Creare una macchina virtuale per ogni tipo di server, associando l'interfaccia di rete corrispondente a ogni macchina virtuale. Questo esempio consente di creare macchine virtuali Windows, ma è possibile modificare *win2016datacenter* in *UbuntuLTS* per creare invece macchine virtuali Linux.

    ```azurecli
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword \
      --no-wait

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare le risorse](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Installare e configurare [PowerShell](/powershell/azure/install-azurerm-ps).
2. Assicurarsi di aver installato la versione 4.4.0 o successiva del modulo AzureRM. È possibile controllare la versione attualmente installata immettendo il comando `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'installazione o l'aggiornamento, installare la versione più recente del modulo AzureRM da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM).
3. In una sessione di PowerShell, accedere ad Azure con l'[account Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando il comando `login-azurermaccount`.
4. Registrare all'anteprima immettendo i comandi seguenti:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Verificare di essere registrati per l'anteprima immettendo il comando seguente:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    > [!WARNING]
    > La registrazione può richiedere fino a un'ora. Non proseguire con i passaggi rimanenti finché non compare *Registered* (Registrato) per **RegistrationState** nell'output restituito dal comando precedente. Se si continua prima di essersi registrati, i passaggi rimanenti avranno esito negativo.
        
6. Creare un gruppo di risorse:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Creare tre gruppi di sicurezza delle applicazioni, uno per ogni tipo di server:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Creare le regole di sicurezza per ogni tipo di server.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80
    
    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443
      
    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336
    ``` 

9. Creare un gruppo di sicurezza di rete:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Creare una configurazione subnet e associarvi il gruppo di sicurezza di rete:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Creare una rete virtuale: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Creare tre interfacce di rete, uno per ogni tipo di server. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Solo la regola di sicurezza corrispondente creata nel passaggio 8 viene applicata all'interfaccia di rete, in base al gruppo di sicurezza delle applicazioni di cui fa parte l'interfaccia di rete. Ad esempio, solo la regola *AppRule* è efficace per *myNic2*, perché l'interfaccia di rete fa parte del gruppo di sicurezza delle applicazioni *AppServers* e la regola specifica il gruppo di sicurezza delle applicazioni *AppServers* come destinazione. Le regole *WebRule* e *DatabaseRule* non si applicano a *myNic2*, perché l'interfaccia di rete non fa parte dei gruppi di sicurezza delle applicazioni *WebServers* e *DatabaseServers*. Tuttavia, entrambe le regole *WebRule* e *AppRule* sono efficaci per *myNic1*, perché l'interfaccia di rete *myNic1* fa parte di entrambi i gruppi di sicurezza delle applicazioni *WebServers* e *AppServers* e le regole specificano i gruppi di sicurezza delle applicazioni *WebServers* e *AppServers* come destinazioni. 

13. Creare una macchina virtuale per ogni tipo di server, associando l'interfaccia di rete corrispondente a ogni macchina virtuale. Questo esempio consente di creare macchine virtuali Windows, ma prima di eseguire lo script, è possibile modificare *-Windows* in *- Linux*, *MicrosoftWindowsServer* in *Canonical*, *WindowsServer* in *UbuntuServer* e *2016-Datacenter* in *14.04.2-LTS* per creare invece macchine virtuali Linux.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Facoltativo:** per eliminare le risorse create in questa esercitazione, completare la procedura descritta in [Eliminare le risorse](#delete-cli).

## <a name="remove-a-nic-from-an-asg"></a>Rimuovere una scheda di interfaccia di rete da un ASG
Dopo aver rimosso un'interfaccia di rete da un gruppo di sicurezza delle applicazioni, nessuna delle regole che specificano il gruppo di sicurezza delle applicazioni si applica all'interfaccia di rete rimossa.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per rimuovere *myNic3* da tutti i gruppi di sicurezza delle applicazioni, immettere il comando seguente:

```azurecli
az network nic update \
  --name myNic3 \
  --resource-group myResourceGroup \
  --remove ipConfigurations[0].applicationSecurityGroups
```

### <a name="powershell"></a>PowerShell

Per rimuovere *myNic3* da tutti i gruppi di sicurezza delle applicazioni, immettere i comandi seguenti:

```powershell
$nic=Get-AzureRmNetworkInterface `
  -Name myNic3 `
  -ResourceGroupName myResourceGroup

$nic.IpConfigurations[0].ApplicationSecurityGroups = $null
$nic | Set-AzureRmNetworkInterface 
```

## <a name="delete"></a>Eliminare risorse

Al termine di questa esercitazione, è possibile eliminare le risorse che sono state create per non incorrere in costi di utilizzo. Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse all'interno di esso.

### <a name="delete-portal"></a>Portale di Azure

1. Nella casella di ricerca del portale immettere **myResourceGroup**. Nei risultati della ricerca fare clic su **myResourceGroup**.
2. Nel pannello **myResourceGroup** fare clic sull'icona **Elimina**.
3. Per confermare l'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi fare clic su **Elimina**.

### <a name="delete-cli"></a>

Nella sessione dell'interfaccia della riga di comando immettere il comando seguente:

```azurecli
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Nella sessione di PowerShell immettere il comando seguente:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

