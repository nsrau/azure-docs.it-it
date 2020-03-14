---
title: Red Hat Enterprise Linux immagini di Azure Bring your own Subscription | Microsoft Docs
description: Informazioni sulle immagini Bring-your-own-Subscription per Red Hat Enterprise Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264596"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux le immagini Gold Bring your own Subscription in Azure

Le immagini di Red Hat Enterprise Linux (RHEL) sono disponibili in Azure tramite un modello con pagamento in base al consumo o bring your own Subscription (BYOS) (Red Hat Gold Image). Questo articolo fornisce una panoramica delle immagini Red Hat Gold in Azure.

>[!NOTE]
> Le immagini RHEL BYOS Gold sono disponibili nel cloud pubblico (commerciale) di Azure e in Azure per enti pubblici. Non sono disponibili nei Cloud Azure Cina o Azure Blackforest.

## <a name="important-points-to-consider"></a>Punti importanti da considerare

- Le immagini Red Hat Gold fornite in questo programma sono immagini RHEL pronte per l'ambiente di produzione simili alle immagini con pagamento in base al consumo di RHEL in Azure Marketplace.
- Le immagini seguono i criteri correnti descritti in [Red Hat Enterprise Linux immagini in Azure](./redhat-images.md).
- I criteri di supporto standard si applicano alle macchine virtuali create da queste immagini.
- Le macchine virtuali di cui è stato effettuato il provisioning da immagini Red Hat Gold non contengono tariffe RHEL associate alle immagini RHEL con pagamento in base al consumo.
- Le immagini non sono autorizzate. È necessario usare Red Hat Subscription-Manager per registrare e sottoscrivere le VM per ottenere gli aggiornamenti direttamente da Red Hat.
- Attualmente non è possibile passare dinamicamente tra i modelli di fatturazione BYOS e con pagamento in base al consumo per le immagini Linux. Per cambiare il modello di fatturazione, è necessario ridistribuire la macchina virtuale dalla rispettiva immagine.

