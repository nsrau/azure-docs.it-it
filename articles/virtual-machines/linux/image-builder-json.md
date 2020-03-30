---
title: Creare un modello di Generatore immagini di Azure (anteprima)Create an Azure Image Builder template (preview)
description: Informazioni su come creare un modello da usare con Azure Image Builder.Learn how to create a template to use with Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246790"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Anteprima: Creare un modello di Generatore di immagini di AzurePreview: Create an Azure Image Builder template 

Azure Image Builder usa un file con estensione json per passare informazioni nel servizio Generatore immagini. In questo articolo esamineremo le sezioni del file json, in modo da poter ne creare una personalizzata. Per visualizzare esempi di file json completi, vedere GitHub di [Azure Image Builder.](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)

Questo è il formato del modello di base:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versione API

Il `type` è il tipo di `"Microsoft.VirtualMachineImages/imageTemplates"`risorsa, che deve essere . Il `apiVersion` cambierà nel tempo come l'API cambia, ma dovrebbe essere `"2019-05-01-preview"` per l'anteprima.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

La posizione è l'area in cui verrà creata l'immagine personalizzata. Per l'anteprima di Image Builder sono supportate le seguenti aree:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile (profilo di vm)
Per impostazione predefinita, Image Builder utilizzerà una macchina virtuale di compilazione "Standard_D1_v2", è possibile eseguire l'override di questo, ad esempio, se si desidera personalizzare un'immagine per una macchina virtuale GPU, è necessaria una dimensione della macchina virtuale GPU. Operazione facoltativa.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Per impostazione predefinita, Image Builder non modifica le dimensioni dell'immagine, utilizzerà le dimensioni dell'immagine di origine. È possibile aumentare le dimensioni del disco del sistema operativo (Win e Linux), questo è facoltativo e un valore pari a 0 significa lasciare le stesse dimensioni dell'immagine di origine. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>VnetConfig (informazioni in vnetConfig)
Se non si specifica alcuna proprietà VNET, Generatore immagini creerà la propria VNET, IP pubblico e gruppo di sicurezza di rete. L'IP pubblico viene utilizzato per il servizio per comunicare con la macchina virtuale di compilazione, tuttavia se non si desidera un IP pubblico o si desidera che Image Builder abbia accesso alle risorse VNET esistenti, ad esempio i server di configurazione (DSC, Chef, Puppet, Ansible), le condivisioni file e così via. , quindi è possibile specificare un vNET. Per ulteriori informazioni, consultare la [documentazione](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)di rete , questa opzione è facoltativa.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tag

Si tratta di coppie chiave/valore che è possibile specificare per l'immagine generata.

## <a name="depends-on-optional"></a>Dipende (opzionale)

Questa sezione facoltativa può essere utilizzata per garantire che le dipendenze vengano completate prima di procedere. 

```json
    "dependsOn": [],
```

Per ulteriori informazioni, vedere [Definire le dipendenze delle risorse.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)

## <a name="identity"></a>Identità
Per impostazione predefinita, Image Builder supporta l'uso di script o la copia di file da più posizioni, ad esempio GitHub e Archiviazione di Azure.By default, Image Builder supports using scripts, or copying files from multiple locations, such as GitHub and Azure storage. Per usarli, devono essere accessibili al pubblico.

È anche possibile usare un'identità gestita assegnata dall'utente di Azure, definita dall'utente, per consentire a Generatore di immagini di accedere ad Archiviazione di Azure, purché all'identità sia stato concesso un minimo di "Lettore di dati BLOB di archiviazione" nell'account di archiviazione di Azure.You can also use an Azure User-Assigned Managed Identity, defined by you, to allow Image Builder access Azure Storage, long as the identity has been granted a minimum of 'Storage Blob Data Reader' on the Azure storage account. Ciò significa che non è necessario rendere i BLOB di archiviazione accessibili esternamente o configurare i token di firma di accesso condiviso.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Per un esempio completo, vedere [Usare un'identità gestita assegnata dall'utente di Azure per accedere ai file in Archiviazione di Azure.For a](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)complete example, see Use an Azure User-Assigned Managed Identity to access files in Azure Storage.

