---
title: Distribuire macchine virtuali nel dispositivo Azure Stack Edge tramite modelli
description: Viene descritto come creare e gestire macchine virtuali (VM) in un dispositivo Azure Stack Edge utilizzando i modelli.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 330186b12efcc31e9f99d7c4bdbff3e081311c23
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085493"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-templates"></a>Distribuire macchine virtuali nel dispositivo Azure Stack Edge tramite modelli

Questa esercitazione descrive come creare e gestire una macchina virtuale nel dispositivo Azure Stack Edge usando i modelli. Questi modelli sono file JavaScript Object Notation (JSON) che definiscono l'infrastruttura e la configurazione per la VM. In questi modelli si specificano le risorse da distribuire e le proprietà di tali risorse.

I modelli sono flessibili in ambienti diversi, in quanto possono assumere parametri come input in fase di esecuzione da un file. La struttura di denominazione standard è `TemplateName.json` per il modello e `TemplateName.parameters.json` per il file dei parametri. Per altre informazioni sui modelli ARM, vedere [che cosa sono i modelli di Azure Resource Manager?](../azure-resource-manager/templates/overview.md).

In questa esercitazione verranno usati modelli di esempio pre-scritti per la creazione di risorse. Non è necessario modificare il file modello ed è possibile modificare solo i `.parameters.json` file per personalizzare la distribuzione nel computer. 

## <a name="vm-deployment-workflow"></a>Flusso di lavoro di distribuzione VM

Per distribuire le macchine virtuali Azure Stack Edge in molti dispositivi, è possibile usare un singolo disco rigido virtuale preparata con Sysprep per la flotta completa, lo stesso modello per la distribuzione e apportare solo modifiche minime ai parametri del modello per ogni percorso di distribuzione (queste modifiche possono essere apportate in modo manuale o a livello di codice). 

Il riepilogo generale del flusso di lavoro di distribuzione con i modelli è il seguente:

1. **Configurare i prerequisiti** : sono disponibili tre tipi di prerequisiti: dispositivo, client e per la macchina virtuale.

    1. **Prerequisiti del dispositivo**

        1. Connettersi a Azure Resource Manager nel dispositivo.
        2. Abilitare il calcolo tramite l'interfaccia utente locale.

    2. **Prerequisiti client**

        1. Scaricare i modelli di macchina virtuale e i file associati nel client.
        1. Facoltativamente, configurare TLS 1,2 nel client.
        1. [Scaricare e installare Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) nel client.

    3. **Prerequisiti della macchina virtuale**

        1. Creare un gruppo di risorse nel percorso del dispositivo che conterrà tutte le risorse della macchina virtuale.
        1. Creare un account di archiviazione per caricare il disco rigido virtuale usato per creare l'immagine di macchina virtuale.
        1. Aggiungere l'URI dell'account di archiviazione locale a DNS o al file hosts nel client che accede al dispositivo.
        1. Installare il certificato di archiviazione BLOB nel dispositivo e nel client locale che accede al dispositivo. Facoltativamente, installare il certificato di archiviazione BLOB nella Storage Explorer.
        1. Creare e caricare un disco rigido virtuale nell'account di archiviazione creato in precedenza.

2. **Creare una macchina virtuale da modelli**

    1. Creare un'immagine di macchina virtuale e una VNet usando il `CreateImageAndVnet.parameters.json` file dei parametri e il `CreateImageAndVnet.json` modello di distribuzione.
    1. Creare una VM con risorse create in precedenza usando il `CreateVM.parameters.json` file di parametri e il  `CreateVM.json` modello di distribuzione.

## <a name="device-prerequisites"></a>Prerequisiti del dispositivo

Configurare questi prerequisiti nel dispositivo Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Prerequisiti client

Configurare questi prerequisiti nel client che verranno usati per accedere al dispositivo Azure Stack Edge.

