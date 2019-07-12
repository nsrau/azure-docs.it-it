---
title: Windows Desktop virtuale tenant e l'host la creazione del pool - Azure
description: Come pool di tenant e l'host risolvere i problemi durante l'installazione di un ambiente di tenant di Desktop virtuale Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 7ec4e0ffd87c0ef73a551416d8a8cc672f095483
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786721"
---
# <a name="tenant-and-host-pool-creation"></a>Creazione di pool di host e tenant

Questo articolo illustra i problemi durante l'installazione iniziale del tenant di Desktop virtuale Windows e l'infrastruttura di pool di host sessione correlato.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>L'acquisizione dell'immagine di Windows 10 Enterprise multisessione

Per usare l'immagine di Windows 10 Enterprise multisessione, passare ad Azure Marketplace, selezionare **iniziare** > **Microsoft Windows 10** > e [Windows 10 Enterprise per Anteprima di desktop virtuali, versione 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Screenshot della selezione di Windows 10 Enterprise per l'anteprima di desktop virtuale 1809 versione.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Creazione di tenant di Desktop virtuale Windows

Questa sezione descrive i potenziali problemi durante la creazione del tenant di Desktop virtuale Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Errore: L'utente non è autorizzato a eseguire query sul servizio di gestione

![Finestra di screenshot di PowerShell in cui un utente non è autorizzato a eseguire query sul servizio di gestione.](media/UserNotAuthorizedNewTenant.png)

Esempio di errore non elaborato:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** L'utente che ha eseguito l'accesso non è stato assegnato il ruolo TenantCreator in Azure Active Directory.

