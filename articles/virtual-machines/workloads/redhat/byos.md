---
title: Red Hat Enterprise Linux bring-your-own-subscription Immagini di Azure Documenti Microsoft
description: Informazioni sulle immagini bring your own-subscription per Red Hat Enterprise Linux in Azure.Learn about bring your own-subscription images for Red Hat Enterprise Linux on Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264596"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux porta le immagini Gold in Azure con la sottoscrizione personale

Le immagini Red Hat Enterprise Linux (RHEL) sono disponibili in Azure tramite un modello BYOS (Pay-as-you-go o bring-your-own-subscription) (ByOS). Questo articolo offre una panoramica delle immagini d'oro del cappello rosso in Azure.This article provides an overview of the Red Hat Gold Images in Azure.

>[!NOTE]
> RHEL BYOS Gold Images sono disponibili nei cloud di Azure Public (commerciali) e Azure per enti pubblici. Non sono disponibili nelle nuvole di Azure Cina o di Azure Blackforest.They'do not available in Azure China or Azure Blackforest clouds.

## <a name="important-points-to-consider"></a>Punti importanti da considerare

- Le immagini Red Hat Gold fornite in questo programma sono immagini RHEL pronte per la produzione simili alle immagini rHEL con pagamento in base al numero di risorse in Azure Marketplace.
- Le immagini seguono i criteri correnti descritti in [Red Hat Enterprise Linux images on Azure](./redhat-images.md).
- I criteri di supporto standard si applicano alle macchine virtuali create da queste immagini.
- Le macchine virtuali di cui è stato eseguito il provisioning da Red Hat Gold Images non comportano costi RHEL associati alle immagini con pagamento in base al numero di utilizzi RHEL.
- Le immagini sono senza titolo. È necessario usare Red Hat Subscription-Manager per registrare e sottoscrivere le macchine virtuali per ottenere direttamente gli aggiornamenti da Red Hat.You must use Red Hat Subscription-Manager to register and subscribe the VMs to get updates from Red Hat directly.
- Al momento non è possibile passare dinamicamente tra BYOS e modelli di fatturazione con pagamento in base al pagamento per le immagini Linux. Per cambiare il modello di fatturazione, è necessario ridistribuire la macchina virtuale dalla rispettiva immagine.

