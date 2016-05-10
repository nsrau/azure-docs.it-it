<properties
	pageTitle="Creare un set di scalabilità di macchine virtuali | Microsoft Azure"
	description="Creare un set di scalabilità di macchine virtuali tramite PowerShell"
	services="virtual-machine-scale-sets"
    documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Creare un set di scalabilità di macchine virtuali Windows tramite Azure PowerShell

Questa procedura per la creazione di un set di scalabilità di macchine virtuali di Azure segue un approccio basato sul completamento di valori predefiniti. In tutto l'articolo sono presenti variabili in cui è necessario inserire valori. Sostituire tutti gli elementi all'interno delle virgolette con i valori appropriati per la sottoscrizione e l'applicazione.

## Passaggio 1: installare Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Passaggio 2: Impostare la sottoscrizione

1. Avviare un prompt di PowerShell.

2. Accedere al proprio account:

        Login-AzureRmAccount

3. Ottenere la propria sottoscrizione:

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. Impostare come corrente la sottoscrizione che si vuole usare:

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## Passaggio 2: Creare le risorse

Creare le risorse necessarie per il nuovo set di scalabilità di macchine virtuali.

### Gruppo di risorse

Un set di scalabilità di macchine virtuali deve trovarsi all'interno di un gruppo di risorse.

1.  Eseguire questo comando per ottenere un elenco di posizioni disponibili e i servizi supportati:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Verrà visualizzata una schermata analoga alla seguente

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    Selezionare la posizione più adatta alle proprie esigenze e quindi sostituire il testo tra virgolette con il nome di questa posizione:

        $locName = "location name from the list, such as Central US"

4. Sostituire il testo tra virgolette con il nome che si vuole usare per il nuovo gruppo di risorse e quindi crearlo nella posizione impostata in precedenza:

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Verrà visualizzata una schermata analoga alla seguente:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Account di archiviazione

Le macchine virtuali create in un set di scalabilità richiedono un account di archiviazione per l'archiviazione dei dischi associati.

1. Sostituire il testo tra virgolette con il nome che si vuole usare per l'account di archiviazione e quindi verificare che sia univoco:

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    Se la risposta è **False**, il nome proposto è univoco.

2. Sostituire il testo tra virgolette con il tipo di account di archiviazione e quindi creare l'account con il nome e la posizione impostati in precedenza. I valori possibili sono: Standard\_LRS, Standard\_GRS, Standard\_RAGRS e Premium\_LRS:

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Verrà visualizzata una schermata analoga alla seguente:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Rete virtuale

Per le macchine virtuali nel set di scalabilità è necessaria una rete virtuale.

1. Sostituire il testo tra virgolette con il nome che si vuole usare per la subnet della rete virtuale e quindi creare la configurazione:

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. Sostituire il testo tra virgolette con il nome che si vuole usare per la rete virtuale e quindi crearla usando le informazioni e le risorse definite in precedenza:

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Indirizzo IP pubblico

Prima di creare un'interfaccia di rete, è necessario creare un indirizzo IP pubblico.

1. Sostituire il testo tra virgolette con l'etichetta del nome di dominio che si vuole usare con l'indirizzo IP pubblico e quindi verificare se il nome è univoco. L'etichetta può contenere solo lettere, numeri e trattini. L'ultimo carattere deve essere una lettera o un numero.

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Se la risposta è **False**, il nome proposto è univoco.

2. Sostituire il testo tra virgolette con il nome che si vuole usare per l'indirizzo IP pubblico e quindi crearlo:

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interfaccia di rete

Ora che l'indirizzo IP pubblico è disponibile, è possibile creare l'interfaccia di rete.

1. Sostituire il testo tra virgolette con il nome che si vuole usare per l'interfaccia di rete e quindi crearla usando le risorse create in precedenza:

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### Creare il set di scalabilità di macchine virtuali

Ora che si dispone di tutte le risorse necessarie è il momento di creare il set di scalabilità.

1. Sostituire il testo tra virgolette con il nome che si vuole usare per la configurazione IP e quindi crearla:

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Sostituire il testo tra virgolette con il nome che si vuole usare per la configurazione del set di scalabilità e quindi crearlo. Questo passaggio comprende anche l'impostazione delle dimensioni, a cui si fa riferimento tramite SkuName, delle macchine virtuali nel set. Vedere la pagina relativa alle [dimensioni delle macchine virtuali](..\virtual-machines\virtual-machines-windows-sizes.md) per individuare dimensioni in grado di soddisfare le proprie esigenze. Per questo esempio è consigliabile usare Standard\_A0.:

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    Verrà visualizzata una schermata analoga alla seguente:

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. Sostituire il testo tra virgolette per il prefisso del nome computer che si vuole usare, per il nome dell'account amministratore delle macchine virtuali e per la password dell'account, quindi creare il profilo del sistema operativo:

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    La sezione osProfile dovrebbe essere simile a quanto segue:

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. Sostituire il testo tra virgolette con il nome che si vuole usare per il profilo di archiviazione, le informazioni dell'immagine e il percorso di archiviazione dei dischi delle macchine virtuali e quindi creare il profilo. Vedere [Esplorare e selezionare immagini di macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md) per trovare le informazioni necessarie:

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    La sezione storageProfile dovrebbe essere simile a quanto segue:

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. Sostituire il testo tra virgolette con il nome del set di scalabilità di macchina virtuali e quindi crearlo:

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Per indicare la riuscita della distribuzione verrà visualizzata una schermata simile a quella che segue:

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## Passaggio 3: Esplorare le risorse

Per esplorare il set di scalabilità di macchine virtuali appena creato, usare queste risorse:

- Portale di Azure: tramite il portale è disponibile una quantità limitata di informazioni.
- [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esaminare lo stato del set di scalabilità.
- Azure PowerShell: per ottenere informazioni, usare il comando seguente:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Passaggi successivi

1. Per altre informazioni, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

2. Prendere in considerazione la possibilità di impostare il ridimensionamento automatico del set di scalabilità usando le informazioni in [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md)

<!---HONumber=AcomDC_0427_2016-->