**Fix:** Seguire le istruzioni in [assegnare il ruolo applicazione TenantCreator a un utente nel tenant di Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Dopo aver seguito le istruzioni, sarà necessario un utente assegnato al ruolo TenantCreator.

![Schermata di TenantCreator ruolo assegnato.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Creazione della sessione di Desktop virtuale Windows di ospitare le macchine virtuali

Host sessione macchine virtuali possono essere creati in diversi modi, ma i team di Remote Desktop Services/Windows Virtual Desktop supportano solo i problemi relativi al modello di Azure Resource Manager di provisioning della macchina virtuale. Il modello di Azure Resource Manager è disponibile nel [Azure Marketplace](https://azuremarketplace.microsoft.com/) e [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemi usando Desktop virtuale di Windows: eseguire il provisioning di un'offerta di Azure Marketplace di pool di host

Desktop virtuali Windows: eseguire il provisioning di un modello di pool di host è disponibile da Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Errore: Quando si usa il collegamento da GitHub, il messaggio "creare un account gratuito" viene visualizzato

![Schermata per creare un account gratuito.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Non vi sono sottoscrizioni attive nell'account usato per accedere ad Azure o l'account utilizzato non dispone delle autorizzazioni per visualizzare le sottoscrizioni.

**Fix 1:** Accedere con un account con accesso come collaboratore (almeno) per la sottoscrizione in cui le macchine virtuali host di sessione sono dovrà essere distribuita.

**Causa 2:** La sottoscrizione in uso è parte di un tenant di Microsoft Cloud Service Provider (CSP).

**Fix 2:** Passare al percorso di GitHub per **crea ed effettuare il provisioning nuovo Desktop virtuale di Windows host pool** e seguire queste istruzioni:

1. Fare clic su **Deploy to Azure** e selezionare **copia indirizzo collegamento**.
2. Aprire **Notepad** e incollare il collegamento.
3. Prima del carattere #, inserire il nome del tenant dei clienti finali CSP.
4. Aprire il nuovo collegamento in un browser e il portale di Azure verrà caricato il modello.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modello di Azure Resource Manager e gli errori di PowerShell Desired State Configuration (DSC)

Seguire queste istruzioni per risolvere i problemi di distribuzioni non riuscite di modelli Azure Resource Manager e PowerShell DSC.

1. Esaminare gli errori di distribuzione usando [visualizzare le operazioni di distribuzione con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Se non sono presenti errori nella distribuzione, esaminare gli errori nel log attività usando [visualizzare i log attività per controllare le azioni sulle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Una volta identificato l'errore, usare il messaggio di errore e le risorse nel [risolvere errori comuni durante la distribuzione di Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) per risolvere il problema.
4. Eliminare tutte le risorse create durante la distribuzione precedente e ripetere la distribuzione il modello di ripetizione dei tentativi.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Errore: La distribuzione non è riuscita...<hostname>/joindomain

![La schermata di distribuzione non è riuscita.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Esempio di errore non elaborato:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** Le credenziali specificate per l'aggiunta di macchine virtuali al dominio sono corrette.

**Fix 1:** Vedere l'errore "Credenziali non corrette" per le macchine virtuali non vengono aggiunti al dominio in [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Causa 2:** Nome di dominio non viene risolto.

**Fix 2:** Vedere l'errore "nome di dominio non viene risolto" per le macchine virtuali non vengono aggiunti al dominio in [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Errore: Failed...\Unauthorized la distribuzione

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** La sottoscrizione in uso è un tipo che non è possibile accedere alle funzionalità necessarie nell'area in cui il cliente sta tentando di distribuire. Sottoscrizioni MSDN, Free o Education, ad esempio, possono visualizzare questo errore.

**Fix:** Impostare il tipo di sottoscrizione o la regione che possono accedere le funzionalità necessarie.

### <a name="error-vmextensionprovisioningerror"></a>Errore: VMExtensionProvisioningError

![Schermata di Your distribuzione non è riuscita con stato di provisioning terminal non è riuscita.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Errore temporaneo con l'ambiente di Desktop virtuale Windows.

**Causa 2:** Errore temporaneo con connessione.

**Fix:** Confermare l'ambiente di Desktop virtuale Windows è integro eseguendo l'accesso tramite PowerShell. Completare la registrazione della macchina virtuale manualmente nel [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Errore: Il nome utente amministratore specificato non è consentito

![Screenshot della distribuzione non riuscita in cui un amministratore specificato non è consentito.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Esempio di errore non elaborato:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** Password specificata contiene le sottostringhe non consentite (admin, administrator, radice).

**Fix:** Aggiornare il nome utente o usare utenti diversi.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Errore: Macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione

![Screenshot dell'operazione di risorsa completata con lo stato di provisioning terminal in Your distribuzione non è riuscita.](media/49c4a1836a55d91cd65125cf227f411f.png)

Esempio di errore non elaborato:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** Estensione PowerShell DSC non è riuscito a ottenere l'accesso come amministratore nella macchina virtuale.

**Fix:** Confermare il nome utente e una password ha accesso come amministratore nella macchina virtuale ed eseguire nuovamente il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Errore: DeploymentFailed – configurazione DSC di PowerShell 'FirstSessionHost' completata con errori

![Screenshot della distribuzione avrà esito negativo configurazione DSC di PowerShell 'FirstSessionHost' completata con errori.](media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Causa:** Estensione PowerShell DSC non è riuscito a ottenere l'accesso come amministratore nella macchina virtuale.

**Fix:** Confermare il nome utente e la password forniti hanno accesso amministrativo nella macchina virtuale ed eseguire nuovamente il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed – InvalidResourceReference

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

**Causa:** Parte del nome del gruppo di risorse viene usato per determinate risorse viene create dal modello. A causa del nome corrispondente alle risorse esistenti, il modello può selezionare una risorsa esistente da un altro gruppo.

**Fix:** Quando si esegue il modello di Azure Resource Manager per distribuire le macchine virtuali host di sessione, rendere i primi due caratteri univoco per il nome di gruppo di risorse di sottoscrizione.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Errore: DeploymentFailed – InvalidResourceReference

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

**Causa:** Questo errore è perché l'interfaccia di rete creata con il modello di Azure Resource Manager ha già lo stesso nome di un'altra interfaccia di rete nella rete virtuale.

**Fix:** Usare un prefisso dell'host diversi.

### <a name="error-deploymentfailed--error-downloading"></a>Errore: DeploymentFailed: errore durante il download

Esempio di errore non elaborato:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Questo errore è dovuto a una route statica, una regola firewall o NSG blocca il download del file zip associato al modello di Azure Resource Manager.

**Fix:** Rimuovere il blocco route statica, una regola firewall o sicurezza di rete. Facoltativamente, aprire il file json del modello Azure Resource Manager in un editor di testo, eseguire il collegamento al file con estensione zip e scaricare la risorsa in un percorso consentito.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Errore: L'utente non è autorizzato a eseguire query sul servizio di gestione

Esempio di errore non elaborato:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** L'amministratore di tenant di Desktop virtuale di Windows specificato non dispone di un'assegnazione di ruolo validi.

**Fix:** L'utente che ha creato il tenant di Desktop virtuale di Windows deve eseguire l'accesso a Windows PowerShell di Desktop virtuale e assegnare l'utente tentata un'assegnazione di ruolo. Se si eseguono i parametri di modello di GitHub Azure Resource Manager, seguire queste istruzioni utilizzando i comandi di PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Errore: Utente richiede Azure multi-Factor Authentication (MFA)

![Screenshot della distribuzione non è riuscita a causa di mancanza di multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Esempio di errore non elaborato:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** L'amministratore di tenant di Desktop virtuale Windows specificata richiede Azure multi-Factor Authentication (MFA) per l'accesso.

**Fix:** Creare un'entità servizio e assegnare un ruolo per il tenant di Windows Desktop virtuale seguendo la procedura descritta in [esercitazione: Creare assegnazioni di ruolo e le entità servizio con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Dopo aver verificato che possa accedere al Desktop virtuale Windows con l'entità servizio, eseguire di nuovo l'offerta del Marketplace di Azure o il modello di GitHub Azure Resource Manager, a seconda del metodo in uso. Seguire le istruzioni seguenti per immettere i parametri corretti per il metodo.

Se si esegue l'offerta di Azure Marketplace, fornire i valori per i parametri seguenti autenticare correttamente per Windows Desktop virtuale:

- Tenant di Windows Desktop virtuale proprietario di servizi desktop remoto: Entità servizio
- ID applicazione: L'identificazione dell'applicazione della nuova entità servizio è stato creato
- Password/Conferma Password: Il segreto della password che è stato generato per l'entità servizio
- ID Tenant di Azure AD: L'ID Tenant di Azure AD dell'entità servizio è stato creato

Se si esegue il modello di GitHub Azure Resource Manager, specificare valori per i parametri seguenti autenticare correttamente per Windows Desktop virtuale:

- Il nome dell'entità utente amministratore di tenant. (UPN) o l'ID applicazione: L'identificazione dell'applicazione della nuova entità servizio è stato creato
- Password amministratore del tenant: Il segreto della password che è stato generato per l'entità servizio
- IsServicePrincipal: **true**
- AadTenantId: L'ID Tenant di Azure AD dell'entità servizio è stato creato

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi di Desktop virtuale Windows e le tracce di escalation dei blocchi, vedere [risoluzione dei problemi di supporto, commenti e suggerimenti e panoramica](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Windows Desktop virtuale, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi con le connessioni client di Desktop virtuale Windows, vedere [le connessioni client Desktop remoto](troubleshoot-client-connection.md).
- Per risolvere i problemi quando si usa PowerShell con Windows Desktop virtuale, vedere [Windows PowerShell di Desktop virtuale](troubleshoot-powershell.md).
- Per altre informazioni sul servizio di anteprima, vedere [ambiente di anteprima di Desktop Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi di distribuzioni di modelli di Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).