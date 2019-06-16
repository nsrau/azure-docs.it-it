---
title: Creare un modello di generatore di immagini di Azure (anteprima)
description: Informazioni su come creare un modello da usare con il generatore di immagini di Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: cf8264cbad3c5c88c58cff3b95cb5c68adf0686c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65538286"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Anteprima: Creare un modello di generatore di immagini di Azure 

Azure Image Builder Usa un file con estensione JSON per passare le informazioni al servizio di Image Builder. In questo articolo verranno esaminate le sezioni del file json, pertanto è possibile compilare. Per esempi di file con estensione JSON completo, vedere la [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Si tratta del formato di modello di base:

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
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versione API

Il `type` è il tipo di risorsa, che deve essere `"Microsoft.VirtualMachineImages/imageTemplates"`. Il `apiVersion` cambieranno nel corso del tempo come le modifiche dell'API, ma dovrebbe essere `"2019-05-01-preview"` per l'anteprima.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Località

Il percorso è l'area in cui verrà creata l'immagine personalizzata. Per l'anteprima di Image Builder, sono supportate le seguenti aree:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Dipende (facoltativo)

Questa sezione facoltativa utilizzabile per garantire che le dipendenze vengono completate prima di procedere. 

```json
    "dependsOn": [],
```

Per altre informazioni, vedere [definire le dipendenze delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identità
Per impostazione predefinita, supporta Image Builder usando script o la copia di file da più posizioni, ad esempio GitHub e archiviazione di Azure. Per usare questi modelli, devono essere accessibili pubblicamente.

È anche possibile usare un Azure User-Assigned identità gestita, definite dall'utente, per consentire l'accesso di Image Builder archiviazione di Azure, purché l'identità è stato concesso un minimo di 'Lettore di dati Blob di archiviazione' nell'account di archiviazione di Azure. Ciò significa che non è necessario rendere il BLOB di archiviazione accessibile esternamente o i token di firma di accesso condiviso di installazione.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Per un esempio completo, vedere [ usare un'identità gestita Azure User-Assigned per accedere ai file in archiviazione di Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Supporto per un'identità assegnata dall'utente di generatore di immagini: • supporta una singola identità solo • non supporta nomi di dominio personalizzati

Per altre informazioni, vedere [What ' s identità gestita per le risorse di Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Per altre informazioni sulla distribuzione di questa funzionalità, vedere [Configura gestiti le identità per le risorse di Azure in una VM di Azure usando Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Proprietà: origine

Il `source` sezione contiene informazioni sull'immagine di origine che verrà utilizzato dal generatore di immagini.

L'API richiede un 'SourceType' che definisce l'origine per la compilazione dell'immagine, attualmente sono disponibili tre tipi:
- ISO - utilizzare questa opzione quando l'origine è un'immagine ISO di RHEL.
- PlatformImage - indicato l'immagine di origine è un'immagine del Marketplace.
- ManagedImage - utilizzare questa opzione quando a partire da un'immagine gestita normale.
- SharedImageVersion - viene usato quando si usa una versione di immagine in una raccolta di immagini condivise come origine.

### <a name="iso-source"></a>Origine ISO

Azure Image Builder supporta solo l'utilizzo pubblicato Red Hat Enterprise Linux 7.x binario DVD le immagini ISO, per l'anteprima. Generatore di immagini supporta:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Per ottenere il `sourceURI` e `sha256Checksum` valori, passare alla `https://access.redhat.com/downloads` quindi selezionare il prodotto **Red Hat Enterprise Linux**e una versione supportata. 

Nell'elenco degli **i programmi di installazione e le immagini di Red Hat Enterprise Linux Server**, è necessario copiare il collegamento per Red Hat Enterprise Linux 7.x binario DVD e il valore di checksum.

> [!NOTE]
> I token di accesso dei collegamenti vengono aggiornati a intervalli frequenti, pertanto ogni volta che si vuole inviare un modello, è necessario controllare se il collegamento di RH indirizzo è stato modificato.
 
### <a name="platformimage-source"></a>Origine PlatformImage 
Azure Image Builder supporta le immagini di Marketplace di Azure seguenti:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


La proprietà qui sono le stesse che vengono usati per creare VM, con AZ CLI, eseguire il seguente per ottenere le proprietà: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Versione non può essere 'latest', è necessario usare il comando precedente per ottenere un numero di versione. 

### <a name="managedimage-source"></a>Origine ManagedImage

Imposta l'immagine di origine come un'immagine gestita esistente di una macchina virtuale o un disco rigido virtuale generalizzato. L'immagine gestita di origine deve essere di un sistema operativo supportato e deve essere nella stessa area come il modello generatore di immagini di Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Il `imageId` deve essere il valore ResourceId dell'immagine gestita. Usare `az image list` per elencare le immagini disponibili.


### <a name="sharedimageversion-source"></a>Origine SharedImageVersion
Imposta l'immagine di origine di una versione di immagine esistente in una raccolta di immagini condivise. La versione dell'immagine deve essere di un sistema operativo supportato, e l'immagine deve essere replicata nella stessa area come il modello generatore di immagini di Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Il `imageVersionId` deve essere il valore di ResourceId della versione dell'immagine. Uso [elenco versione dell'immagine di az sig](/cli/azure/sig/image-version#az-sig-image-version-list) per elencare le versioni dell'immagine.

## <a name="properties-customize"></a>Proprietà: personalizzare


Generatore di immagini supporta più 'impegnati nella personalizzazione'. Impegnati nella personalizzazione sono funzioni che consentono di personalizzare l'immagine, ad esempio esecuzione di script o il riavvio dei server. 

Quando si usa `customize`: 
- È possibile usare più impegnati nella personalizzazione, ma devono avere un valore univoco `name`.
- Impegnati nella personalizzazione eseguite nell'ordine specificato nel modello.
- Se un addetto personalizzazione non riesce, il componente intera personalizzazione avrà esito negativo e segnalare un errore.
- Prendere in considerazione il tempo di compilazione dell'immagine verrà richiedono e modificare la proprietà 'buildTimeoutInMinutes' per consentire tempo sufficiente per completare il generatore di immagini.
- È consigliabile che testare accuratamente lo script prima di utilizzarlo in un modello. Debug dello script nella propria VM risulterà più semplice.
- Non inserire i dati riservati negli script. 
- I percorsi di script devono essere accessibile pubblicamente, a meno che non si usa [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
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

 
La sezione Personalizza è una matrice. Azure Image Builder verrà eseguito tramite l'utilità di personalizzazione in ordine sequenziale. Qualsiasi errore di qualsiasi personalizzazione avrà esito negativo del processo di compilazione. 
 
 
### <a name="shell-customizer"></a>Personalizzazione della shell

L'addetto personalizzazione shell supporta l'esecuzione di script della shell, si deve essere accessibile pubblicamente per IB per accedervi.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
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

- **tipo** : Shell 
- **nome** : nome per la personalizzazione di rilevamento 
- **scriptUri** -URI per il percorso del file 
- **inline** -matrice di comandi della shell, separati da virgole.
 
> [!NOTE]
> Quando si esegue la personalizzazione della shell con origine RHEL ISO, è necessario assicurarsi che gli handle di shell personalizzazione prima la registrazione con un server di entitlement Red Hat prima che venga eseguita alcuna personalizzazione. Dopo aver completata la personalizzazione, lo script deve annullare la registrazione con il server dei diritti.

### <a name="windows-restart-customizer"></a>Personalizzazione di riavviare Windows 
La personalizzazione di riavvio consente di riavviare una macchina virtuale Windows e attendere che torni online, in questo modo è possibile installare il software che richiede un riavvio.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Supporto del sistema operativo: Windows
 
Personalizzare le proprietà:
- **Tipo**: WindowsRestart
- **restartCommand** -comando per eseguire il riavvio (facoltativo). Il valore predefinito è `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** : comando per verificare se il riavvio ha avuto esito positivo (facoltativo). 
- **restartTimeout** -riavviare timeout specificato sotto forma di stringa di grandezza e unità. Ad esempio, `5m` (5 minuti) o `2h` (2 ore). Il valore predefinito è: ' 5M '


### <a name="powershell-customizer"></a>Personalizzazione di PowerShell 
L'addetto personalizzazione shell supporta l'esecuzione degli script di PowerShell e comandi inline, gli script devono essere accessibili pubblicamente per IB per accedervi.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Supporto del sistema operativo: Windows e Linux

Personalizzare le proprietà:

- **tipo** -PowerShell.
- **scriptUri** -URI per il percorso del file di script di PowerShell. 
- **inline** – Inline comandi da eseguire, separate da virgole.
- **valid_exit_codes** : valore facoltativo, codici validi che possono essere restituiti dallo script/inline di comando, si eviterà di errore segnalato del comando script/inline.

### <a name="file-customizer"></a>Personalizzazione del file

La personalizzazione di File consente Generatore immagini scaricare un file da un GitHub o da archiviazione di Azure. Se si dispone di una pipeline di compilazione di immagini che si basa su elementi di compilazione, è possibile impostare la personalizzazione di file per il download dalla condivisione di compilazione e spostare gli elementi nell'immagine.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Supporto del sistema operativo: Linux e Windows 

Proprietà addetto personalizzazione del file:

- **sourceUri** -un endpoint di archiviazione accessibili, può trattarsi di archiviazione di Azure o GitHub. È possibile scaricare solo un file, non un'intera directory. Se è necessario scaricare una directory, usare un file compresso, quindi decomprimere gli impegnati nella personalizzazione della Shell o PowerShell. 
- **destinazione** – si tratta del nome di file e percorso completo di destinazione. Qualsiasi percorso di riferimento e le sottodirectory devono esiste, usare gli impegnati nella personalizzazione della Shell o PowerShell per impostare tali elementi in anticipo. È possibile utilizzare gli impegnati nella personalizzazione di script per creare il percorso. 

Questa è supportata dalla directory di Windows e Linux percorsi, ma esistono alcune differenze: 
- Linux nei sistemi operativi: l'unico percorso di Image builder possono scrivere è /tmp.
- Windows: nessuna restrizione di percorso, ma il percorso deve esistere.
 
 
Se si è verificato un errore tentando di scaricare il file o inserirlo in una directory specificata, il passaggio Personalizza avrà esito negativo e sarà il customization.log.

I file di personalizzazione il File possono essere scaricati da archiviazione di Azure usando [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalizzazione 
Per impostazione predefinita, generatore di immagini di Azure vengono eseguiti anche 'il deprovisioning di' codice alla fine di ogni fase di personalizzazione di immagine, per generalizzare l'immagine. Generalizzazione di è un processo in cui l'immagine è configurato in modo che possa essere riutilizzato per creare più macchine virtuali. Per le macchine virtuali Windows, Azure Image Builder Usa Sysprep. Per Linux, viene eseguito Azure Image Builder ' waagent-deprovision'. 

I comandi agli utenti di Image Builder generalizzare potrebbero non essere adatti per ogni situazione, in modo che Azure Image Builder modo sarà possibile personalizzare questo comando, se necessario. 

Se si esegue la migrazione di personalizzazione esistente e si usano comandi Sysprep/waagent diversi, è possibile usando i comandi di Image Builder generici e se la creazione della macchina virtuale non riesce, usare i propri comandi Sysprep o waagent.

Se Azure Image Builder crea correttamente un'immagine personalizzata di Windows e si crea una VM dal, quindi individuare la creazione della VM non riesce o non viene completata correttamente, sarà necessario consultare la documentazione di Sysprep di Windows Server o generare una richiesta di supporto con il Team di supporto di servizi tecnico di Sysprep di Windows Server, in grado di risolvere i problemi e consigliare l'utilizzo di Sysprep corretto.


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
#### <a name="default-linux-deprovision-command"></a>Comando deprovision Linux predefinito

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Si esegue l'override di comandi
Per ignorare i comandi, usare gli strumenti di provisioning di uno script PowerShell o la Shell per creare i file di comando con il nome file esatto e le inserisce nelle directory corrette:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder leggerà i comandi seguenti, questi vengono scritti nei log di un file AIB, 'customization.log'. Visualizzare [risoluzione dei problemi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) su come raccogliere i log.
 
## <a name="properties-distribute"></a>Proprietà: distribuire

Azure Image Builder supporta tre destinazioni di distribuzione: 

- **managedImage** - managed image.
- **sharedImage** -raccolta di immagini condivise.
- **Disco rigido virtuale** -disco rigido virtuale in un account di archiviazione.

È possibile distribuire un'immagine in entrambi i tipi di destinazione nella stessa configurazione, consultare [esempi](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Poiché è possibile avere più di una destinazione da distribuire ai, Image Builder mantiene uno stato per ogni destinazione di distribuzione che è possibile accedere eseguendo una query di `runOutputName`.  Il `runOutputName` è un oggetto è possibile eseguire una query post distribuzione per informazioni su tale distribuzione. Ad esempio, è possibile eseguire una query con il percorso del disco rigido virtuale o le aree in cui la versione dell'immagine sia stata replicata in. Si tratta di una proprietà di ogni destinazione di distribuzione. Il `runOutputName` deve essere univoco per ogni destinazione di distribuzione.
 
### <a name="distribute-managedimage"></a>Distribuisci: managedImage

L'immagine di output sarà una risorsa immagine gestita.

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
- **tipo** – managedImage 
- **ID immagini** : ID risorsa dell'immagine di destinazione, previsto il formato: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **percorso** -percorso dell'immagine gestita.  
- **runOutputName** : univoco nome per identificare la distribuzione.  
- **artifactTags** -utente facoltativo specificato tag coppia valore della chiave.
 
 
> [!NOTE]
> Il gruppo di risorse di destinazione deve esistere.
> Se si desidera che l'immagine distribuita in un'area diversa, aumenterà il tempo di distribuzione. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Raccolta di immagini di Azure condiviso è un nuovo servizio di gestione delle immagini che consente la gestione della replica tra aree di immagine, le immagini personalizzate di condivisione e controllo delle versioni. Azure Image Builder supporta la distribuzione con questo servizio, pertanto è possibile distribuire le immagini per le aree supportate da raccolte di immagini condivise. 
 
Una raccolta di immagini condiviso è costituita da: 
 
- Raccolta - contenitore per più immagini condivise. Una raccolta viene distribuita in un'unica area.
- Definizioni di immagine - un raggruppamento per le immagini concetto. 
- Versioni dell'immagine - questo è un tipo di immagine usato per la distribuzione di un set di scalabilità o della macchina virtuale. Versioni dell'immagine possono essere replicate in altre aree in cui le macchine virtuali dovranno essere distribuiti.
 
Prima di poter distribuire alla raccolta di immagini, è necessario creare una raccolta e una definizione dell'immagine, vedere [le immagini condivise](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribuire le proprietà per raccolte di immagini condivise:

- **type** - sharedImage  
- **galleryImageId** : ID della raccolta immagini condivise. Il formato è: /subscriptions/<subscriptionId>ResourceGroups<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** : univoco nome per identificare la distribuzione.  
- **artifactTags** -utente facoltativo specificato tag coppia valore della chiave.
- **replicationRegions** -matrice di aree per la replica. Una delle aree deve essere l'area in cui viene distribuita nella raccolta.
 
> [!NOTE]
> È possibile usare generatore di immagini di Azure in un'area diversa per la raccolta, ma è necessario il servizio di generazione di immagini di Azure trasferire l'immagine tra i Data Center e l'operazione richiederà più tempo. Image Builder è eseguirà automaticamente la versione dell'immagine, in un numero intero monotonico base, è possibile specificarlo attualmente. 

### <a name="distribute-vhd"></a>Distribuire: VHD  
È possibile l'output in un disco rigido virtuale. È quindi possibile copiare il disco rigido virtuale e usarlo per pubblicare in Azure MarketPlace o usare con Azure Stack.  

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

Distribuire i parametri di disco rigido virtuale:

- **tipo** -disco rigido virtuale.
- **runOutputName** : univoco nome per identificare la distribuzione.  
- **i tag** -utente facoltativo specificato tag coppia valore della chiave.
 
Azure Image Builder non consente all'utente di specificare una posizione dell'account di archiviazione, ma è possibile eseguire una query lo stato del `runOutputs` per ottenere il percorso.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Dopo aver creato il disco rigido virtuale, copiarlo in un percorso diverso, appena possibile. Il disco rigido virtuale verrà archiviato in un account di archiviazione nel gruppo di risorse temporaneo creato quando il modello di immagine viene inviato al servizio Azure Image Builder. Se si elimina il modello di immagine, si perderà il disco rigido virtuale. 
 
## <a name="next-steps"></a>Passaggi successivi

Sono presenti file con estensione JSON di esempio per i diversi scenari nel [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
