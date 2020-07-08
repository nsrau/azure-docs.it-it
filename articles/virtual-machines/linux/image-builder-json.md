---
title: Creare un modello di Image Builder di Azure (anteprima)
description: Informazioni su come creare un modello da usare con Image Builder di Azure.
author: danielsollondon
ms.author: danis
ms.date: 06/23/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 44cafd4ce7e36c34082ff3c5498c5bbc35282221
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263314"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Anteprima: Creare un modello di Image Builder di Azure 

Image Builder di Azure usa un file con estensione JSON per passare le informazioni nel servizio Image Builder. In questo articolo verranno esaminate le sezioni del file JSON così da poterne creare uno personalizzato. Per vedere gli esempi di file con estensione JSON completi, fare riferimento al [GitHub per Image Builder di Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Questo è il formato del modello di base:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
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



## <a name="type-and-api-version"></a>type e apiVersion

`type` è il tipo di risorsa che deve essere `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` cambierà nel tempo quando cambia l'API, ma per l'anteprima deve essere `"2019-05-01-preview"`.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

La posizione è l'area in cui verrà creata l'immagine personalizzata. Per l'anteprima di Image Builder sono supportate le aree seguenti:

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
## <a name="vmprofile"></a>vmProfile
Per impostazione predefinita, Image Builder userà una macchina virtuale di compilazione "Standard_D1_v2". Questo elemento può essere sostituito, ad esempio, se si vuole personalizzare un'immagine per una macchina virtuale GPU, è necessaria una dimensione della macchina virtuale GPU. Operazione facoltativa.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Per impostazione predefinita, Image Builder non modifica le dimensioni dell'immagine, usa le dimensioni dell'immagine di origine. È possibile aumentare **solo** le dimensioni del disco del sistema operativo (Win e Linux), questo è facoltativo e il valore 0 indica che la dimensione dell'immagine di origine è uguale a quella di. Non è possibile ridurre le dimensioni del disco del sistema operativo a dimensioni inferiori rispetto a quelle dell'immagine di origine.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se non si specifica alcuna proprietà della rete virtuale, Image Builder creerà la propria rete virtuale, l'indirizzo IP pubblico e l'NSG. L'indirizzo IP pubblico viene usato dal servizio per comunicare con la macchina virtuale di compilazione. Tuttavia, se non si vuole un IP pubblico o si desidera che Image Builder abbia accesso alle risorse della rete virtuale esistenti, quali i server di configurazione (DSC, Chef, Puppet, Ansible), le condivisioni file e così via, è possibile specificare una rete virtuale. Per altre informazioni, consultare la [documentazione sulla rete](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), che è facoltativa.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tag

Si tratta di coppie chiave-valore che l'utente può specificare per l'immagine generata.

## <a name="depends-on-optional"></a>dependsOn (facoltativo)

Questa sezione facoltativa può essere usata per assicurarsi che le dipendenze vengano completate prima di procedere. 

```json
    "dependsOn": [],
```

Per altre informazioni, vedere [Definire le dipendenze della risorsa](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identità
Per impostazione predefinita, Image Builder supporta l'uso di script o la copia di file da più posizioni, quali GitHub e Archiviazione di Azure. Per usarli, devono essere accessibili pubblicamente.

È anche possibile usare un'identità gestita assegnata dall'utente di Azure, definita dall'utente, per consentire a Image Builder di accedere ad Archiviazione di Azure, purché all'identità sia stato concesso un "Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione" minimo nell'account di archiviazione di Azure. Ciò significa che non è necessario rendere i BLOB di archiviazione accessibili esternamente o configurare i token SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Per un esempio completo, vedere [Usare un'identità gestita assegnata dall'utente di Azure per accedere ai file in Archiviazione di Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Supporto di Image Builder per un'identità assegnata dall'utente: • supporta una sola identità • non supporta i nomi di dominio personalizzati

Per altre informazioni, vedere [Cosa sono le identità gestite per le risorse di Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Per altre informazioni sulla distribuzione di questa funzionalità, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Proprietà: source

La sezione `source` contiene informazioni sull'immagine di origine che verrà usata da Image Builder.

L'API richiede un "tipo di origine" che definisce l'origine per la compilazione dell'immagine, attualmente ne esistono tre tipi:
- PlatformImage: indica che l'immagine di origine è un'immagine del Marketplace.
- ManagedImage: usarla quando si parte da una normale immagine gestita.
- SharedImageVersion: viene usata quando si usa una versione dell'immagine in Raccolta immagini condivise come origine.

### <a name="iso-source"></a>Origine ISO
Questa funzionalità è deprecata da Image Builder, perché ora esistono le [immagini Bring Your Own Subscription di RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos); esaminare le sequenze temporali seguenti:
    * 31 marzo 2020: i modelli di immagine con origini RHEL ISO non saranno più accettati dal provider di risorse.
    * 30 aprile 2020: i modelli di immagine che contengono origini RHEL ISO non saranno più elaborati.

### <a name="platformimage-source"></a>Origine PlatformImage 
Azure Image Builder supporta le immagini di Windows Server, dei client e di Azure Marketplace in Linux. Vedere [qui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) per l'elenco completo. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Le proprietà sono le stesse usate per creare le macchine virtuali con l'interfaccia della riga di comando AZ; eseguire il comando seguente per ottenere le proprietà: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

È possibile usare "latest" nella versione, la versione viene valutata quando avviene la compilazione dell'immagine, non quando viene inviato il modello. Se si usa questa funzionalità con la destinazione Raccolta immagini condivise, è possibile evitare di inviare di nuovo il modello e rieseguire la compilazione dell'immagine a intervalli, in modo che le immagini vengano ricreate dalle immagini più recenti.

### <a name="managedimage-source"></a>Origine ManagedImage

Imposta l'immagine di origine come un'immagine gestita esistente di un disco rigido virtuale generalizzato o di una macchina virtuale. L'immagine gestita di origine deve appartenere a un sistema operativo supportato e trovarsi nella stessa area del modello di Image Builder di Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` deve essere l'ID della risorsa dell'immagine gestita. Usare `az image list` per elencare le immagini disponibili.


### <a name="sharedimageversion-source"></a>Origine SharedImageVersion
Imposta l'immagine di origine in una versione dell'immagine esistente in Raccolta immagini condivise. La versione dell'immagine deve appartenere a un sistema operativo supportato e l'immagine deve essere replicata nella stessa area del modello di Image Builder di Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` deve essere l'ID della risorsa della versione dell'immagine. Usare [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) per elencare le versioni delle immagini.

## <a name="properties-buildtimeoutinminutes"></a>Proprietà: buildTimeoutInMinutes

Per impostazione predefinita, Image Builder viene eseguito per 240 minuti. Successivamente, raggiunge il timeout e si arresta anche se la compilazione dell'immagine non è stata completata. Se si raggiunge il timeout, viene visualizzato un errore simile al seguente:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se non si specifica un valore per buildTimeoutInMinutes o lo si imposta su 0, viene usato il valore predefinito. È possibile aumentare o ridurre il valore, fino a un massimo di 960 minuti, ovvero 16 ore. Per Windows, non è consigliabile impostare questo valore al di sotto dei 60 minuti. Se si rileva che il processo sta per raggiungere il timeout, esaminare i [log](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs) per accertarsi che il passaggio di personalizzazione non sia in attesa, ad esempio, di un input dell'utente. 

Se è necessario più tempo per completare la personalizzazione, impostare il valore in base alle necessità, aumentandolo leggermente. Tuttavia, non è consigliabile impostare un valore troppo elevato perché potrebbe essere necessario attendere il raggiungimento del timeout prima di visualizzare un errore. 


## <a name="properties-customize"></a>Proprietà: customize

Image Builder supporta più "funzioni di personalizzazione". Le funzioni di personalizzazione sono funzioni usate per personalizzare l'immagine, ad esempio gli script di esecuzione o i server di riavvio. 

Quando si usa `customize`: 
- È possibile usare più funzioni di personalizzazione, ma devono avere un `name` univoco.
- Le funzioni di personalizzazione vengono eseguite nell'ordine specificato nel modello.
- Se una funzione di personalizzazione ha esito negativo, l'intero componente di personalizzazione avrà esito negativo e segnalerà un errore.
- Si consiglia vivamente di testare accuratamente lo script prima di usarlo in un modello. Il debug dello script nella propria macchina virtuale sarà più semplice.
- Non inserire dati sensibili negli script. 
- I percorsi degli script devono essere pubblicamente accessibili, a meno che non si usi [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
La sezione customize è una matrice. Image Builder di Azure eseguirà le funzioni di personalizzazione in ordine sequenziale. Eventuali errori nelle funzioni di personalizzazione impediranno il processo di compilazione. 
 
 
### <a name="shell-customizer"></a>Funzione di personalizzazione shell

La funzione di personalizzazione shell supporta l'esecuzione di script della shell, che devono essere pubblicamente accessibili affinché Image Builder possa accedervi.

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

Supporto sistema operativo: Linux 
 
Proprietà customize:

- **type** - Shell 
- **name** - nome per tenere traccia della personalizzazione 
- **scriptUri** - URI del percorso del file 
- **inline** - matrice dei comandi della shell, separati da virgole.
- **sha256Checksum** - valore di checksum sha256 del file, viene generato in locale, quindi Image Builder lo genera e lo verifica.
    * Per generare sha256Checksum, usando un terminale in Mac o Linux eseguire: `sha256sum <fileName>`


Per eseguire i comandi con privilegi avanzati, è necessario che siano preceduti dal prefisso `sudo`.

> [!NOTE]
> Quando si esegue la funzione di personalizzazione shell con l'origine RHEL ISO, è necessario assicurarsi che la prima shell di personalizzazione gestisca la registrazione con un server di entitlement Red Hat prima di eseguire la personalizzazione. Al termine della personalizzazione, lo script deve annullare la registrazione con il server di entitlement.

### <a name="windows-restart-customizer"></a>Funzione di personalizzazione di riavvio di Windows 
La funzione di personalizzazione di riavvio consente di riavviare una macchina virtuale Windows e attendere che torni online, consentendo all'utente di installare il software che richiede il riavvio.  

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

Supporto sistema operativo: Windows
 
Proprietà customize:
- **Tipo**: WindowsRestart
- **restartCommand** - comando per eseguire il riavvio (facoltativo). Il valore predefinito è `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** - comando per verificare che il riavvio sia stato completato (facoltativo). 
- **restartTimeout** - timeout di riavvio specificato come stringa di grandezza e unità. Ad esempio, `5m` (5 minuti) o `2h` (2 ore). Il valore predefinito: "5m"

### <a name="linux-restart"></a>Riavvio di Linux  
Non esiste una funzione di personalizzazione di riavvio di Linux, tuttavia, se si installano i driver o i componenti che richiedono un riavvio, è possibile installarli e richiamare un riavvio usando la funzione di personalizzazione shell; il timeout SSH per la macchina virtuale di compilazione è di 20 minuti.

### <a name="powershell-customizer"></a>Funzione di personalizzazione PowerShell 
La funzione di personalizzazione shell supporta l'esecuzione di script PowerShell e di comandi inline, gli script devono essere pubblicamente accessibili affinché Image Builder possa accedervi.

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

Supporto sistema operativo: Windows e Linux

Proprietà customize:

- **type** - PowerShell.
- **scriptUri** - URI del percorso del file di script di PowerShell. 
- **inline** - comandi inline da eseguire, separati da virgole.
- **validExitCodes** - codici validi e facoltativi che possono essere restituiti dallo script o dal comando inline. In questo modo si eviterà l'errore segnalato dello script o del comando inline.
- **runElevated** - valore facoltativo, booleano, supporto per l'esecuzione di comandi e script con autorizzazioni elevate.
- **sha256Checksum** - valore di checksum sha256 del file, viene generato in locale, quindi Image Builder lo genera e lo verifica.
    * Per generare sha256Checksum, usare il comando [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) di PowerShell in Windows


### <a name="file-customizer"></a>Funzione di personalizzazione File

La funzione di personalizzazione File consente a Image Builder di scaricare un file da un archivio Azure o GitHub. Se si dispone di una pipeline di compilazione dell'immagine che si basa sugli artefatti della compilazione, è possibile impostare la funzione di personalizzazione File per il download dalla condivisione di compilazione e spostare gli artefatti nell'immagine.  

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

Supporto sistema operativo: Linux e Windows 

Proprietà della funzione di personalizzazione File:

- **sourceUri** - un endpoint di archiviazione accessibile; può essere un'archiviazione Azure o GitHub. È possibile scaricare un solo file, non tutta la directory. Se è necessario scaricare una directory, usare un file compresso, quindi decomprimerlo usando la funzione di personalizzazione shell o PowerShell. 
- **destination** - è il percorso di destinazione completo e il nome del file. Qualsiasi percorso e sottodirectory a cui si fa riferimento deve essere esistente, usare le funzioni di personalizzazione shell o PowerShell per configurarli in anticipo. Per creare il percorso, è possibile usare le funzioni di personalizzazione dello script. 

Questa operazione è supportata dalle directory Windows e dai percorsi Linux, ma ci sono alcune differenze: 
- Sistema operativo Linux: l'unico percorso che Image Builder può scrivere è /tmp.
- Windows: nessuna restrizione del percorso, ma deve essere esistente.
 
 
Se si verifica un errore durante il tentativo di download del file o di inserimento in una directory specifica, il passaggio di personalizzazione genererà un errore che sarà presente nel file customization.log.

> [!NOTE]
> La funzione di personalizzazione file è adatta solo per il download di file di piccole dimensioni, inferiori a 20 MB. Per i download di file più grandi usare uno script o un comando inline, quindi il codice per scaricare i file, ad esempio `wget` o `curl` per Linux, `Invoke-WebRequest` per Windows.

I file nella funzione di personalizzazione file possono essere scaricati da Archiviazione di Azure usando [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Funzione di personalizzazione Windows Update
Questa funzione di personalizzazione si basa sullo [strumento di provisioning Windows Update per la community](https://packer.io/docs/provisioners/community-supported.html) per Packer, un progetto open source gestito dalla community di Packer. Microsoft testa e convalida lo strumento di provisioning con il servizio Image Builder, supporta con esso l'indagine dei problemi e si impegna per risolverli. Tuttavia il progetto open source non è ufficialmente supportato da Microsoft. Per la documentazione dettagliata e la guida allo strumento di provisioning di Windows Update, vedere il repository del progetto.
 
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
Supporto sistema operativo: Windows

Proprietà customize:
- **type** - WindowsUpdate.
- **searchCriteria** - facoltativo, definisce il tipo di aggiornamenti installati (consigliati, importanti e così via), BrowseOnly=0 e IsInstalled=0 (scelta consigliata) è il valore predefinito.
- **filters** - facoltativo, consente di specificare un filtro per includere o escludere gli aggiornamenti.
- **updateLimit** - facoltativo, definisce il numero di aggiornamenti che è possibile installare, valore predefinito 1000.
 
 

### <a name="generalize"></a>Generalizzazione 
Per impostazione predefinita, Image Builder di Azure eseguirà anche il codice di "deprovisioning" alla fine di ogni fase di personalizzazione dell'immagine, in modo da "generalizzare" l'immagine. La generalizzazione è un processo in cui l'immagine è configurata in modo da poter essere riutilizzata per creare più macchine virtuali. Per le macchine virtuali Windows, Image Builder di Azure usa Sysprep. Per Linux, Image Builder di Azure esegue "waagent-deprovision". 

I comandi di Image Builder per la generalizzazione potrebbero non essere adatti per tutte le situazioni, quindi Image Builder di Azure consentirà di personalizzare il comando, se necessario. 

Se si esegue la migrazione della personalizzazione esistente e si usano comandi Sysprep o waagent diversi, è possibile usare i comandi generici di Image Builder e, se la creazione della macchina virtuale non riesce, i comandi Sysprep o waagent personalizzati.

Se Image Builder di Azure crea correttamente un'immagine personalizzata di Windows e l'utente crea una macchina virtuale da questa, ma poi scopre che la creazione della macchina virtuale ha esito negativo o non viene completata correttamente, sarà necessario esaminare la documentazione relativa a Sysprep di Windows Server oppure inoltrare una richiesta di supporto al team di supporto del servizio clienti di Sysprep per Windows Server, che può risolvere i problemi e dare consigli sull'uso corretto di Sysprep.


#### <a name="default-sysprep-command"></a>Comando di Sysprep predefinito
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>Comando di deprovisioning predefinito di Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Override dei comandi
Per eseguire l'override dei comandi, usare lo strumento di provisioning dello script PowerShell o Shell per creare i file di comando con il nome esatto del file e inserirli nelle directory corrette:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder leggerà questi comandi, che verranno scritti nei log di Image Builder di Azure, in "customization.log". Consultare la [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) su come raccogliere i log.
 
## <a name="properties-distribute"></a>Proprietà: distribute

Azure Image Builder supporta tre destinazioni di distribuzione: 

- **managedImage** - immagine gestita.
- **sharedImage** - Raccolta immagini condivise.
- **VHD** - disco rigido virtuale in un account di archiviazione.

È possibile distribuire un'immagine in entrambi i tipi di destinazione nella stessa configurazione. Fare riferimento agli [esempi](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Poiché è possibile distribuire in più di una destinazione, Image Builder mantiene uno stato per ogni destinazione di distribuzione a cui è possibile accedere eseguendo una query per `runOutputName`.  `runOutputName` è un oggetto per cui è possibile eseguire una query dopo la distribuzione per ottenere informazioni su tale distribuzione. Ad esempio, è possibile eseguire una query per il percorso del disco rigido virtuale o delle aree in cui è stata replicata la versione dell'immagine oppure in cui è stata creata la versione dell'immagine SIG. Si tratta di una proprietà di tutte le destinazioni di distribuzione. `runOutputName` deve essere univoco per ogni destinazione di distribuzione. Di seguito è riportato un esempio che esegue una query per una distribuzione di Raccolta immagini condivise:

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

### <a name="distribute-managedimage"></a>Distribute: managedImage

L'output dell'immagine sarà una risorsa dell'immagine gestita.

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
 
Proprietà distribute:
- **type** - managedImage 
- **imageId** : ID risorsa dell'immagine di destinazione, formato previsto:/subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** - percorso dell'immagine gestita.  
- **runOutputName** - nome univoco per identificare la distribuzione.  
- **artifactTags** - facoltativo, tag della coppia chiave-valore specificata dall'utente.
 
 
> [!NOTE]
> Il gruppo di risorse di destinazione deve essere esistente.
> Se si vuole che l'immagine venga distribuita in un'area diversa, il tempo di distribuzione aumenterà. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Raccolta immagini condivise di Azure è un nuovo servizio di gestione delle immagini che consente di gestire la replica dell'area dell'immagine, il controllo delle versioni e la condivisione delle immagini personalizzate. Image Builder di Azure supporta la distribuzione con questo servizio, in modo che sia possibile distribuire le immagini alle aree supportate da Raccolta immagini condivise. 
 
Raccolta immagini condivise è costituita da: 
 
- Raccolta: contenitore per più immagini condivise. Una raccolta viene distribuita in un'area.
- Definizioni di immagini: un raggruppamento concettuale per le immagini. 
- Versioni delle immagini: tipo di immagine usato per la distribuzione di una macchina virtuale o di un set di scalabilità. Le versioni delle immagini possono essere replicate in altre aree in cui è necessario distribuire le macchine virtuali.
 
Prima di poter distribuire in Raccolta immagini, è necessario creare una raccolta e una definizione dell'immagine, vedere [Immagini condivise](shared-images.md). 

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

Proprietà distribute per le raccolte di immagini condivise:

- **type** - sharedImage  
- **galleryImageId** - ID di Raccolta immagini condivise. Il formato è:/subscriptions/ \<subscriptionId> /ResourceGroups/ \<resourceGroupName> /providers/Microsoft.Compute/Galleries/ \<sharedImageGalleryName> /images/ \<imageGalleryName> .
- **runOutputName** - nome univoco per identificare la distribuzione.  
- **artifactTags** - facoltativo, tag della coppia chiave-valore specificata dall'utente.
- **replicationRegions** - matrice di aree per la replica. Una delle aree deve essere l'area in cui è distribuita la raccolta.
 
> [!NOTE]
> È possibile usare Image Builder di Azure in un'area diversa per la raccolta, ma il servizio Image Builder di Azure dovrà trasferire l'immagine tra i data center e questa operazione richiederà più tempo. Image Builder esegue automaticamente la versione dell'immagine, in base a un numero intero monotonico, non è possibile specificarlo al momento. 

### <a name="distribute-vhd"></a>Distribute: VHD  
È possibile eseguire l'output in un disco rigido virtuale. È quindi possibile copiare il disco rigido virtuale e usarlo per pubblicare in Azure Marketplace oppure usarlo con Azure Stack.  

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
 
Supporto sistema operativo: Windows e Linux

Parametri del disco rigido virtuale per la proprietà distribute:

- **type** - VHD.
- **runOutputName** - nome univoco per identificare la distribuzione.  
- **tags** - facoltativo, tag della coppia chiave-valore specificata dall'utente.
 
Image Builder di Azure non consente all'utente di specificare un percorso dell'account di archiviazione, ma è possibile eseguire una query per lo stato di `runOutputs` per ottenere la posizione.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Dopo aver creato il disco rigido virtuale, copiarlo in un percorso diverso appena possibile. Il disco rigido virtuale viene memorizzato in un account di archiviazione nel gruppo di risorse temporaneo creato quando il modello di immagine viene inviato al servizio Image Builder di Azure. Se si elimina il modello di immagine, il disco rigido virtuale verrà perso. 
 
## <a name="next-steps"></a>Passaggi successivi

Nel [GitHub di Image Builder di Azure](https://github.com/danielsollondon/azvmimagebuilder) sono disponibili file JSON di esempio per diversi scenari.
 
