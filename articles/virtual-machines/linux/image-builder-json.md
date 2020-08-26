---
title: Creare un modello di Image Builder di Azure (anteprima)
description: Informazioni su come creare un modello da usare con Image Builder di Azure.
author: danielsollondon
ms.author: danis
ms.date: 08/13/2020
ms.topic: conceptual
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 6ed95f87d2b2a5f811531a5ff258ebe97a9b892a
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869202"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Anteprima: Creare un modello di Image Builder di Azure 

Image Builder di Azure usa un file con estensione JSON per passare le informazioni nel servizio Image Builder. In questo articolo verranno esaminate le sezioni del file JSON così da poterne creare uno personalizzato. Per vedere gli esempi di file con estensione JSON completi, fare riferimento al [GitHub per Image Builder di Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Questo è il formato del modello di base:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
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
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>type e apiVersion

`type` è il tipo di risorsa che deve essere `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` cambierà nel tempo quando cambia l'API, ma per l'anteprima deve essere `"2020-02-14"`.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
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
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>Tag

Si tratta di coppie chiave-valore che l'utente può specificare per l'immagine generata.

## <a name="depends-on-optional"></a>dependsOn (facoltativo)

Questa sezione facoltativa può essere usata per assicurarsi che le dipendenze vengano completate prima di procedere. 

```json
    "dependsOn": [],
```

Per altre informazioni, vedere [Definire le dipendenze della risorsa](../../azure-resource-manager/templates/define-resource-dependency.md#dependson).

## <a name="identity"></a>Identità

Obbligatorio: per il generatore di immagini è necessario disporre delle autorizzazioni per leggere/scrivere immagini, leggere negli script da archiviazione di Azure è necessario creare un'identità assegnata dall'utente di Azure, che dispone delle autorizzazioni per le singole risorse. Per informazioni dettagliate sul funzionamento delle autorizzazioni di Image Builder e sui passaggi pertinenti, consultare la [documentazione](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements).


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


Supporto di Image Builder per un'identità assegnata dall'utente:
* Supporta solo una singola identità
* Non supporta i nomi di dominio personalizzati

Per altre informazioni, vedere [Cosa sono le identità gestite per le risorse di Azure?](../../active-directory/managed-identities-azure-resources/overview.md).
Per altre informazioni sulla distribuzione di questa funzionalità, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity).

## <a name="properties-source"></a>Proprietà: source

Image Builder supporta attualmente solo le immagini e le macchine virtuali HyperV di generazione 1 `source` . la sezione contiene informazioni sull'immagine di origine che verrà usata da Image Builder.

L'API richiede un "tipo di origine" che definisce l'origine per la compilazione dell'immagine, attualmente ne esistono tre tipi:
- PlatformImage: indica che l'immagine di origine è un'immagine del Marketplace.
- ManagedImage: usarla quando si parte da una normale immagine gestita.
- SharedImageVersion: viene usata quando si usa una versione dell'immagine in Raccolta immagini condivise come origine.


> [!NOTE]
> Quando si utilizzano immagini personalizzate di Windows esistenti, è possibile eseguire il comando Sysprep fino a 8 volte in una singola immagine di Windows. per ulteriori informazioni, vedere la documentazione di [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) .

### <a name="platformimage-source"></a>Origine PlatformImage 
Azure Image Builder supporta le immagini di Windows Server, dei client e di Azure Marketplace in Linux. Vedere [qui](../windows/image-builder-overview.md#os-support) per l'elenco completo. 

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

#### <a name="support-for-market-place-plan-information"></a>Supporto per le informazioni sul piano Market Place
È anche possibile specificare le informazioni sul piano, ad esempio:
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>Origine ManagedImage

Imposta l'immagine di origine come un'immagine gestita esistente di un disco rigido virtuale generalizzato o di una macchina virtuale.

> [!NOTE]
> L'immagine gestita di origine deve essere di un sistema operativo supportato e l'immagine deve avere la stessa area del modello di generatore di immagini di Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` deve essere l'ID della risorsa dell'immagine gestita. Usare `az image list` per elencare le immagini disponibili.


### <a name="sharedimageversion-source"></a>Origine SharedImageVersion
Imposta l'immagine di origine in una versione dell'immagine esistente in Raccolta immagini condivise.

> [!NOTE]
> L'immagine gestita di origine deve essere di un sistema operativo supportato e l'immagine deve essere identica a quella del modello di generatore di immagini di Azure. in caso contrario, replicare la versione dell'immagine nell'area del modello del generatore di immagini.


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

> [!NOTE]
> I comandi inline possono essere visualizzati nella definizione del modello di immagine e da supporto tecnico Microsoft quando si assiste a un caso di supporto. Se si dispone di informazioni riservate, è necessario spostarle in script in archiviazione di Azure, dove l'accesso richiede l'autenticazione.
 
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
> I comandi inline vengono archiviati come parte della definizione del modello di immagine, che possono essere visualizzati quando si esegue il dump della definizione dell'immagine e sono visibili anche per supporto tecnico Microsoft nel caso di un caso di supporto a scopo di risoluzione dei problemi. Se si dispone di comandi o valori sensibili, si consiglia vivamente di spostarli negli script e di usare un'identità utente per l'autenticazione in archiviazione di Azure.

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
    * Per generare sha256Checksum, usare il comando [Get-hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) di PowerShell in Windows


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

```json
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
OS support: Windows
```

Proprietà customize:
- **type** - WindowsUpdate.
- **searchCriteria** - facoltativo, definisce il tipo di aggiornamenti installati (consigliati, importanti e così via), BrowseOnly=0 e IsInstalled=0 (scelta consigliata) è il valore predefinito.
- **filters** - facoltativo, consente di specificare un filtro per includere o escludere gli aggiornamenti.
- **updateLimit** - facoltativo, definisce il numero di aggiornamenti che è possibile installare, valore predefinito 1000.
 
> [!NOTE]
> Il Windows Update verbi può avere esito negativo se sono presenti riavvii di Windows in attesa o se le installazioni dell'applicazione sono ancora in esecuzione, in genere è possibile che questo errore venga visualizzato nel file customization. log `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` . È consigliabile prendere in considerazione l'aggiunta di un riavvio di Windows e/o consentire alle applicazioni un tempo sufficiente per completare le installazioni usando i comandi [Sleep] o Wait ( https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) nei comandi o negli script inline prima di eseguire Windows Update.

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

È possibile distribuire un'immagine in entrambi i tipi di destinazione nella stessa configurazione.

> [!NOTE]
> Il comando Sysprep AIB predefinito non include "/Mode: VM", tuttavia questo potrebbe essere necessario quando si creano immagini per le quali verrà installato il ruolo HyperV. Se è necessario aggiungere questo argomento di comando, è necessario eseguire l'override del comando Sysprep.

Poiché è possibile distribuire in più di una destinazione, Image Builder mantiene uno stato per ogni destinazione di distribuzione a cui è possibile accedere eseguendo una query per `runOutputName`.  `runOutputName` è un oggetto per cui è possibile eseguire una query dopo la distribuzione per ottenere informazioni su tale distribuzione. Ad esempio, è possibile eseguire una query per il percorso del disco rigido virtuale o delle aree in cui è stata replicata la versione dell'immagine oppure in cui è stata creata la versione dell'immagine SIG. Si tratta di una proprietà di tutte le destinazioni di distribuzione. `runOutputName` deve essere univoco per ogni destinazione di distribuzione. Di seguito è riportato un esempio che esegue una query per una distribuzione di Raccolta immagini condivise:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
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
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
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
 
Prima di poter distribuire in Raccolta immagini, è necessario creare una raccolta e una definizione dell'immagine, vedere [Immagini condivise](../shared-images-cli.md). 

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
- **galleryImageId** : ID della raccolta di immagini condivise, che può essere specificato in due formati:
    * Controllo automatico delle versioni: generatore di immagini genera un numero di versione monotona per l'utente. questa operazione è utile quando si vuole salvare le immagini dallo stesso modello: il formato è: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>` .
    * Controllo delle versioni esplicito: è possibile passare il numero di versione che si vuole venga usato da Image Builder. Il formato è: `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** - nome univoco per identificare la distribuzione.  
- **artifactTags** - facoltativo, tag della coppia chiave-valore specificata dall'utente.
- **replicationRegions** - matrice di aree per la replica. Una delle aree deve essere l'area in cui è distribuita la raccolta. L'aggiunta di aree comporta un aumento del tempo di compilazione, in quanto la compilazione non viene completata fino al completamento della replica.
- **excludeFromLatest** (facoltativo) consente di contrassegnare la versione dell'immagine creata non usata come ultima versione nella definizione del sig. il valore predefinito è "false".
- **storageAccountType** (facoltativo) AIB supporta la specifica di questi tipi di archiviazione per la versione dell'immagine da creare:
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> Se il modello di immagine e a cui viene fatto riferimento `image definition` non si trovano nella stessa posizione, verrà visualizzato un ulteriore tempo per la creazione di immagini. Il generatore di immagini attualmente non dispone di un `location` parametro per la risorsa di versione dell'immagine `image definition` . Se, ad esempio, la definizione di un'immagine si trova in westus e si desidera che la versione dell'immagine venga replicata in eastus, viene copiato un BLOB in westus, da questo, viene creata una risorsa di versione dell'immagine in westus, che viene quindi replicata in eastus. Per evitare il tempo di replica aggiuntivo, verificare `image definition` che il modello di immagine e si trovino nella stessa posizione.


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

## <a name="image-template-operations"></a>Operazioni sui modelli di immagine

### <a name="starting-an-image-build"></a>Avvio di una compilazione di immagini
Per avviare una compilazione, è necessario richiamare ' Run ' nella risorsa modello di immagine, esempi di `run` comandi:

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>Annullamento di una compilazione dell'immagine
Se si sta eseguendo una compilazione di immagini che si ritiene non corretta, in attesa dell'input dell'utente o se si ritiene che non verrà mai completata correttamente, è possibile annullare la compilazione.

La compilazione può essere annullata in qualsiasi momento. Se la fase di distribuzione è stata avviata, è comunque possibile annullare, ma sarà necessario eseguire la pulizia di tutte le immagini che potrebbero non essere completate. Il comando Annulla non attende il completamento dell'annullamento. monitorare l' `lastrunstatus.runstate` annullamento dello stato di avanzamento, usando questi [comandi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#get-statuserror-of-the-template-submission-or-template-build-status)di stato.


Esempi di `cancel` comandi:

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>Passaggi successivi

Nel [GitHub di Image Builder di Azure](https://github.com/danielsollondon/azvmimagebuilder) sono disponibili file JSON di esempio per diversi scenari.
