---
title: Risolvere i problemi del servizio Azure Image Builder
description: Risolvere i problemi e gli errori comuni quando si usa il servizio Generatore di immagini di VM di Azure
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: dd17057a56e8dfb269a22458b9aa20fefaab68bc
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661109"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Risolvere i problemi del servizio Azure Image Builder

Questo articolo consente di risolvere i problemi comuni che possono verificarsi quando si usa il servizio Generatore immagini di Azure.

Gli errori AIB possono verificarsi in due aree:
- Invio del modello di immagine
- Compilazione immagine

## <a name="troubleshoot-image-template-submission-errors"></a>Risolvere gli errori di invio del modello di immagine

Gli errori di invio del modello di immagine vengono restituiti solo al momento dell'invio. Non è presente alcun log degli errori per gli errori di invio del modello di immagine. Se si è verificato un errore durante l'invio, è possibile restituire l'errore controllando lo stato del modello, esaminando in modo specifico il `ProvisioningState` e `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Per PowerShell, è necessario installare i moduli di [PowerShell](../windows/image-builder-powershell.md#prerequisites)per il generatore di immagini di Azure.

Le sezioni seguenti includono informazioni aggiuntive sulla risoluzione dei problemi per gli errori di invio del modello di immagine comune.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>L'aggiornamento e l'aggiornamento dei modelli di immagine non sono attualmente supportati

#### <a name="error"></a>Errore di

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Causa

Il modello esiste già.

#### <a name="solution"></a>Soluzione

Se si invia un modello di configurazione dell'immagine e l'invio ha esito negativo, esiste ancora un artefatto di modello non riuscito. Eliminare il modello non riuscito.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>L'operazione della risorsa è stata completata con lo stato di provisioning del terminale ' failed '

#### <a name="error"></a>Errore di

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Causa

Nella maggior parte dei casi, si verifica un errore di distribuzione delle risorse dovuto a autorizzazioni mancanti.

#### <a name="solution"></a>Soluzione

A seconda dello scenario, Azure Image Builder potrebbe richiedere le autorizzazioni per:
- Gruppo di risorse immagine di origine o raccolta immagini condivise
- Risorsa immagine di distribuzione o raccolta immagini condivise
- L'account di archiviazione, il contenitore o il BLOB a cui il file verbi accede. 

