---
title: Attività DevOps del servizio Generatore di immagini di Azure
description: Attività DevOps di Azure per inserire gli artefatti di compilazione in un'immagine di macchina virtuale in modo da poter installare e configurare l'applicazione e il sistema operativo.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 88bbd83d7ac5b834255c9b4d46d7cef4394f15d3
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968668"
---
# <a name="azure-image-builder-service-devops-task"></a>Attività DevOps del servizio Generatore di immagini di Azure

Questo articolo illustra come usare un'attività DevOps di Azure per inserire gli artefatti di compilazione in un'immagine di macchina virtuale in modo da poter installare e configurare l'applicazione e il sistema operativo.

## <a name="devops-task-versions"></a>Versioni dell'attività DevOps
Sono disponibili due attività DevOps di Azure VM Image Builder (AIB):

* [Attività AIB "stabile"](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), che consente di inserire gli aggiornamenti e le funzionalità più recenti, consentire ai clienti di testarli, prima di innalzarli di livello all'attività "stabile", circa una settimana in un secondo momento. 


* [Attività AIB ' unstable '](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), che consente di inserire gli aggiornamenti e le funzionalità più recenti, consentire ai clienti di testarli, prima di innalzarli di livello all'attività "stabile". Se non sono presenti problemi segnalati e i dati di telemetria non mostrano alcun problema, circa una settimana dopo, il codice dell'attività verrà innalzato di livello a "stabile". 

## <a name="prerequisites"></a>Prerequisiti