Supporto di Image Builder per un'identità assegnata dall'utente: - Supporta una sola identità - Non supporta i nomi di dominio personalizzati

Per altre informazioni, vedere [Che cos'è le identità gestite per le risorse](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)di Azure? .
Per altre informazioni sulla distribuzione di questa funzionalità, vedere Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure usando l'interfaccia della riga di comando di Azure.For more information on deploying this feature, see [Configure managed identities for Azure resources on an Azure VM using Azure CLI.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)

## <a name="properties-source"></a>Proprietà: origine

La `source` sezione contiene informazioni sull'immagine di origine che verrà utilizzata da Image Builder.

L'API richiede un 'SourceType' che definisce l'origine per la compilazione dell'immagine, attualmente sono disponibili tre tipi:The API requires a 'SourceType' that defines the source for the image build, currently there are three types:
- PlatformImage - indicato che l'immagine di origine è un'immagine del Marketplace.PlatformImage - indicated the source image is a Marketplace image.
- ManagedImage: usare this quando si inizia da un'immagine gestita normale.
- SharedImageVersion: viene utilizzato quando si utilizza una versione dell'immagine in una raccolta immagini condivise come origine.

### <a name="iso-source"></a>Sorgente ISO
Stiamo deprecando questa funzionalità dal generatore di immagini, in quanto ora ci sono [immagini RHEL Bring Your Own Subscription](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), si prega di rivedere le timeline qui sotto:
    * 31 marzo 2020 - I modelli di immagine con sorgenti ISO RHEL ora saranno più accettati dal fornitore di risorse.
    * 30 aprile 2020 - I modelli di immagine che contengono sorgenti ISO RHEL non verranno più elaborati.

### <a name="platformimage-source"></a>Origine PlatformImage 
Azure Image Builder supporta Windows Server e client e le immagini di Azure Marketplace Linux, vedere [qui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) per l'elenco completo. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Le proprietà qui sono le stesse che vengono usate per creare le macchine virtuali, usando l'interfaccia della riga di comando, eseguire le opzioni seguenti per ottenere le proprietà:The properties here are the same that are used to create VM's, using A A CLI, run the below to get the properties: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

È possibile utilizzare 'latest' nella versione, la versione viene valutata quando viene eseguita la compilazione dell'immagine, non quando il modello viene inviato. Se si utilizza questa funzionalità con la destinazione Raccolta immagini condivise, è possibile evitare di inviare nuovamente il modello ed eseguire nuovamente la compilazione dell'immagine a intervalli, in modo che le immagini vengano ricreate dalle immagini più recenti.

### <a name="managedimage-source"></a>Origine ManagedImage

Imposta l'immagine di origine come immagine gestita esistente di un disco rigido virtuale o di una macchina virtuale generalizzata. L'immagine gestita di origine deve essere di un sistema operativo supportato e trovarsi nella stessa area del modello di Generatore di immagini di Azure.The source managed image must be of a supported OS, and be in the same region as your Azure Image Builder template. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Deve `imageId` essere resourceId dell'immagine gestita. Consente `az image list` di elencare le immagini disponibili.


### <a name="sharedimageversion-source"></a>Origine SharedImageVersion
Imposta l'immagine di origine di una versione dell'immagine esistente in una raccolta immagini condivise. La versione dell'immagine deve essere di un sistema operativo supportato e l'immagine deve essere replicata nella stessa area del modello di Generatore di immagini di Azure.The image version must be of a supported OS, and the image must be replicated into the same region as your Azure Image Builder template. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Deve `imageVersionId` essere il ResourceId della versione dell'immagine. Utilizzare [az sig image-version elenco](/cli/azure/sig/image-version#az-sig-image-version-list) per elencare le versioni dell'immagine.

## <a name="properties-buildtimeoutinminutes"></a>Proprietà: buildTimeoutInMinutes

Per impostazione predefinita, il Generatore di immagini verrà eseguito per 240 minuti. Dopo di che, si timeout e arrestare, se la build dell'immagine è completa o meno. Se viene raggiunto il timeout, verrà visualizzato un errore simile al seguente:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se non si specifica un valore buildTimeoutInMinutes o lo si imposta su 0, verrà utilizzato il valore predefinito. È possibile aumentare o diminuire il valore, fino a un massimo di 960 min (16 ore). Per Windows, non è consigliabile impostare questo sotto 60 minuti. Se si rileva che si sta raggiungendo il timeout, esaminare i [registri](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)per verificare se il passaggio di personalizzazione è in attesa di qualcosa di simile all'input dell'utente. 

Se si ritiene che sia necessario più tempo per il completamento delle personalizzazioni, impostarlo su ciò che si ritiene necessario, con un piccolo sovraccarico. Tuttavia, non impostarlo troppo alto perché potrebbe essere necessario attendere il timeout prima di visualizzare un errore. 


## <a name="properties-customize"></a>Proprietà: personalizzare

Image Builder supporta più 'customizer'. Gli addetti alla personalizzazione sono funzioni utilizzate per personalizzare l'immagine, ad esempio l'esecuzione di script o il riavvio dei server. 

Quando `customize`si utilizza : 
- È possibile utilizzare più elementi di personalizzazione, ma devono avere un oggetto . `name`
- Gli addetti alla personalizzazione vengono eseguiti nell'ordine specificato nel modello.
- Se un addetto alla personalizzazione ha esito negativo, l'intero componente di personalizzazione avrà esito negativo e riporterà un errore.
- Si consiglia vivamente di testare accuratamente lo script prima di utilizzarlo in un modello. Il debug dello script nella propria macchina virtuale sarà più semplice.
- Non inserire dati sensibili negli script. 
- I percorsi degli script devono essere accessibili pubblicamente, a meno che non si utilizzi [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
La sezione customize è un array. Azure Image Builder verrà eseguito tramite le personalizzazioni in ordine sequenziale. Qualsiasi errore in qualsiasi addetto alla personalizzazione avrà esito negativo nel processo di compilazione. 
 
 
### <a name="shell-customizer"></a>Personalizzazione della shell

Il psichevita della shell supporta l'esecuzione di script della shell, questi devono essere accessibili pubblicamente per l'IB per accedervi.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Supporto del sistema operativo: Linux 
 
Personalizzare le proprietà:

- **tipo** – Shell 
- **name** - nome per il monitoraggio della personalizzazione 
- **scriptUri** - URI nel percorso del file 
- **inline** - matrice di comandi della shell, separati da virgole.
- **sha256Checksum** - Valore del checksum sha256 del file, viene generato in locale e quindi Image Builder verrà il checksum e la convalida.
    * Per generare sha256Checksum, utilizzando un terminale su Mac/Linux eseguire:`sha256sum <fileName>`


Affinché i comandi vengano eseguiti con privilegi `sudo`di utente con privilegi avanzati, è necessario che siano preceduti da .

> [!NOTE]
> Quando si esegue l'addetto alla personalizzazione della shell con origine ISO RHEL, è necessario assicurarsi che la prima shell di personalizzazione gestisca la registrazione con un server di adesione Red Hat prima di qualsiasi personalizzazione. Una volta completata la personalizzazione, lo script deve annullare la registrazione con il server dei diritti.

### <a name="windows-restart-customizer"></a>Personalizzazione riavvio di Windows 
L'addetto alla personalizzazione del riavvio consente di riavviare una macchina virtuale Windows e attendere che torni online, in modo da installare software che richiede un riavvio.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Supporto del sistema operativo: Windows
 
Personalizzare le proprietà:
- **Tipo**: WindowsRestart
- **restartCommand** - Comando per eseguire il riavvio (facoltativo). Il valore predefinito è `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Comando per verificare se il riavvio è riuscito (facoltativo). 
- **restartTimeout** - Timeout di riavvio specificato come stringa di grandezza e unità. Ad esempio, `5m` (5 `2h` minuti) o (2 ore). Il valore predefinito è: '5m'

### <a name="linux-restart"></a>Riavvio di Linux  
Non è disponibile alcun addetto alla personalizzazione del riavvio di Linux, tuttavia, se si installano driver o componenti che richiedono un riavvio, è possibile installarli e richiamare un riavvio utilizzando l'addetto alla personalizzazione di Shell, alla macchina virtuale di compilazione è presente un timeout SSH di 20 minuti.

### <a name="powershell-customizer"></a>Personalizzazione di PowerShellPowerShell customizer 
L'addetto alla personalizzazione della shell supporta l'esecuzione di script di PowerShell e di comando inline, gli script devono essere accessibili pubblicamente per l'IB per accedervi.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Supporto del sistema operativo: Windows e Linux

Personalizzare le proprietà:

- **type** – PowerShell.
- **scriptUri** - URI nel percorso del file di script di PowerShell. 
- **inline:** comandi inline da eseguire, separati da virgole.
- **validExitCodes:** è facoltativo, i codici validi che possono essere restituiti dal comando script/inline, si eviterà l'errore segnalato del comando script/inline.
- **runElevated:** facoltativo, booleano, supporto per l'esecuzione di comandi e script con autorizzazioni elevate.runElevated – Optional, boolean, support for running commands and scripts with elevated permissions.
- **sha256Checksum** - Valore del checksum sha256 del file, viene generato in locale e quindi Image Builder verrà il checksum e la convalida.
    * Per generare sha256Checksum, utilizzando PowerShell in [Windows Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Personalizzazione dei file

Il personalizzazione dei file consente al generatore di immagini di scaricare un file da un archivio GitHub o Azure.The File customizer lets image builder download a file from a GitHub or Azure storage. Se si dispone di una pipeline di compilazione dell'immagine che si basa su elementi di compilazione, è possibile impostare l'addetto alla personalizzazione dei file per il download dalla condivisione di compilazione e spostare gli elementi nell'immagine.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Supporto del sistema operativo: Linux e Windows 

Proprietà di personalizzazione dei file:

- **sourceUri:** un endpoint di archiviazione accessibile, può essere GitHub o Archiviazione di Azure.sourceUri - an accessible storage endpoint, this can be GitHub or Azure storage. È possibile scaricare un solo file, non un'intera directory. Se è necessario scaricare una directory, utilizzare un file compresso, quindi decomprimerlo utilizzando gli addetti alla personalizzazione di Shell o PowerShell. 
- **destinazione** – questo è il percorso di destinazione completo e il nome del file. Qualsiasi percorso e sottodirectory a cui si fa riferimento deve esistere, utilizzare gli addetti alla personalizzazione di Shell o PowerShell per configurarli in anticipo. È possibile utilizzare le utilità di personalizzazione degli script per creare il percorso. 

Questo è supportato dalle directory di Windows e percorsi Linux, ma ci sono alcune differenze: 
- Sistema operativo Linux: l'unico percorso in cui il generatore di immagini può scrivere è /tmp.
- Windows: nessuna restrizione di percorso, ma il percorso deve esistere.
 
 
Se si verifica un errore durante il tentativo di scaricare il file o di inserirlo in una directory specificata, il passaggio di personalizzazione avrà esito negativo e questo sarà nel file customization.log.

> [!NOTE]
> L'addetto alla personalizzazione dei file è adatto solo per download di file di piccole dimensioni, < 20 MB. Per i download di file di dimensioni maggiori utilizzare uno script o `wget` `curl`un comando `Invoke-WebRequest`inline, il codice di utilizzo per scaricare file, ad esempio, Linux o , Windows, .

I file nella personalizzazione dei file possono essere scaricati da Archiviazione di Azure usando [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Addetto alla personalizzazione di Windows Update
Questo addetto alla [personalizzazione](https://packer.io/docs/provisioners/community-supported.html) si basa sulla community di Windows Update Provisioner per Packer, un progetto open source gestito dalla community packer. Microsoft testa e convalida il provisioning con il servizio Image Builder e supporterà l'analisi dei problemi con esso e lavorerà per risolvere i problemi, tuttavia il progetto open source non è ufficialmente supportato da Microsoft. Per la documentazione dettagliata su e la guida con il provisioning di Windows Update si prega di consultare il repository del progetto.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Supporto del sistema operativo: Windows

Personalizzare le proprietà:
- **Type** – WindowsUpdate.
- **searchCriteria** - Facoltativo, definisce il tipo di aggiornamenti da installare (consigliato, importante e così via), l'impostazione predefinita è BrowseOnly, browseOnly e IsInstalled.0 (scelta consigliata).
- **filters** – Facoltativo, consente di specificare un filtro per includere o escludere gli aggiornamenti.
- **updateLimit** – Facoltativo, definisce il numero di aggiornamenti che è possibile installare, valore predefinito 1000.
 
 

### <a name="generalize"></a>Generalizzazione 
Per impostazione predefinita, Azure Image Builder eseguirà anche il codice di deprovisioning alla fine di ogni fase di personalizzazione dell'immagine, per generalizzare l'immagine. La generalizzazione è un processo in cui l'immagine è configurata in modo che possa essere riutilizzata per creare più macchine virtuali. Per le macchine virtuali Windows, Azure Image Builder usa Sysprep.For Windows VMs, Azure Image Builder uses Sysprep. Per Linux, Azure Image Builder esegue 'waagent -deprovision'. 

I comandi di Image Builder per generalizzare potrebbero non essere adatti per ogni situazione, pertanto Azure Image Builder consentirà di personalizzare questo comando, se necessario. 

Se si esegue la migrazione della personalizzazione esistente e si utilizzano comandi Sysprep/waagent diversi, è possibile utilizzare i comandi generici di Generatore immagini e, se la creazione della macchina virtuale non riesce, utilizzare i propri comandi Sysprep o waagent.

Se Azure Image Builder crea correttamente un'immagine personalizzata di Windows e si crea una macchina virtuale da essa, si scopre che la creazione della macchina virtuale non riesce o non viene completata correttamente, sarà necessario esaminare la documentazione di Windows Server Sysprep o generare una richiesta di supporto con il Team del Servizio Supporto Tecnico Clienti di Windows Server Sysprep, che può risolvere i problemi e fornire consulenza sull'utilizzo corretto di Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep predefinito
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando di deprovisioning Linux predefinito

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Sostituzione dei comandi
Per eseguire l'override dei comandi, utilizzare i provisioning di script di PowerShell o Shell per creare i file di comando con il nome file esatto e inserirli nelle directory corrette:

* Finestre: c:
* Linux: /tmp/DeprovisioningScript.sh

Image Builder leggerà questi comandi, questi sono scritti nei registri AIB, 'customization.log'. Vedere [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) su come raccogliere i log.
 
## <a name="properties-distribute"></a>Proprietà: distribuire

Azure Image Builder supporta tre destinazioni di distribuzione:Azure Image Builder supports three distribution targets: 

- **managedImage** - immagine gestita.
- **sharedImage** - Galleria di immagini condivise.
- **VHD** - VHD in un account di archiviazione.

È possibile distribuire un'immagine a entrambi i tipi di destinazione nella stessa configurazione, vedere [esempi](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Poiché è possibile avere più di una destinazione su cui eseguire la `runOutputName`distribuzione, Image Builder mantiene uno stato per ogni destinazione di distribuzione a cui è possibile accedere eseguendo una query su .  Si `runOutputName` tratta di un oggetto che è possibile interrogare la distribuzione post-distribuzione per informazioni su tale distribuzione. Ad esempio, è possibile eseguire una query sul percorso del disco rigido virtuale o sulle aree in cui è stata replicata la versione dell'immagine oppure è stata creata la versione dell'immagine SIG. Si tratta di una proprietà di ogni destinazione di distribuzione. L'oggetto `runOutputName` deve essere univoco per ogni destinazione di distribuzione. Di seguito è riportato un esempio, si tratta di eseguire una query su una distribuzione della raccolta immagini condivisa:Here is an example, this is querying a Shared Image Gallery distribution:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Output:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuire: managedImageDistribute: managedImage

L'output dell'immagine sarà una risorsa immagine gestita.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuire le proprietà:
- **type** – managedImage 
- **imageId** – ID risorsa dell'immagine di destinazione, formato previsto:\<\</subscriptions/ subscriptionId>/resourceGroups/ destinationResourceGroupName>/providers/Microsoft.Compute/images/\<imageName>
- **location** - posizione dell'immagine gestita.  
- **runOutputName:** nome univoco per l'identificazione della distribuzione.  
- **artefactTags** - Tag di coppia di valori chiave specificati dall'utente facoltativi.
 
 
> [!NOTE]
> Il gruppo di risorse di destinazione deve esistere.
> Se si desidera distribuire l'immagine in un'area diversa, aumenterà il tempo di distribuzione. 

### <a name="distribute-sharedimage"></a>Distribuire: sharedImageDistribute: sharedImage 
Azure Shared Image Gallery è un nuovo servizio di gestione delle immagini che consente la gestione della replica dell'area delle immagini, il controllo delle versioni e la condivisione di immagini personalizzate. Azure Image Builder supporta la distribuzione con questo servizio, pertanto è possibile distribuire immagini nelle aree supportate dalle raccolte di immagini condivise. 
 
Una Galleria di immagini condivise è composta da: 
 
- Galleria - Contenitore per più immagini condivise. Una raccolta viene distribuita in un'area.
- Definizioni di immagini - un raggruppamento concettuale per le immagini. 
- Versioni dell'immagine: si tratta di un tipo di immagine usato per la distribuzione di una macchina virtuale o di un set di scalabilità. Le versioni delle immagini possono essere replicate in altre aree in cui è necessario distribuire le macchine virtuali.
 
Prima di poter eseguire la distribuzione alla Raccolta immagini, è necessario creare una raccolta e una definizione di immagine, vedere [Immagini condivise](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuire le proprietà per le raccolte di immagini condivise:

- **type** - sharedImage  
- **galleryImageId:** ID della raccolta di immagini condivise.galleryImageId – ID of the shared image gallery. Il formato è:\</subscriptions/ subscriptionId\<>/resourceGroups/ resourceGroupName>/providers/Microsoft.Compute/galleries/\<\<sharedImageGalleryName>/images/imageGalleryName>.
- **runOutputName:** nome univoco per l'identificazione della distribuzione.  
- **artefactTags** - Tag di coppia di valori chiave specificati dall'utente facoltativi.
- **replicationRegions** - Matrice di aree per la replica. Una delle aree deve essere l'area in cui viene distribuita la Raccolta.
 
> [!NOTE]
> È possibile usare Generatore immagini di Azure in un'area diversa dalla raccolta, ma il servizio Generatore di immagini di Azure dovrà trasferire l'immagine tra i data center e questo richiederà più tempo. Image Builder verrà automaticamente versione dell'immagine, in base a un numero intero monotonico, non è possibile specificarlo attualmente. 

### <a name="distribute-vhd"></a>Distribuire: VHDDistribute: VHD  
È possibile eseguire l'output in un disco rigido virtuale. È quindi possibile copiare il disco rigido virtuale e usarlo per pubblicare in Azure MarketPlace o usare Azure Stack.You can then copy the VHD, and use it to publish to Azure MarketPlace, or use with Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Supporto del sistema operativo: Windows e Linux

Distribuire i parametri VHD:Distribute VHD parameters:

- **type** - VHD.
- **runOutputName:** nome univoco per l'identificazione della distribuzione.  
- **tags** - Tag di coppia di valori chiave specificati dall'utente opzionali.
 
Azure Image Builder non consente all'utente di specificare un percorso `runOutputs` dell'account di archiviazione, ma è possibile eseguire una query sullo stato di per ottenere il percorso.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Dopo aver creato il disco rigido virtuale, copiarlo in una posizione diversa, il prima possibile. Il disco rigido virtuale viene archiviato in un account di archiviazione nel gruppo di risorse temporaneo creato quando il modello di immagine viene inviato al servizio Generatore immagini di Azure.The VHD is stored in a storage account in the temporary resource group created when the image template is submitted to the Azure Image Builder service. Se si elimina il modello di immagine, si perderà il disco rigido virtuale. 
 
## <a name="next-steps"></a>Passaggi successivi

Esistono file con estensione json di esempio per scenari diversi in [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