>[!NOTE]
> Le immagini RHEL BYOS di seconda generazione non sono attualmente disponibili tramite l'offerta Marketplace. Se è necessaria un'immagine di generazione 2 RHEL BYOS, visitare il dashboard di accesso al cloud in Red Hat Subscription Management. Per ulteriori informazioni, vedere la [documentazione di Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisiti e condizioni per accedere alle immagini Red Hat Gold

1. Acquisire familiarità con le condizioni del [programma Red Hat cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Abilitare le sottoscrizioni di Red Hat per l'accesso cloud a [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). È necessario avere a disposizione le sottoscrizioni di Azure che verranno registrate per l'accesso al cloud.

1. Se le sottoscrizioni Red Hat abilitate per l'accesso al cloud soddisfano i requisiti di idoneità, le sottoscrizioni di Azure vengono abilitate automaticamente per l'accesso alle immagini Gold.

### <a name="expected-time-for-image-access"></a>Tempo previsto per l'accesso alle immagini

Al termine della procedura di abilitazione dell'accesso al cloud, Red Hat convalida l'idoneità per le immagini Red Hat Gold. Se la convalida ha esito positivo, si riceve l'accesso alle immagini Gold entro tre ore.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Usare le immagini Red Hat Gold dal portale di Azure

1. Quando la sottoscrizione di Azure riceve l'accesso alle immagini Red Hat Gold, è possibile individuarle nel [portale di Azure](https://portal.azure.com). Passare a **Crea una risorsa** > **vedere tutti**.

1. Nella parte superiore della pagina si noterà che si dispone di offerte private.

    ![Offerte private del Marketplace](./media/rhel-byos-privateoffers.png)

1. Selezionare il collegamento viola o scorrere fino alla fine della pagina per visualizzare le offerte private.

1. Il resto del provisioning nell'interfaccia utente non è diverso da qualsiasi altra immagine Red Hat esistente. Scegliere la versione RHEL e seguire le istruzioni per eseguire il provisioning della macchina virtuale. Questo processo consente inoltre di accettare le condizioni dell'immagine nel passaggio finale.

>[!NOTE]
>Questa procedura fino a questo punto non consentirà l'immagine Red Hat Gold per la distribuzione a livello di codice. È necessario eseguire un passaggio aggiuntivo, come descritto nella sezione "informazioni aggiuntive".

Il resto di questo documento è incentrato sul metodo dell'interfaccia della riga di comando per il provisioning e l'accettazione di termini nell'immagine. L'interfaccia della riga di comando e l'interfaccia della riga di comando sono completamente intercambiabili per quanto riguarda il risultato finale, ovvero una macchina virtuale di immagini RHEL Gold con provisioning.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Usare le immagini Red Hat Gold dall'interfaccia della riga di comando di Azure

Le istruzioni seguenti illustrano il processo di distribuzione iniziale per una macchina virtuale RHEL usando l'interfaccia della riga di comando di Azure. Queste istruzioni presuppongono che sia [installata l'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli)della riga di comando di Azure.

>[!IMPORTANT]
>Assicurarsi di usare tutte le lettere minuscole nei riferimenti al server di pubblicazione, all'offerta, al piano e all'immagine per tutti i comandi seguenti.

1. Verificare di trovarsi nella sottoscrizione desiderata.

    ```azurecli
    az account show -o=json
    ```

1. Creare un gruppo di risorse per la macchina virtuale dell'immagine Red Hat Gold.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Accettare le condizioni per l'immagine.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Questi termini devono essere accettati *una sola volta per ogni sottoscrizione di Azure, per ogni SKU di immagine*.

1. Opzionale Convalidare la distribuzione della macchina virtuale con il comando seguente:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Eseguire il provisioning della VM eseguendo lo stesso comando, come illustrato nell'esempio precedente senza l'argomento `--validate`.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Connettersi tramite SSH alla macchina virtuale e verificare di disporre di un'immagine non autorizzata. Per eseguire questo passaggio, eseguire `sudo yum repolist`. Per RHEL 8 usare `sudo dnf repolist`. L'output richiede di usare Subscription-Manager per registrare la macchina virtuale con Red Hat.

>[!NOTE]
>In RHEL 8, `dnf` e `yum` sono intercambiabili. Per ulteriori informazioni, vedere la [Guida dell'amministratore di RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Usare le immagini Red Hat Gold da PowerShell

Di seguito è riportato uno script di esempio. Sostituire il gruppo di risorse, la posizione, il nome della macchina virtuale, le informazioni di accesso e altre variabili con la configurazione scelta. Le informazioni sul server di pubblicazione e sul piano devono essere minuscole.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Crittografare Red Hat Enterprise Linux immagini Gold Bring your own Subscription

Red Hat Enterprise Linux le immagini Gold BYOS possono essere protette tramite l'uso di [crittografia dischi di Azure](../../linux/disk-encryption-overview.md). Prima di poter abilitare la crittografia, è *necessario* che la sottoscrizione sia registrata. Per altre informazioni su come registrare un'immagine di RHEL BYOS Gold, vedere [come registrare e sottoscrivere un sistema nel portale per i clienti di Red Hat con Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se si dispone di una sottoscrizione attiva di Red Hat, è anche possibile leggere la pagina relativa alla [creazione di chiavi di attivazione di Red Hat Customer Portal](https://access.redhat.com/articles/1378093).

Crittografia dischi di Azure non è supportata nelle [Immagini personalizzate di Red Hat](../../linux/redhat-create-upload-vhd.md). I requisiti e i prerequisiti aggiuntivi di crittografia dischi di Azure sono documentati in [crittografia dischi di Azure per macchine virtuali Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Per i passaggi per l'applicazione di crittografia dischi di Azure, vedere [scenari di crittografia dischi di Azure nelle macchine virtuali Linux](../../linux/disk-encryption-linux.md) e articoli correlati.

## <a name="additional-information"></a>Informazioni aggiuntive

- Se si tenta di effettuare il provisioning di una macchina virtuale in una sottoscrizione non abilitata per questa offerta, viene ricevuto il messaggio seguente:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    In questo caso, contattare Microsoft o Red Hat per abilitare la sottoscrizione.

- Se si modifica uno snapshot da un'immagine RHEL BYOS e si tenta di pubblicare l'immagine personalizzata nella [raccolta di immagini condivise](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), è necessario fornire informazioni sul piano corrispondenti all'origine dello snapshot. Ad esempio, il comando potrebbe essere simile al seguente:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Prendere nota dei parametri del piano nella riga finale.

    [Crittografia dischi di Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) non è supportata nelle immagini personalizzate.

- Se si usa l'automazione per eseguire il provisioning di macchine virtuali dalle immagini RHEL BYOS, è necessario specificare parametri del piano simili a quelli mostrati nei comandi di esempio. Se ad esempio si usa la bonifica, le informazioni sul piano vengono fornite in un [blocco di piano](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Passaggi successivi

- Per le guide dettagliate e i dettagli del programma per l'accesso al cloud, vedere la [documentazione di Red Hat cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Per altre informazioni su Red Hat Update Infrastructure, vedere [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Per ulteriori informazioni su tutte le immagini Red Hat in Azure, vedere la [pagina della documentazione](./redhat-images.md).
- Per informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL, vedere la pagina relativa al [ciclo di vita del Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
- Per la documentazione aggiuntiva sulle immagini RHEL Gold, vedere la [documentazione di Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
