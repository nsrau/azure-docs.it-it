---
title: PowerShell-come creare immagini di VM con Packer
description: Informazioni su come usare Packer e PowerShell per creare immagini di macchine virtuali in Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: 16d0a4ea2353778b16803bd9b92a7f8af6e4ba88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325913"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: come usare Packer per creare immagini di macchine virtuali in Azure
Ogni macchina virtuale (VM, Virtual Machine) in Azure viene creata a partire da un'immagine che ne definisce la distribuzione di Windows e la versione del sistema operativo. Le immagini possono includere applicazioni e configurazioni preinstallate. In Microsoft Azure Marketplace sono disponibili molte prime immagini e immagini di terze parti per i sistemi operativi e gli ambienti applicativi più diffusi. In alternativa, è possibile creare immagini personalizzate su misura per le proprie esigenze. Questo articolo illustra in dettaglio come definire e compilare immagini personalizzate in Azure tramite lo strumento open source [Packer](https://www.packer.io/).

Questo articolo è stato testato per l'ultima volta il 8/5/2020 usando [Packer](https://www.packer.io/docs/install) versione 1.6.1.

> [!NOTE]
> Azure include ora Azure Image Builder, un servizio disponibile in anteprima per la definizione e la creazione di immagini personalizzate. Azure Image Builder è basato su Packer, di conseguenza può essere usato persino con gli script di provisioning della shell Packer esistenti. Per iniziare a usare Azure Image Builder, vedere [Creare una macchina virtuale Windows con Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Creare un gruppo di risorse di Azure
Durante il processo di compilazione della macchina virtuale di origine Packer crea risorse di Azure temporanee. Per acquisire la macchina virtuale di origine per usarla come immagine, è necessario definire un gruppo di risorse, nel quale verrà archiviato l'output del processo di compilazione di Packer.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *myPackerGroup* nella posizione *eastus* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Creare credenziali di Azure
Per eseguire l'autenticazione con Azure, Packer usa un'entità servizio. Un'entità servizio di Azure è un'identità di sicurezza che è possibile usare con le app, con i servizi e con strumenti di automazione come Packer. Le autorizzazioni per le operazioni che l'entità servizio può eseguire in Azure vengono controllate e definite dall'utente.

Creare un'entità servizio con [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Il valore di `-DisplayName` deve essere univoco. Se necessario, sostituirlo con uno personalizzato.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Vengono quindi visualizzati la password e l'ID applicazione.

```powershell
$plainPassword
$sp.ApplicationId
```


Per eseguire l'autenticazione in Azure è anche necessario ottenere l'ID del tenant e l'ID della sottoscrizione di Azure con [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definire un modello di Packer
Per compilare immagini, è necessario creare un modello come file JSON. Nel modello si definiscono i compilatori e gli strumenti di provisioning che eseguono il processo di compilazione effettivo. Packer è dotato di un [generatore per Azure](https://www.packer.io/docs/builders/azure.html) che consente di definire risorse di Azure, ad esempio le credenziali delle entità servizio create nel passaggio precedente.

Creare un file con nome *windows.json* e incollare al suo interno il contenuto seguente. Immettere valori personalizzati per i parametri seguenti:

| Parametro                           | Origine |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | ID entità servizio di visualizzazione con `$sp.applicationId` |
| *client_secret*                     | Password generata automaticamente con `$plainPassword` |
| *tenant_id*                         | Output del comando `$sub.TenantId` |
| *subscription_id*                   | Output del comando `$sub.SubscriptionId` |
| *managed_image_resource_group_name* | Nome del gruppo di risorse creato nel primo passaggio |
| *managed_image_name*                | Nome per l'immagine del disco gestito che viene creata |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Questo modello compila una macchina virtuale di Windows Server 2016, installa IIS e quindi generalizza la macchina virtuale con Sysprep. L'installazione IIS mostra come usare lo strumento di provisioning di PowerShell per eseguire altri comandi. L'immagine finale di Packer include quindi l'installazione e la configurazione del software necessarie.

L'agente guest di Windows partecipa al processo Sysprep. Per poter Sysprep la macchina virtuale, è necessario che l'agente sia installato completamente. Per assicurarsi che il valore sia true, tutti i servizi agente devono essere in esecuzione prima di eseguire sysprep.exe. Il frammento di codice JSON precedente mostra un modo per eseguire questa operazione nel provisioning di PowerShell. Questo frammento è necessario solo se la macchina virtuale è configurata per l'installazione dell'agente, che è l'impostazione predefinita.


## <a name="build-packer-image"></a>Compilare l'immagine in Packer
Se Packer non è installato nel computer locale, [seguire le istruzioni di installazione di Packer](https://learn.hashicorp.com/packer/getting-started/install).

Compilare l'immagine aprendo un prompt dei comandi e specificando il file del modello di Packer come specificato di seguito:

```
./packer build windows.json
```

Ecco un esempio di output dei comandi precedenti:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer impiega alcuni minuti per compilare la macchina virtuale, eseguire gli strumenti di provisioning e pulire la distribuzione.


## <a name="create-a-vm-from-the-packer-image"></a>Creare una macchina virtuale dall'immagine Packer
È ora possibile creare una macchina virtuale dall'immagine con [New-AzVM](/powershell/module/az.compute/new-azvm). Se non esistono già, vengono create le rispettive risorse di rete di supporto. Quando richiesto, immettere un nome utente e una password di amministratore da creare nella macchina virtuale. L'esempio seguente crea una macchina virtuale denominata *myVM* da *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Per creare macchine virtuali in un gruppo di risorse o un'area diversa rispetto all'immagine Packer, specificare l'ID immagine anziché il nome. È possibile ottenere l'ID immagine con [Get-AzImage](/powershell/module/az.compute/get-azimage).

La creazione della macchina virtuale dall'immagine Packer richiede alcuni minuti.


## <a name="test-vm-and-webserver"></a>Testare la macchina virtuale e il server Web
Ottenere l'indirizzo IP pubblico della macchina virtuale con [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Nell'esempio seguente si ottiene l'indirizzo IP per *myPublicIP* creato in precedenza:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Per vedere in azione la macchina virtuale, con l'installazione IIS dello strumento di provisioning Packer, immettere l'indirizzo IP pubblico in un Web browser.

![Sito IIS predefinito](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Passaggi successivi
È anche possibile usare gli script dello strumento di provisioning di Packer esistenti con [Azure Image Builder](image-builder.md).
