---
title: Creazione del pool host dell'ambiente desktop virtuale Windows-Azure
description: Come risolvere i problemi e risolvere i problemi relativi ai pool di tenant e host durante l'installazione di un ambiente desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65a61babe58e1cb9438262186a7f4cf37cb10a34
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612572"
---
# <a name="host-pool-creation"></a>Creazione del pool host

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questo articolo vengono illustrati i problemi durante la configurazione iniziale del tenant di desktop virtuale Windows e l'infrastruttura del pool di host sessione correlata.

## <a name="provide-feedback"></a>Inviare feedback

Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Acquisizione dell'immagine multisessione Enterprise di Windows 10

Per usare l'immagine multisessione Enterprise di Windows 10, passare a Azure Marketplace, selezionare **Introduzione** > a**Microsoft Windows 10** > e [Windows 10 Enterprise multisessione, versione 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemi relativi all'utilizzo del portale di Azure per creare pool host

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Errore: l'opzione "crea un account gratuito" viene visualizzata quando si accede al servizio

![Immagine che mostra la portale di Azure che Visualizza il messaggio "crea un account gratuito"](media/create-new-account.png)

**Causa**: non sono presenti sottoscrizioni attive nell'account con cui è stato effettuato l'accesso ad Azure oppure l'account non dispone delle autorizzazioni per visualizzare le sottoscrizioni. 

**Correzione**: accedere alla sottoscrizione in cui verranno distribuite le macchine virtuali (VM) host della sessione con un account che dispone almeno dell'accesso a livello di collaboratore.

### <a name="error-exceeding-quota-limit"></a>Errore: "superamento del limite di quota"

Se l'operazione supera il limite di quota, è possibile eseguire una delle operazioni seguenti: 

- Creare un nuovo pool di host con gli stessi parametri, ma un minor numero di VM e Core VM.

- Aprire il collegamento visualizzato nel campo statusMessage in un browser per inviare una richiesta di aumento della quota per la sottoscrizione di Azure per lo SKU di VM specificato.

## <a name="azure-resource-manager-template-errors"></a>Errori del modello di Azure Resource Manager

Seguire queste istruzioni per risolvere i problemi relativi alle distribuzioni non riuscite dei modelli di Azure Resource Manager e di PowerShell DSC.