>[!NOTE]
> Le immagini RHEL BYOS di generazione 2 non sono attualmente disponibili tramite l'offerta del marketplace. Se è necessaria un'immagine RHEL BYOS RHEL generazione 2, visitare il dashboard di accesso cloud in gestione delle sottoscrizioni Red Hat. Per ulteriori informazioni, vedere la [documentazione](https://access.redhat.com/articles/4847681)di Red Hat .

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisiti e condizioni per accedere alle immagini Red Hat Gold

1. Acquisire familiarità con i termini del [programma Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Abilitare gli abbonamenti Red Hat per Cloud Access presso [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). È necessario avere a portata di mano le sottoscrizioni di Azure che verranno registrate per l'accesso cloud.

1. Se le sottoscrizioni Red Hat abilitate per Cloud Access soddisfano i requisiti di idoneità, le sottoscrizioni di Azure vengono abilitate automaticamente per l'accesso a Gold Image.If the Red Hat subscriptions you enabled for Cloud Access meet the eligibility requirements, your Azure subscriptions are automatically enabled for Gold Image access.

### <a name="expected-time-for-image-access"></a>Tempo previsto per l'accesso alle immagini

Dopo aver completato i passaggi di abilitazione di Cloud Access, Red Hat convalida l'idoneità per le immagini d'oro del cappello rosso. Se la convalida ha esito positivo, si riceverà l'accesso alle immagini d'oro entro tre ore.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Usare le immagini d'oro del cappello rosso dal portale di AzureUse the Red Hat Gold Images from the Azure portal

1. Dopo che la sottoscrizione di Azure riceve l'accesso a Red Hat Gold Images, è possibile individuarle nel portale di [Azure.](https://portal.azure.com) Passare a **Crea una risorsa** > **Visualizza tutto**.

1. Nella parte superiore della pagina, vedrai che hai offerte private.

    ![Marketplace offerte private](./media/rhel-byos-privateoffers.png)

1. Seleziona il link viola o scorri verso il basso fino alla fine della pagina per vedere le tue offerte private.

1. Il resto del provisioning nell'interfaccia utente non è diverso da qualsiasi altra immagine Red Hat esistente. Scegliere la versione RHEL e seguire le istruzioni per eseguire il provisioning della macchina virtuale. Questo processo consente inoltre di accettare i termini dell'immagine nel passaggio finale.

>[!NOTE]
>Questi passaggi finora non abilitano l'immagine Red Hat Gold per la distribuzione a livello di codice. È necessario un passaggio aggiuntivo, come descritto nella sezione "Ulteriori informazioni".

Il resto di questo documento si concentra sul metodo CLI per eseguire il provisioning e accettare termini sull'immagine. L'interfaccia utente e l'interfaccia della riga di comando sono completamente intercambiabili per quanto riguarda il risultato finale (una macchina virtuale RHEL Gold Image di cui è stato eseguito il provisioning).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Usare le immagini di Red Hat Gold dell'interfaccia della riga di comando di AzureUse the Red Hat Gold Images from the Azure CLI

Le istruzioni seguenti illustrano il processo di distribuzione iniziale per una macchina virtuale RHEL usando l'interfaccia della riga di comando di Azure.The following instructions guide you through the initial deployment process for a RHEL VM by using the Azure CLI. Queste istruzioni presuppongono che sia [installata l'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/install-azure-cli)di Azure.

>[!IMPORTANT]
>Assicurati di utilizzare tutte le lettere minuscole nei riferimenti publisher, offerta, piano e immagine per tutti i comandi seguenti.

1. Verificare di essere nell'abbonamento desiderato.

    ```azurecli
    az account show -o=json
    ```

1. Creare un gruppo di risorse per la macchina virtuale Red Hat Gold Image.Create a resource group for your Red Hat Gold Image VM.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Accettare i termini dell'immagine.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Questi termini devono essere accettati una volta per ogni sottoscrizione di *Azure, per SKU di immagini.*

1. (Facoltativo) Convalidare la distribuzione della macchina virtuale con il comando seguente:Validate your VM deployment with the following command:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Effettuare il provisioning della macchina virtuale eseguendo lo `--validate` stesso comando illustrato nell'esempio precedente senza l'argomento.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH nella macchina virtuale e verificare di disporre di un'immagine senza diritto. Per eseguire questo `sudo yum repolist`passaggio, eseguire . Per RHEL 8, utilizzare `sudo dnf repolist`. The output asks you to use Subscription-Manager to register the VM with Red Hat.

>[!NOTE]
>Su RHEL `dnf` 8, e `yum` sono intercambiabili. Per ulteriori informazioni, vedere la [guida per l'amministratore di RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Usare le immagini Red Hat Gold di PowerShellUse the Red Hat Gold Images from PowerShell

Di seguito è riportato uno script di esempio. Sostituire il gruppo di risorse, il percorso, il nome della macchina virtuale, le informazioni di accesso e altre variabili con la configurazione desiderata. Le informazioni sul server di pubblicazione e sul piano devono essere minuscole.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Crittografa le immagini Gold di Red Hat Enterprise Linux

Red Hat Enterprise Linux Linux BYOS Gold Images può essere protetto tramite l'uso di Crittografia disco di [Azure.](../../linux/disk-encryption-overview.md) La sottoscrizione *deve* essere registrata prima di poter abilitare la crittografia. Per ulteriori informazioni su come registrare un'immagine D'oro RHEL BYOS, vedere [Come registrare e sottoscrivere un sistema al portale clienti Red Hat utilizzando Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se si dispone di un abbonamento Red Hat attivo, è anche possibile leggere [Creazione di codici di attivazione del portale clienti Red Hat](https://access.redhat.com/articles/1378093).

Crittografia disco di Azure non è supportata nelle [immagini personalizzate Red Hat](../../linux/redhat-create-upload-vhd.md). Ulteriori requisiti e prerequisiti di Crittografia dischi di Azure sono documentati in Crittografia disco di Azure per macchine virtuali Linux.Additional Azure Disk Encryption requirements and prerequisites are documented in [Azure Disk Encryption for Linux VMs](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Per la procedura per applicare Crittografia disco di Azure, vedere Scenari di Crittografia disco di [Azure in macchine virtuali Linux](../../linux/disk-encryption-linux.md) e articoli correlati.

## <a name="additional-information"></a>Informazioni aggiuntive

- Se si tenta di eseguire il provisioning di una macchina virtuale in una sottoscrizione non abilitata per questa offerta, viene visualizzato il messaggio seguente:If you attempt to provision a VM on a subscription that isn't enabled for this offer, you get the following message:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    In questo caso, contattare Microsoft o Red Hat per abilitare l'abbonamento.

- Se si modifica un'istantanea da un'immagine RHEL BYOS e si tenta di pubblicare l'immagine personalizzata nella [Raccolta immagini condivise](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), è necessario fornire informazioni sul piano corrispondenti all'origine originale dello snapshot. Ad esempio, il comando potrebbe essere simile al seguente:For example, the command might look like this:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Prendere nota dei parametri del piano nella riga finale.

    [Crittografia disco di Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) non è supportata nelle immagini personalizzate.

- Se si usa l'automazione per eseguire il provisioning delle macchine virtuali dalle immagini BYOS RHEL, è necessario fornire parametri del piano simili a quelli visualizzati nei comandi di esempio. Ad esempio, se si utilizza Terraform, si forniscono le informazioni sul piano in un [blocco di piano.](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)

## <a name="next-steps"></a>Passaggi successivi

- Per le guide dettagliate e i dettagli del programma per Cloud Access, vedere la [documentazione](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)di Red Hat Cloud Access .
- Per altre informazioni sull'infrastruttura Red Hat Update, vedere [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Per altre informazioni su tutte le immagini di Red Hat in Azure, vedere la pagina della [documentazione](./redhat-images.md).
- Per informazioni sui criteri di supporto red Hat per tutte le versioni di RHEL, vedere la pagina del ciclo di vita di [Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Per ulteriori documentazione sulle immagini RHEL Gold, vedere la [documentazione](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)di Red Hat .