1. [Scaricare Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se viene usato per caricare un disco rigido virtuale. In alternativa, è possibile scaricare AzCopy per caricare un disco rigido virtuale. Potrebbe essere necessario configurare TLS 1,2 nel computer client se si eseguono versioni precedenti di AzCopy. 
1. [Scaricare i modelli di macchina virtuale e i file di parametri](https://aka.ms/ase-vm-templates) nel computer client. Decomprimerlo in una directory che verrà usata come directory di lavoro.


## <a name="vm-prerequisites"></a>Prerequisiti della macchina virtuale

Configurare questi prerequisiti per creare risorse che saranno necessarie per la creazione di macchine virtuali. 

    
### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui vengono distribuite e gestite le risorse di Azure, ad esempio account di archiviazione, disco, disco gestito.

> [!IMPORTANT]
> Tutte le risorse vengono create nella stessa posizione del dispositivo e la posizione è impostata su **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Di seguito è riportato un output di esempio.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Creare un nuovo account di archiviazione usando il gruppo di risorse creato nel passaggio precedente. Si tratta di un **account di archiviazione locale** che verrà usato per caricare l'immagine del disco virtuale per la VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Solo gli account di archiviazione locali, ad esempio l'archiviazione con ridondanza locale (Standard_LRS o Premium_LRS), possono essere creati tramite Azure Resource Manager. Per creare account di archiviazione a più livelli, vedere la procedura in [aggiungere, connettersi agli account di archiviazione sul Azure stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Di seguito è riportato un output di esempio.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Per ottenere la chiave dell'account di archiviazione, eseguire il `Get-AzureRmStorageAccountKey` comando. Di seguito è riportato un esempio di output di questo comando.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Aggiungi URI BLOB al file hosts

Assicurarsi di aver già aggiunto l'URI del BLOB nel file hosts per il client usato per connettersi all'archiviazione BLOB. **Eseguire blocco note come amministratore** e aggiungere la voce seguente per l'URI del BLOB in `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

In un ambiente tipico, il DNS è configurato in modo che tutti gli account di archiviazione puntino al dispositivo Azure Stack Edge con una `*.blob.devicename.domainname.com` voce.

### <a name="optional-install-certificates"></a>Opzionale Installare i certificati

Ignorare questo passaggio se si effettuerà la connessione tramite Storage Explorer tramite *http*. Se si usa *https*, è necessario installare i certificati appropriati in Storage Explorer. In questo caso, installare il certificato dell'endpoint BLOB. Per ulteriori informazioni, vedere How to create and upload Certificates in [Manage Certificates](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Creare e caricare un disco rigido virtuale

Assicurarsi di disporre di un'immagine del disco virtuale che è possibile usare per caricare il passaggio successivo. Seguire la procedura descritta in [creare un'immagine di macchina virtuale](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Copiare le immagini del disco da usare nei BLOB di pagine nell'account di archiviazione locale creato nei passaggi precedenti. È possibile usare uno strumento come [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) o [AzCopy per caricare il disco rigido virtuale nell'account di archiviazione](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) creato nei passaggi precedenti. 

### <a name="use-storage-explorer-for-upload"></a>USA Storage Explorer per il caricamento

1. Aprire Esplora archivi. Passare a **Edit (modifica** ) e assicurarsi che l'applicazione sia impostata su **target Azure stack API**.

    ![Impostare la destinazione su API Azure Stack](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Installare il certificato client in formato PEM. Passare a **modificare > certificati SSL > importare i certificati**. Puntare al certificato client.

    ![Importa certificato dell'endpoint di archiviazione BLOB](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Se si usano i certificati generati dal dispositivo, scaricare e convertire il certificato dell'endpoint di archiviazione BLOB `.cer` in un `.pem` formato. Eseguire il comando indicato di seguito. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Se si sta portando un certificato, usare il certificato radice della catena di firma in `.pem` formato.

3. Dopo aver importato il certificato, riavviare Storage Explorer per rendere effettive le modifiche.

    ![Riavviare Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. Nel riquadro sinistro fare clic con il pulsante destro del mouse su **account di archiviazione** e selezionare **Connetti ad archiviazione di Azure**. 

    ![Connettersi ad archiviazione di Azure 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selezionare **Usare un nome e una chiave dell'account di archiviazione**. Selezionare **Avanti**.

    ![Connettersi ad archiviazione di Azure 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Nella pagina **Connetti con nome e chiave**specificare il **nome visualizzato**, il **nome dell'account di archiviazione**e la chiave dell' **account**di archiviazione di Azure. Selezionare **altro** dominio di archiviazione e quindi specificare la `<device name>.<DNS domain>` stringa di connessione. Se non è stato installato un certificato in Storage Explorer, selezionare l'opzione **USA http** . Selezionare **Avanti**.

    ![Connetti con nome e chiave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Esaminare il **Riepilogo della connessione** e selezionare **Connetti**.

8. L'account di archiviazione viene visualizzato nel riquadro sinistro. Selezionare ed espandere l'account di archiviazione. Selezionare **contenitori BLOB**, fare clic con il pulsante destro del mouse e scegliere **Crea contenitore BLOB**. Specificare un nome per il contenitore BLOB.

9. Selezionare il contenitore appena creato e fare clic su **carica > carica file**nel riquadro di destra. 

    ![Carica file VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Individuare e puntare al disco rigido virtuale che si desidera caricare nei **file selezionati**. Selezionare **tipo di BLOB** come **BLOB di pagine** e selezionare **carica**.

    ![Carica file VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Una volta caricato il disco rigido virtuale nel contenitore BLOB, selezionare il disco rigido virtuale, fare clic con il pulsante destro del mouse e scegliere **Proprietà**. 

    ![Carica file VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copiare e salvare l' **URI** come verrà usato nei passaggi successivi.

    ![Copia URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Creare un'immagine e un VNet per la macchina virtuale

Per creare un'immagine e una rete virtuale per la macchina virtuale, sarà necessario modificare il `CreateImageAndVnet.parameters.json` file dei parametri e quindi distribuire il modello `CreateImageAndVnet.json` che usa questo file di parametri.


### <a name="edit-parameters-file"></a>Modificare il file dei parametri

Il file `CreateImageAndVnet.parameters.json` accetta i parametri seguenti: 

```json
"parameters": {
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
      "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Modificare il file `CreateImageAndVnet.parameters.json` in modo da includere quanto segue per il dispositivo Azure stack Edge:

1. Specificare il tipo di sistema operativo corrispondente al disco rigido virtuale che si desidera caricare. Il tipo di sistema operativo può essere Windows o Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Modificare l'URI dell'immagine nell'URI dell'immagine caricata nel passaggio precedente:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Se si usa *http* con Storage Explorer, modificare questo valore in un URI *https* .

3. Modificare `addressPrefix` e `subnetPrefix` . Nell'interfaccia utente locale del dispositivo andare alla pagina **rete** . Trovare la porta abilitata per il calcolo. Ottenere l'indirizzo IP della rete di base e aggiungere la subnet mask per creare la notazione CIDR. Se si dispone di una subnet 255.255.255.0 standard, effettuare questa operazione sostituendo l'ultimo numero dell'indirizzo IP con 0 e aggiungendo/24 alla fine. Quindi, 10.126.68.0 con un subnet mask 255.255.255.0 diventa 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Specificare il nome dell'immagine univoco, il nome VNet e il nome della subnet per i parametri.

    Di seguito è riportato un esempio JSON usato in questo articolo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Salvare il file dei parametri.


### <a name="deploy-template"></a>Distribuire il modello 

Distribuire il modello `CreateImageAndVnet.json` . Questo modello consente di distribuire le risorse VNet e image che verranno usate per creare le macchine virtuali nel passaggio successivo.

> [!NOTE]
> Quando si distribuisce il modello se viene ricevuto un errore di autenticazione, è possibile che le credenziali di Azure per questa sessione siano scadute. Eseguire `login-AzureRM` nuovamente il comando per connettersi di nuovo a Azure Resource Manager sul dispositivo Azure stack Edge.

1. Eseguire il comando seguente: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Verificare se è stato effettuato il provisioning dell'immagine e delle risorse VNet. Di seguito è riportato un esempio di output di un'immagine creata correttamente e di VNet.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Creare una macchina virtuale

### <a name="edit-parameters-file-to-create-vm"></a>Modificare il file dei parametri per creare una macchina virtuale
 
Per creare una macchina virtuale, usare il `CreateVM.parameters.json` file dei parametri. Accetta i parametri seguenti.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Assegnare i parametri appropriati in `CreateVM.parameters.json` per il dispositivo Azure stack Edge.

1. Specificare un nome univoco, il nome dell'interfaccia di rete e il nome ipconfig. 
1. Immettere un nome utente, una password e una dimensione di macchina virtuale supportata.
1. Assegnare lo stesso nome per **VnetName**, **subnetName**e **ImageName** come specificato nei parametri per `CreateImageAndVnet.parameters.json` . Ad esempio, se sono stati specificati VnetName, subnetName e ImageName come **vnet1**, **Subnet1**e **image1**, conservarli anche per i parametri nel modello.
1. A questo punto è necessario un indirizzo IP statico da assegnare alla VM che si trova nella rete subnet definita in precedenza. Sostituire **PrivateIPAddress** con questo indirizzo nel file dei parametri. Per fare in modo che la macchina virtuale ottenga un indirizzo IP dal server DCHP locale, lasciare `privateIPAddress` vuoto il valore.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Salvare il file dei parametri.

    Di seguito è riportato un esempio JSON usato in questo articolo.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Distribuire un modello per creare una macchina virtuale

Distribuire il modello di creazione della macchina virtuale `CreateVM.json` . Questo modello crea un'interfaccia di rete dal VNet esistente e crea una macchina virtuale dall'immagine distribuita.

1. Eseguire il comando seguente: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "RG1"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    La creazione della macchina virtuale può richiedere 15-20 minuti. Di seguito è riportato un esempio di output di una macchina virtuale creata correttamente.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
 
7. Controllare se è stato eseguito correttamente il provisioning della macchina virtuale. Eseguire il comando seguente:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Connettersi a una macchina virtuale

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Operazioni e cmdlet non supportati per le macchine virtuali

Le estensioni, i set di scalabilità, i set di disponibilità e gli snapshot non sono supportati.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge device.

On the client used to access your Azure Stack Edge device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Passaggi successivi

[Cmdlet di Azure Resource Manager](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