* Installare l' [attività DevOps stabile dal Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* È necessario disporre di un account VSTS DevOps e di una pipeline di compilazione creata
* Registrare e abilitare i requisiti delle funzionalità del generatore di immagini nella sottoscrizione usata dalle pipeline:
    * [AZ PowerShell](../windows/image-builder-powershell.md#register-features)
    * [INTERFACCIA della riga di comando AZ](../windows/image-builder.md#register-the-features)
    
* Creare un account di archiviazione di Azure standard nel gruppo di risorse dell'immagine di origine, è possibile usare altri account di archiviazione o del gruppo di risorse. L'account di archiviazione viene usato per trasferire gli elementi di compilazione dall'attività DevOps all'immagine.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Aggiungi attività alla pipeline di rilascio

Selezionare **modifica pipeline di rilascio**  >  **Edit**

Nell'agente utente selezionare *+* per aggiungere quindi Cerca **Generatore di immagini**. Selezionare **Aggiungi**.

Impostare le proprietà dell'attività seguenti:

### <a name="azure-subscription"></a>Sottoscrizione di Azure

Selezionare dal menu a discesa la sottoscrizione che si desidera venga eseguita dal generatore di immagini. Usare la stessa sottoscrizione in cui si trovano le immagini di origine e la posizione in cui devono essere distribuite le immagini. È necessario autorizzare l'accesso del collaboratore al generatore di immagini alla sottoscrizione o al gruppo di risorse.

### <a name="resource-group"></a>Resource Group

Usare il gruppo di risorse in cui verrà archiviato l'artefatto del modello di immagine temporanea. Quando si crea un elemento del modello, viene creato un altro gruppo di risorse generatore di immagini temporanee `IT_<DestinationResourceGroup>_<TemplateName>_guid` . Il gruppo di risorse temporaneo archivia i metadati dell'immagine, ad esempio gli script. Al termine dell'attività, l'artefatto del modello di immagine e il gruppo di risorse generatore di immagini temporanee vengono eliminati.
 
### <a name="location"></a>Posizione

Il percorso è l'area in cui verrà eseguito il generatore di immagini. Sono supportati solo un numero impostato di [aree](../windows/image-builder-overview.md#regions) . Le immagini di origine devono essere presenti in questa posizione. Se ad esempio si usa raccolta immagini condivise, è necessario che sia presente una replica in tale area.

### <a name="managed-identity-required"></a>Identità gestita (obbligatoria)
Il generatore di immagini richiede un'identità gestita, che usa per leggere immagini personalizzate di origine, connettersi ad archiviazione di Azure e creare immagini personalizzate. Per altri dettagli, vedere [qui](./image-builder-overview.md#permissions).

### <a name="vnet-support"></a>Supporto di VNET

Attualmente, l'attività DevOps non supporta la specifica di una subnet esistente. si tratta della roadmap, ma se si vuole usare un VNET esistente, è possibile usare un modello ARM con un modello di generatore di immagini annidato in. per esempi, vedere il modello di generatore di immagini Windows per informazioni su come ottenere questo risultato oppure usare il comando [AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Source (Sorgente)

Le immagini di origine devono essere dei sistemi operativi del generatore di immagini supportati. È possibile scegliere immagini personalizzate esistenti nella stessa area in cui è in esecuzione Image Builder:
* Immagine gestita: è necessario passare il resourceId, ad esempio:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Raccolta di immagini condivise di Azure: è necessario passare il resourceId della versione dell'immagine, ad esempio:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Se è necessario ottenere la versione più recente della raccolta immagini condivise, è possibile avere un'attività AZ PowerShell o AZ CLI prima che ottenga la versione più recente e imposti una variabile DevOps. Usare la variabile nell'attività AZ VM Image Builder DevOps. Per ulteriori informazioni, vedere gli [esempi](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Mercato Immagine di base è disponibile un elenco a discesa di immagini diffuse, che userà sempre la versione più recente del sistema operativo supportato. 

    Se l'immagine di base non è presente nell'elenco, è possibile specificare l'immagine esatta usando `Publisher:Offer:Sku` .

    Versione dell'immagine di base (facoltativo): è possibile specificare la versione dell'immagine che si vuole usare. il valore predefinito è `latest` .

### <a name="customize"></a>Personalizza

#### <a name="provisioner"></a>Provisioning

Inizialmente, sono supportati due personalizzatori: **Shell** e **PowerShell**. È supportato solo inline. Se si desidera scaricare gli script, è possibile passare i comandi inline a tale scopo.

Per il sistema operativo, selezionare PowerShell o Shell.

#### <a name="windows-update-task"></a>Attività Windows Update

Solo per Windows, l'attività viene eseguita Windows Update alla fine delle personalizzazioni. Gestisce i riavvii richiesti.

Viene eseguita la seguente configurazione di Windows Update:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Installa gli aggiornamenti di Windows importanti e consigliati che non sono in anteprima.

#### <a name="handling-reboots"></a>Gestione dei riavvii
Attualmente l'attività DevOps non dispone del supporto per il riavvio delle compilazioni di Windows, se si tenta di riavviare con il codice PowerShell, la compilazione avrà esito negativo. Tuttavia, è possibile usare il codice per riavviare le compilazioni di Linux.

#### <a name="build-path"></a>Percorso di compilazione

L'attività è progettata per essere in grado di inserire artefatti della versione della build di DevOps nell'immagine. Per eseguire questa operazione, è necessario configurare una pipeline di compilazione. Nel programma di installazione della pipeline di rilascio è necessario aggiungere il repository degli elementi di compilazione.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Selezionare Aggiungi un artefatto nella pipeline di rilascio.":::

Selezionare il pulsante **percorso compilazione** per scegliere la cartella di compilazione che si desidera inserire nell'immagine. L'attività generatore di immagini copia tutti i file e le directory al suo interno. Quando si crea l'immagine, Image Builder distribuisce i file e le directory in percorsi diversi, a seconda del sistema operativo.

> [!IMPORTANT]
> Quando si aggiunge un elemento del repository, è possibile che la directory sia preceduta da un carattere di sottolineatura *_*. Il carattere di sottolineatura può causare problemi con i comandi inline. Utilizzare le virgolette appropriate nei comandi.
> 

L'esempio seguente illustra come funziona:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Selezionare Aggiungi un artefatto nella pipeline di rilascio.":::


* Windows: i file sono disponibili in `C:\` . Viene creata una directory denominata `buildArtifacts` che include la `webapp` Directory.

* Linux: i file sono presenti in  `/tmp` . `webapp`Viene creata la directory che include tutti i file e le directory. È necessario spostare i file da questa directory. In caso contrario, verranno eliminati perché si trova nella directory temporanea.

#### <a name="inline-customization-script"></a>Script di personalizzazione inline

* Windows: è possibile immettere comandi inline di PowerShell separati da virgole. Se si vuole eseguire uno script nella directory di compilazione, è possibile usare:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux: nei sistemi Linux gli artefatti di compilazione vengono inseriti nella `/tmp` Directory. Tuttavia, in molti sistemi operativi Linux, al riavvio, il contenuto della directory/tmp viene eliminato. Se si desidera che gli elementi siano presenti nell'immagine, è necessario creare un'altra directory e copiarli.  Ad esempio:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Se si usa la directory "tmp", è possibile usare il codice seguente per eseguire lo script.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Cosa accade agli artefatti di compilazione dopo la compilazione dell'immagine?

> [!NOTE]
> Il generatore di immagini non rimuove automaticamente gli elementi di compilazione, pertanto si consiglia di disporre sempre di codice per rimuovere gli artefatti di compilazione.
> 

* Il generatore di immagini Windows distribuisce i file nella `c:\buildArtifacts` Directory. La directory è permanente. è necessario rimuovere la directory. È possibile rimuoverlo nello script eseguito. Ad esempio:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux: gli artefatti di compilazione vengono inseriti nella `/tmp` Directory. Tuttavia, in molti sistemi operativi Linux, al riavvio, il `/tmp` contenuto della directory viene eliminato. Si consiglia di disporre di codice per rimuovere il contenuto e non basarsi sul sistema operativo per rimuovere il contenuto. Ad esempio:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Lunghezza totale della compilazione dell'immagine

Non è ancora possibile modificare la lunghezza totale nell'attività della pipeline DevOps. Usa il valore predefinito di 240 minuti. Se si vuole aumentare il valore di [buildTimeoutInMinutes](./image-builder-json.md?bc=%252fazure%252fvirtual-machines%252fwindows%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json#properties-buildtimeoutinminutes), è possibile usare un'attività AZ CLI nella pipeline di rilascio. Configurare l'attività per copiare un modello e inviarlo. Per un esempio, vedere questa [soluzione](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)o usare AZ PowerShell.


#### <a name="storage-account"></a>Account di archiviazione

Selezionare l'account di archiviazione creato nei prerequisiti. Se non viene visualizzato nell'elenco, Image Builder non dispone delle autorizzazioni necessarie.

Quando viene avviata la compilazione, Image Builder creerà un contenitore denominato `imagebuilder-vststask` . Il contenitore è la posizione in cui vengono archiviati gli artefatti di compilazione del repository.

> [!NOTE]
> È necessario eliminare manualmente l'account di archiviazione o il contenitore dopo ogni compilazione.
>

### <a name="distribute"></a>Distribuire

Sono supportati 3 tipi di distribuzione.

#### <a name="managed-image"></a>Immagine gestita

* ResourceId
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Percorsi

#### <a name="azure-shared-image-gallery"></a>Raccolta di immagini condivise di Azure

La raccolta di immagini condivise **deve** esistere già.

* ResourceId 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regions: elenco di aree, separate da virgole. Ad esempio, westus, eastus, centralus

#### <a name="vhd"></a>VHD 

Non è possibile passare alcun valore a questo, il generatore di immagini creerà il disco rigido virtuale nel gruppo di risorse generatore di immagini temporanee, `IT_<DestinationResourceGroup>_<TemplateName>` , nel contenitore *VHD* . Quando si avvia la build di rilascio, Image Builder genera log. Al termine, verrà generato l'URL del disco rigido virtuale.

### <a name="optional-settings"></a>Impostazioni facoltative

* [Dimensioni macchina virtuale](image-builder-json.md#vmprofile) : è possibile eseguire l'override delle dimensioni della macchina virtuale, dal valore predefinito di *Standard_D1_v2*. È possibile eseguire l'override di per ridurre il tempo di personalizzazione totale o perché si vuole creare le immagini che dipendono da determinate dimensioni di VM, ad esempio GPU/HPC e così via.

## <a name="how-it-works"></a>Funzionamento

Quando si crea la versione, l'attività crea un contenitore nell'account di archiviazione denominato *ImageBuilder-vststask*. Consente di eseguire la cerniera e di caricare gli elementi di compilazione e di creare un token di firma di accesso condiviso per il file zip.

L'attività USA le proprietà passate all'attività per creare l'artefatto del modello di generatore di immagini. L'attività esegue le operazioni seguenti:
* Scarica il file zip dell'artefatto di compilazione e qualsiasi altro script associato. I file vengono salvati in un account di archiviazione nel gruppo di risorse generatore di immagini temporanee `IT_<DestinationResourceGroup>_<TemplateName>` .
* Crea un *t_* con prefisso del modello e un intero monotonic a 10 cifre. Il modello viene salvato nel gruppo di risorse selezionato. Il modello esiste per la durata della compilazione nel gruppo di risorse. 

Output di esempio:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Quando viene avviata la compilazione dell'immagine, lo stato di esecuzione viene segnalato nei registri versione:

```text
starting run template...
```

Al termine della compilazione dell'immagine, viene visualizzato un output simile al testo seguente:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Il modello di immagine e `IT_<DestinationResourceGroup>_<TemplateName>` viene eliminato.

È possibile usare la variabile VSTS ' $ (imageUri)' e usarla nell'attività successiva o usare semplicemente il valore e compilare una VM.

## <a name="output-devops-variables"></a>Variabili DevOps di output

Pubblicazione/offerta/SKU/versione dell'immagine del Marketplace di origine:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

URI dell'immagine: il ResourceID dell'immagine distribuita:
* $ (imageUri)

## <a name="faq"></a>Domande frequenti

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>È possibile usare un modello di immagine esistente già creato, al di fuori di DevOps?

Attualmente non in questo momento.

### <a name="can-i-specify-the-image-template-name"></a>È possibile specificare il nome del modello di immagine?

No. Viene utilizzato un nome di modello univoco, che viene quindi eliminato.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Il generatore di immagini non è riuscito. Come è possibile risolvere I problemi?

Se si verifica un errore di compilazione, l'attività DevOps non elimina il gruppo di risorse di gestione temporanea. È possibile accedere al gruppo di risorse di gestione temporanea che contiene il log di personalizzazione della compilazione.

Verrà visualizzato un errore nel registro DevOps per l'attività generatore di immagini VM e verrà visualizzato il percorso Customization. log. Ad esempio:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Selezionare Aggiungi un artefatto nella pipeline di rilascio.":::

Per altre informazioni sulla risoluzione dei problemi, vedere [risolvere i problemi del servizio Generatore di immagini di Azure](image-builder-troubleshoot.md). 

Dopo aver esaminato l'errore, è possibile eliminare il gruppo di risorse di gestione temporanea. Prima di tutto, eliminare l'elemento di risorsa modello di immagine. L'artefatto è preceduto da *t_* e si trova nel log di compilazione delle attività DevOps:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

L'elemento di risorsa modello di immagine si trova nel gruppo di risorse specificato inizialmente nell'attività. Al termine della risoluzione dei problemi, eliminare l'artefatto. Se si elimina usando il portale di Azure, nel gruppo di risorse selezionare **Mostra tipi nascosti**per visualizzare l'artefatto.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di Azure Image Builder](image-builder-overview.md).