1. Esaminare gli errori nella distribuzione usando [Visualizza operazioni di distribuzione con Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Se non sono presenti errori nella distribuzione, esaminare gli errori nel log attività usando [Visualizza log attività per controllare le azioni sulle risorse](../azure-resource-manager/resource-group-audit.md).
3. Una volta identificato l'errore, usare il messaggio di errore e le risorse in [risolvere gli errori comuni di distribuzione di Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) per risolvere il problema.
4. Eliminare tutte le risorse create durante la distribuzione precedente e riprovare a distribuire il modello.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Errore: la distribuzione non è riuscita..\<. hostname>/JoinDomain

![Screenshot della distribuzione non riuscita.](media/failure-joindomain.png)

Esempio di errore non elaborato:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Cause 1:** Le credenziali specificate per l'aggiunta di macchine virtuali al dominio non sono corrette.

**Correzione 1:** Vedere l'errore "credenziali non corrette" per le macchine virtuali non appartenenti al dominio nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Motivo 2:** Il nome di dominio non viene risolto.

**Correzione 2:** Vedere [errore: il nome di dominio non viene risolto](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Motivo 3:** La configurazione DNS della rete virtuale (VNET) è impostata sul **valore predefinito**.

Per risolvere il problema, eseguire le operazioni seguenti:

1. Aprire il portale di Azure e passare alla scheda **reti virtuali** .
2. Individuare il VNET, quindi selezionare **server DNS**.
3. Il menu server DNS verrà visualizzato sul lato destro dello schermo. Nel menu selezionare **personalizzato**.
4. Verificare che i server DNS elencati in personalizzato corrispondano al controller di dominio o al dominio Active Directory. Se il server DNS non è visibile, è possibile aggiungerlo immettendo il relativo valore nel campo **Aggiungi server DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Errore: la distribuzione non è riuscita. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Motivo:** La sottoscrizione in uso è un tipo che non può accedere alle funzionalità necessarie nell'area in cui il cliente sta provando a eseguire la distribuzione. Ad esempio, gli abbonamenti MSDN, Free o Education possono visualizzare questo errore.

**Correzione:** Modificare il tipo di sottoscrizione o l'area geografica in modo da poter accedere alle funzionalità necessarie.

### <a name="error-vmextensionprovisioningerror"></a>Errore: VMExtensionProvisioningError

![Lo screenshot della distribuzione non è riuscito con lo stato di provisioning del terminale non riuscito.](media/failure-vmextensionprovisioning.png)

**Cause 1:** Errore temporaneo con l'ambiente desktop virtuale di Windows.

**Motivo 2:** Errore temporaneo con connessione.

**Correzione:** Verificare che l'ambiente desktop virtuale Windows sia integro eseguendo l'accesso con PowerShell. Completare manualmente la registrazione della macchina virtuale in [creare un pool di host con PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Errore: il nome utente amministratore specificato non è consentito

![Screenshot della distribuzione non riuscita. l'amministratore specificato non è consentito.](media/failure-username.png)

Esempio di errore non elaborato:

```Error
 { …{ "provisioningOperation": 
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId": 
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message": 
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Motivo:** La password specificata contiene sottostringhe non consentite (admin, Administrator, root).

**Correzione:** Aggiornare il nome utente o usare utenti diversi.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Errore: la macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione

![Screenshot dell'operazione della risorsa completata con lo stato di provisioning terminal nella distribuzione non riuscita.](media/failure-processing.png)

Esempio di errore non elaborato:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code": 
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Motivo:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Correzione:** Verificare che il nome utente e la password dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Errore: DeploymentFailed-la configurazione DSC di PowerShell ' FirstSessionHost ' è stata completata con errori

![Screenshot della distribuzione non riuscito con la configurazione DSC di PowerShell ' FirstSessionHost ' completata con errori.](media/failure-dsc.png)

Esempio di errore non elaborato:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Motivo:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Correzione:** Verificare che il nome utente e la password specificati dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed-InvalidResourceReference

Esempio di errore non elaborato:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Motivo:** Parte del nome del gruppo di risorse viene usata per determinate risorse create dal modello. A causa del nome che corrisponde alle risorse esistenti, il modello può selezionare una risorsa esistente da un gruppo diverso.

**Correzione:** Quando si esegue il modello di Azure Resource Manager per distribuire le VM host sessione, rendere i primi due caratteri univoci per il nome del gruppo di risorse di sottoscrizione.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed-InvalidResourceReference

Esempio di errore non elaborato:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Motivo:** Questo errore è dovuto al fatto che la scheda di interfaccia di rete creata con il modello di Azure Resource Manager ha lo stesso nome di un'altra NIC già presente in VNET.

**Correzione:** Usare un prefisso host diverso.

### <a name="error-deploymentfailed--error-downloading"></a>Errore: DeploymentFailed – errore durante il download

Esempio di errore non elaborato:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Motivo:** Questo errore è dovuto a una route statica, a una regola del firewall o a un NSG che blocca il download del file zip associato al modello di Azure Resource Manager.

**Correzione:** Rimuovere la route statica di blocco, la regola del firewall o NSG. Facoltativamente, aprire il file JSON del modello di Azure Resource Manager in un editor di testo, fare il collegamento al file zip e scaricare la risorsa in un percorso consentito.

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [risoluzione dei problemi del client di desktop remoto](troubleshoot-client.md)
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per un'esercitazione per la risoluzione dei problemi, vedere [esercitazione: risolvere i problemi relativi alle distribuzioni di modelli gestione risorse](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../azure-resource-manager/templates/deployment-history.md).