Per altre informazioni sulla configurazione delle autorizzazioni, vedere Configurare le autorizzazioni del [servizio Azure Image Builder usando l'interfaccia](image-builder-permissions-cli.md) della riga di comando di Azure o [configurare le autorizzazioni del servizio Azure Image Builder con PowerShell](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Errore durante il recupero dell'immagine gestita

#### <a name="error"></a>Errore di

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Causa

Autorizzazioni mancanti.

#### <a name="solution"></a>Soluzione

A seconda dello scenario, Azure Image Builder potrebbe richiedere le autorizzazioni per:
* Gruppo di risorse immagine di origine o raccolta immagini condivise
* Risorsa immagine di distribuzione o raccolta immagini condivise
* L'account di archiviazione, il contenitore o il BLOB a cui il file verbi accede. 

Per altre informazioni sulla configurazione delle autorizzazioni, vedere Configurare le autorizzazioni del [servizio Azure Image Builder usando l'interfaccia](image-builder-permissions-cli.md) della riga di comando di Azure o [configurare le autorizzazioni del servizio Azure Image Builder con PowerShell](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Istruzione di compilazione non riuscita per la versione dell'immagine

#### <a name="error"></a>Errore di
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Causa

Generatore di immagini di Azure non è in grado di individuare l'immagine di origine.

#### <a name="solution"></a>Soluzione

Verificare che l'immagine di origine sia corretta ed esista nella posizione del servizio Generatore di immagini di Azure.

### <a name="downloading-external-file-to-local-file"></a>Download del file esterno nel file locale

#### <a name="error"></a>Errore di

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Causa

Il nome o il percorso del file non è corretto oppure il percorso non è raggiungibile.

#### <a name="solution"></a>Soluzione

Verificare che il file sia raggiungibile. Verificare che il nome e il percorso siano corretti.

## <a name="troubleshoot-build-failures"></a>Risolvere gli errori di compilazione

Per gli errori di compilazione delle immagini, è possibile ottenere l'errore dalla `lastrunstatus` , quindi esaminare i dettagli nel file customization. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Log di personalizzazione

Quando si esegue la compilazione dell'immagine, i log vengono creati e archiviati in un account di archiviazione. Azure Image Builder crea l'account di archiviazione nel gruppo di risorse temporaneo quando si crea un elemento del modello di immagine.

Il nome dell'account di archiviazione usa il modello seguente: **it_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\> **

Ad esempio, *IT_aibmdi_helloImageTemplateLinux01*.

È possibile visualizzare il file customization. log nell'account di archiviazione nel gruppo di risorse selezionando BLOB dell' **account di archiviazione**  >  **Blobs**  >  `packerlogs` .  Quindi selezionare **directory > Customization. log**.


### <a name="understanding-the-customization-log"></a>Informazioni sul log di personalizzazione

Il log è dettagliato. Viene illustrata la compilazione di immagini che include eventuali problemi con la distribuzione dell'immagine, ad esempio la replica della raccolta immagini condivisa. Questi errori vengono visualizzati nel messaggio di errore dello stato del modello di immagine.

Il file customization. log include le fasi seguenti:

1. Distribuire la macchina virtuale di compilazione e le dipendenze usando i modelli ARM nella fase del gruppo di risorse IT_ staging. Questa fase include più post per il provider di risorse di Azure Image Builder:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Stato della fase distribuzioni. Questa fase include lo stato di ogni distribuzione di risorse:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Connettersi alla fase di compilazione della macchina virtuale.

    Se Windows, il servizio Generatore di immagini di Azure si connette utilizzando WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Se Linux, il servizio Generatore di immagini di Azure si connetterà usando SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Esegui la fase di personalizzazione. Quando vengono eseguite le personalizzazioni, è possibile identificarle esaminando il file customization. log. Cercare *(telemetria)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Fase di deprovisioning. Azure Image Builder aggiunge un verbi nascosto. Questo passaggio di deprovisioning è responsabile della preparazione della macchina virtuale per il deprovisioning. Esegue Windows Sysprep (usando c:\DeprovisioningScript.ps1) o il deprovisioning waagent di Linux (tramite/tmp/DeprovisioningScript.sh). 

    Ad esempio:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Fase di pulizia. Una volta completata la compilazione, le risorse del generatore di immagini di Azure vengono eliminate.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Suggerimenti per la risoluzione dei problemi relativi a script/personalizzazione inline
- Testare il codice prima di fornirlo a Image Builder
- Assicurarsi che i criteri e i firewall di Azure consentano la connettività alle risorse remote.
- Inviare i commenti alla console, ad esempio usando `Write-Host` o `echo` , in modo da consentire la ricerca nel file customization. log.

## <a name="troubleshoot-common-build-errors"></a>Risolvere gli errori di compilazione comuni

### <a name="packer-build-command-failure"></a>Errore del comando di compilazione di Packer

#### <a name="error"></a>Errore di

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Causa

Errore di personalizzazione.

#### <a name="solution"></a>Soluzione

Esaminare il log per individuare gli errori di personalizzazione. Cercare *(telemetria)*. 

Ad esempio:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Timeout superato

#### <a name="error"></a>Errore di

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Causa

La compilazione ha superato il timeout di compilazione. Questo errore si verifica in ' LastRunStatus '.

#### <a name="solution"></a>Soluzione

1. Esaminare il file customization. log. Identificare l'ultimo verbi da eseguire. Eseguire `(telemetry)` una ricerca iniziando dalla parte inferiore del log. 

2. Controllare le personalizzazioni degli script. È possibile che le personalizzazioni non eliminino l'interazione dell'utente per i comandi, ad esempio le `quiet` Opzioni. Ad esempio, `apt-get install -y` l'esecuzione dello script è in attesa dell'interazione dell'utente.

3. Se si usa `File` verbi per scaricare gli artefatti maggiori di 20 MB, vedere la sezione soluzioni alternative.

4. Esaminare gli errori e le dipendenze nello script che può causare l'attesa dello script.

5. Se si prevede che le personalizzazioni necessitino di più tempo, aumentare [buildTimeoutInMinutes](image-builder-json.md). Il valore predefinito è quattro ore.

6. Se si hanno azioni a elevato utilizzo di risorse, ad esempio il download di gigabyte di file, prendere in considerazione le dimensioni della VM di compilazione sottostanti. Il servizio usa una macchina virtuale Standard_D1_v2. La macchina virtuale ha, 1 vCPU e 3,5 GB di memoria. Se si sta scaricando 50 GB, le risorse della macchina virtuale potrebbero esaurirsi e causare errori di comunicazione tra il servizio Generatore di immagini di Azure e la macchina virtuale di compilazione. Ritentare la compilazione con una macchina virtuale di memoria più grande, impostando il [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Tempo di download di file lunghi

#### <a name="error"></a>Errore di
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Causa 

Il file verbi sta scaricando un file di grandi dimensioni.

#### <a name="solution"></a>Soluzione

Il file verbi è adatto solo per i download di file di piccole dimensioni inferiori a 20 MB. Per i download di file di dimensioni maggiori, usare uno script o un comando inline. Ad esempio, in Linux è possibile usare `wget` o `curl` . In Windows è possibile usare `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Errore di attesa nella raccolta di immagini condivise

#### <a name="error"></a>Errore di

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Causa

Timeout di Image Builder durante l'attesa dell'aggiunta e della replica dell'immagine nella raccolta di immagini condivise (SIG). Se l'immagine viene inserita nel SIG, è possibile ipotizzare che la compilazione dell'immagine abbia avuto esito positivo. Tuttavia, il processo complessivo non è riuscito perché il generatore di immagini era in attesa della raccolta immagini condivisa per completare la replica. Anche se la compilazione ha avuto esito negativo, la replica continua. È possibile ottenere le proprietà della versione dell'immagine controllando la *runOutput*di distribuzione.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Soluzione

Aumentare il **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Eventi di informazioni sulle risorse Windows insufficienti

#### <a name="error"></a>Errore di

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Causa

Esaurimento delle risorse. Questo problema si verifica in genere con Windows Update in esecuzione usando le dimensioni predefinite della macchina virtuale di compilazione D1_V2.

#### <a name="solution"></a>Soluzione

Aumentare le dimensioni della macchina virtuale di compilazione.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Compilazioni completate, ma non sono stati creati artefatti

#### <a name="error"></a>Errore di

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Causa

Timeout causato dall'attesa della creazione delle risorse di Azure necessarie.

#### <a name="solution"></a>Soluzione

Eseguire di nuovo la compilazione per riprovare.

### <a name="resource-not-found"></a>Resource not found

#### <a name="error"></a>Errore di

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Causa

Autorizzazioni mancanti.

#### <a name="solution"></a>Soluzione

Verificare che il generatore di immagini di Azure disponga di tutte le autorizzazioni necessarie. 

Per altre informazioni sulla configurazione delle autorizzazioni, vedere Configurare le autorizzazioni del [servizio Azure Image Builder usando l'interfaccia](image-builder-permissions-cli.md) della riga di comando di Azure o [configurare le autorizzazioni del servizio Azure Image Builder con PowerShell](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Temporizzazione Sysprep

#### <a name="error"></a>Errore di

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Causa

La causa potrebbe essere un problema di temporizzazione a causa della D1_V2 dimensioni della macchina virtuale. Se le personalizzazioni sono limitate ed eseguite in meno di tre secondi, i comandi sysprep vengono eseguiti dal generatore di immagini di Azure per effettuare il deprovisioning. Quando il generatore di immagini di Azure esegue il deprovisioning, il comando Sysprep controlla la presenza di *WindowsAzureGuestAgent*, che potrebbero non essere completamente installate causando il problema di temporizzazione. 

#### <a name="solution"></a>Soluzione

Aumentare le dimensioni della macchina virtuale. In alternativa, è possibile aggiungere una personalizzazione di sospensione di PowerShell di 60 secondi per evitare il problema di temporizzazione.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Annullamento del generatore dopo il contesto di annullamento del contesto annullato

#### <a name="error"></a>Errore di
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Causa
Il servizio Image Builder usa la porta 22 (Linux) o 5986 (Windows) per connettersi alla macchina virtuale di compilazione. questo errore si verifica quando il servizio viene disconnesso dalla macchina virtuale di compilazione durante una compilazione dell'immagine. I motivi della disconnessione possono variare, ma l'abilitazione o la configurazione di firewall nello script può bloccare le porte precedenti.

#### <a name="solution"></a>Soluzione
Esaminare gli script per le modifiche o l'abilitazione del firewall o le modifiche apportate a SSH o WinRM e assicurarsi che le modifiche consentano la connettività costante tra il servizio e la macchina virtuale di compilazione nelle porte precedenti. Per altre informazioni sulle funzionalità di rete di Image Builder, vedere i [requisiti](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking).

## <a name="devops-task"></a>Attività DevOps 

### <a name="troubleshooting-the-task"></a>Risoluzione dei problemi relativi all'attività
L'attività avrà esito negativo solo se si verifica un errore durante la personalizzazione. in questo caso, l'attività segnalerà un errore e lascerà il gruppo di risorse di staging, con i log, in modo da poter identificare il problema. 

Per individuare il log, è necessario conoscere il nome del modello, passare alla pipeline > compilazione non riuscita > esaminare l'attività AIB DevOps, quindi verranno visualizzati il registro e il nome del modello:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Passare al portale, cercare il nome del modello nel gruppo di risorse e quindi cercare il gruppo di risorse con IT_ *.
Passare all'account di archiviazione > BLOB > contenitori > log.

### <a name="troubleshooting-successful-builds"></a>Risoluzione dei problemi di compilazioni completate
Nei casi in cui è necessario esaminare le compilazioni riuscite e si desidera esaminare il log. Come indicato in precedenza, se la compilazione dell'immagine ha esito positivo, il gruppo di risorse di gestione temporanea che contiene i log verrà eliminato nell'ambito della pulizia. Tuttavia, ciò che è possibile fare è inserire una sospensione dopo il comando inline, quindi ottenere i log quando la compilazione viene sospesa. A tale scopo, attenersi alla procedura seguente:
 
1. Aggiornare il comando inline e aggiungere: write-host/echo "Sleep". questa operazione consentirà di eseguire la ricerca nel log
2. Aggiungere una sospensione per almeno 10mins, è possibile usare il comando [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)o `Sleep` Linux.
3. Usare il metodo precedente per identificare il percorso del log, quindi proseguire con il download e il controllo del log fino a quando non viene chiuso.


### <a name="operation-was-canceled"></a>L'operazione è stata annullata

#### <a name="error"></a>Errore di

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Causa

Se la compilazione non è stata annullata da un utente, è stata annullata dall'agente utente di Azure DevOps. Probabilmente il timeout di 1 ora si è verificato a causa delle funzionalità di Azure DevOps. Se si usa un progetto privato e un agente, si ottengono 60 minuti di tempo di compilazione. Se la compilazione supera il timeout, DevOps Annulla l'attività in esecuzione.

Per altre informazioni sulle funzionalità e sulle limitazioni di Azure DevOps, vedere [agenti ospitati da Microsoft](https://docs.microsoft.com/azure/devops/pipelines/agents/hosted?view=azure-devops#capabilities-and-limitations)
 
#### <a name="solution"></a>Soluzione

È possibile ospitare agenti DevOps personalizzati o cercare di ridurre il tempo di compilazione. Ad esempio, se si esegue la distribuzione nella raccolta di immagini condivise, eseguire la replica in un'area. Se si desidera eseguire la replica in modo asincrono. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Accesso lento a Windows:' attendere il programma di installazione dei moduli di Windows '

#### <a name="error"></a>Errore di
Dopo aver creato un'immagine di Windows 10 con Image Builder, creare una VM dall'immagine, eseguire il protocollo RDP e attendere qualche minuto al primo accesso, visualizzando una schermata blu con il messaggio:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Soluzione
Per prima cosa, nel controllo della compilazione dell'immagine non sono necessari riavvii in attesa aggiungendo un riavvio di Windows verbi come ultima personalizzazione e che l'installazione del software sia completa. Infine, aggiungere l'opzione [/mode: VM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-command-line-options) al Sysprep predefinito usato da AIB, vedere di seguito,' le macchine virtuali create da immagini AIB non creano correttamente ' >' eseguendo l'override dei comandi '  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Le macchine virtuali create da immagini AIB non vengono create correttamente

Per impostazione predefinita, il generatore di immagini di Azure esegue il *deprovisioning* del codice alla fine di ogni fase di personalizzazione dell'immagine per *generalizzare* l'immagine. Generalizzare è un processo in cui l'immagine viene configurata per essere riutilizzata per creare più macchine virtuali ed è possibile passare le impostazioni della macchina virtuale, ad esempio nome host, nome utente e così via. Per Windows, Azure Image Builder esegue *Sysprep*e per le esecuzioni di Azure Image Builder per Linux `waagent -deprovision` . 

Per Windows, Azure Image Builder usa un comando Sysprep generico. Tuttavia, questo potrebbe non essere adatto a ogni generalizzazione di Windows completata. Il generatore di immagini di Azure consente di personalizzare il comando Sysprep. Si noti che il generatore di immagini di Azure è uno strumento di automazione delle immagini. È responsabile dell'esecuzione corretta del comando Sysprep. Tuttavia, potrebbero essere necessari comandi sysprep diversi per riutilizzarla. Per Linux, Azure Image Builder usa un `waagent -deprovision+user` comando generico. Per ulteriori informazioni, vedere [Microsoft Azure documentazione dell'agente Linux](https://github.com/Azure/WALinuxAgent#command-line-options).

Se si esegue la migrazione di una personalizzazione esistente e si usano comandi sysprep/waagent diversi, è possibile provare i comandi generici del generatore di immagini. Se la creazione della macchina virtuale ha esito negativo, usare i comandi sysprep/waagent precedenti.

> [!NOTE]
> Se AIB crea correttamente un'immagine personalizzata di Windows e si crea una macchina virtuale da tale immagine, la macchina virtuale non verrà creata correttamente (ad esempio, il comando di creazione della macchina virtuale non viene completato/timeout), sarà necessario esaminare la documentazione di Windows Server Sysprep. In alternativa, è possibile generare una richiesta di supporto con il team di supporto di Windows Server Sysprep Customer Services, che può risolvere i problemi e consigliare il comando Sysprep corretto.

### <a name="command-locations-and-filenames"></a>Percorsi del comando e nomi file

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Comando Sysprep: Windows

```PowerShell
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

### <a name="deprovision-command-linux"></a>Comando di deprovisioning: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Override dei comandi

Per eseguire l'override dei comandi, usare i provisioner di script PowerShell o Shell per creare i file di comando con il nome esatto del file e inserirli nelle directory elencate in precedenza. Azure Image Builder legge questi comandi e l'output viene scritto nel file *Customization. log*.

## <a name="getting-support"></a>Possibilità di ricevere assistenza
Se si è fatto riferimento alle linee guida e non è ancora possibile risolvere il problema, è possibile aprire un caso di supporto. Quando si esegue questa operazione, selezionare il prodotto corretto e l'argomento del supporto tecnico, in modo da coinvolgere il team di supporto di Azure VM Image Builder.

Selezione del prodotto case:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Panoramica di Azure Image Builder](image-builder-overview.md).
