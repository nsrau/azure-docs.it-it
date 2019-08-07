---
title: Creazione di un tenant e di un pool host per desktop virtuale Windows-Azure
description: Come risolvere i problemi e risolvere i problemi del pool di tenant e host durante l'installazione di un ambiente tenant di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 7ab8ec9536af74102d2c9384ea3d0d0503f58f63
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816488"
---
# <a name="tenant-and-host-pool-creation"></a>Creazione di pool di host e tenant

In questo articolo vengono illustrati i problemi durante la configurazione iniziale del tenant di desktop virtuale Windows e l'infrastruttura del pool di host sessione correlata.

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

Al momento non vengono accettati casi di supporto mentre Desktop virtuale Windows è in anteprima. Visitare la pagina [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) per discutere del servizio Desktop virtuale Windows con il team del prodotto e i membri attivi della community.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Acquisizione dell'immagine multisessione Enterprise di Windows 10

Per usare l'immagine multisessione Enterprise di Windows 10, passare a Azure Marketplace, selezionare **Introduzione** > a**Microsoft Windows 10** > e [Windows 10 Enterprise per desktop virtuali (anteprima), versione 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Screenshot della selezione di Windows 10 Enterprise per desktop virtuali (anteprima), versione 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Creazione di un tenant desktop virtuale Windows

In questa sezione vengono illustrati i potenziali problemi durante la creazione del tenant desktop virtuale di Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Errore: L'utente non è autorizzato a eseguire una query sul servizio di gestione

![Screenshot della finestra di PowerShell in cui un utente non è autorizzato a eseguire query sul servizio di gestione.](media/UserNotAuthorizedNewTenant.png)

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

**Causa:** All'utente che ha eseguito l'accesso non è stato assegnato il ruolo TenantCreator nel Azure Active Directory.

**Difficoltà** Seguire le istruzioni riportate in [assegnare il ruolo applicazione TenantCreator a un utente nel tenant del Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Dopo aver seguito le istruzioni, si avrà un utente assegnato al ruolo TenantCreator.

![Screenshot del ruolo TenantCreator assegnato.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Creazione di VM host sessione desktop virtuale Windows

È possibile creare macchine virtuali host sessione in diversi modi, ma i team di desktop virtuali Servizi Desktop remoto/Windows supportano solo i problemi di provisioning delle VM correlati al modello di Azure Resource Manager. Il modello di Azure Resource Manager è disponibile in [Azure Marketplace](https://azuremarketplace.microsoft.com/) e in [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemi di utilizzo del desktop virtuale di Windows: effettuare il provisioning di un'offerta di Azure Marketplace

Il modello desktop virtuale Windows-provisioning di un pool di host è disponibile in Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Errore: Quando si usa il collegamento da GitHub, viene visualizzato il messaggio "crea un account gratuito"

![Screenshot per creare un account gratuito.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Non sono presenti sottoscrizioni attive nell'account usato per accedere ad Azure oppure l'account usato non dispone delle autorizzazioni per visualizzare le sottoscrizioni.

**Correzione 1:** Accedere con un account che disponga almeno dell'accesso collaboratore alla sottoscrizione in cui verranno distribuite le VM host sessione.

**Causa 2:** La sottoscrizione utilizzata fa parte di un tenant del provider di servizi di Microsoft Cloud (CSP).

**Fix 2:** Passare al percorso GitHub per creare ed effettuare il provisioning di un **nuovo pool di host per desktop virtuali Windows** e seguire queste istruzioni:

1. Fare clic con il pulsante destro del mouse su **Distribuisci in Azure** e scegliere **Copia indirizzo collegamento**.
2. Aprire il **blocco note** e incollare il collegamento.
3. Prima del carattere #, inserire il nome del tenant del cliente finale CSP.
4. Aprire il nuovo collegamento in un browser e il portale di Azure caricherà il modello.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Errori del modello di Azure Resource Manager e di PowerShell DSC (Desired state Configuration)

Seguire queste istruzioni per risolvere i problemi relativi alle distribuzioni non riuscite dei modelli di Azure Resource Manager e di PowerShell DSC.

1. Esaminare gli errori nella distribuzione usando [Visualizza operazioni di distribuzione con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Se non sono presenti errori nella distribuzione, esaminare gli errori nel log attività usando [Visualizza log attività per controllare le azioni sulle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Una volta identificato l'errore, usare il messaggio di errore e le risorse in [risolvere gli errori comuni di distribuzione di Azure con Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) per risolvere il problema.
4. Eliminare tutte le risorse create durante la distribuzione precedente e riprovare a distribuire il modello.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Errore: La distribuzione non è riuscita..\<. hostname >/JoinDomain

![Screenshot della distribuzione non riuscita.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Esempio di errore non elaborato:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** Le credenziali specificate per l'aggiunta di macchine virtuali al dominio non sono corrette.

**Correzione 1:** Vedere l'errore "credenziali non corrette" per le macchine virtuali non appartenenti al dominio nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).

**Causa 2:** Il nome di dominio non viene risolto.

**Fix 2:** Vedere l'errore "Impossibile risolvere il nome di dominio" per le macchine virtuali non appartenenti al dominio nella [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Errore: La distribuzione non è riuscita. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** La sottoscrizione in uso è un tipo che non può accedere alle funzionalità necessarie nell'area in cui il cliente sta provando a eseguire la distribuzione. Ad esempio, gli abbonamenti MSDN, Free o Education possono visualizzare questo errore.

**Difficoltà** Modificare il tipo di sottoscrizione o l'area geografica in modo da poter accedere alle funzionalità necessarie.

### <a name="error-vmextensionprovisioningerror"></a>Errore: VMExtensionProvisioningError

![Lo screenshot della distribuzione non è riuscito con lo stato di provisioning del terminale non riuscito.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Errore temporaneo con l'ambiente desktop virtuale di Windows.

**Causa 2:** Errore temporaneo con connessione.

**Difficoltà** Verificare che l'ambiente desktop virtuale Windows sia integro eseguendo l'accesso con PowerShell. Completare manualmente la registrazione della macchina virtuale in [creare un pool di host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Errore: Il nome utente amministratore specificato non è consentito

![Screenshot della distribuzione non riuscita. l'amministratore specificato non è consentito.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Esempio di errore non elaborato:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** La password specificata contiene sottostringhe non consentite (admin, Administrator, root).

**Difficoltà** Aggiornare il nome utente o usare utenti diversi.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Errore: La macchina virtuale ha segnalato un errore durante l'elaborazione dell'estensione

![Screenshot dell'operazione della risorsa completata con lo stato di provisioning terminal nella distribuzione non riuscita.](media/49c4a1836a55d91cd65125cf227f411f.png)

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

**Causa:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Difficoltà** Verificare che il nome utente e la password dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Errore: DeploymentFailed-la configurazione DSC di PowerShell ' FirstSessionHost ' è stata completata con errori

![Screenshot della distribuzione non riuscito con la configurazione DSC di PowerShell ' FirstSessionHost ' completata con errori.](media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Causa:** PowerShell DSC Extension non è stato in grado di ottenere l'accesso amministrativo alla macchina virtuale.

**Difficoltà** Verificare che il nome utente e la password specificati dispongano dell'accesso amministrativo alla macchina virtuale ed eseguire di nuovo il modello di Azure Resource Manager.

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

**Causa:** Parte del nome del gruppo di risorse viene usata per determinate risorse create dal modello. A causa del nome che corrisponde alle risorse esistenti, il modello può selezionare una risorsa esistente da un gruppo diverso.

**Difficoltà** Quando si esegue il modello di Azure Resource Manager per distribuire le VM host sessione, rendere i primi due caratteri univoci per il nome del gruppo di risorse di sottoscrizione.

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

**Causa:** Questo errore è dovuto al fatto che la scheda di interfaccia di rete creata con il modello di Azure Resource Manager ha lo stesso nome di un'altra NIC già presente in VNET.

**Difficoltà** Usare un prefisso host diverso.

### <a name="error-deploymentfailed--error-downloading"></a>Errore: DeploymentFailed – errore durante il download

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

**Causa:** Questo errore è dovuto a una route statica, a una regola del firewall o a un NSG che blocca il download del file zip associato al modello di Azure Resource Manager.

**Difficoltà** Rimuovere la route statica di blocco, la regola del firewall o NSG. Facoltativamente, aprire il file JSON del modello di Azure Resource Manager in un editor di testo, fare il collegamento al file zip e scaricare la risorsa in un percorso consentito.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Errore: L'utente non è autorizzato a eseguire una query sul servizio di gestione

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

**Causa:** L'amministratore del tenant del desktop virtuale Windows specificato non dispone di un'assegnazione di ruolo valida.

**Difficoltà** L'utente che ha creato il tenant desktop virtuale di Windows deve accedere a PowerShell per desktop virtuale Windows e assegnare all'utente tentata un'assegnazione di ruolo. Se si eseguono i parametri del modello di Azure Resource Manager GitHub, seguire queste istruzioni usando i comandi di PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Errore: L'utente richiede Azure multi-factor authentication

![Screenshot della distribuzione non riuscita a causa della mancanza di multi-factor authentication](media/MFARequiredError.png)

Esempio di errore non elaborato:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** L'amministratore del tenant del desktop virtuale Windows specificato richiede l'accesso a multi-factor authentication di Azure.

**Difficoltà** Creare un'entità servizio e assegnarle un ruolo per il tenant di desktop virtuale Windows seguendo la procedura descritta [nell'esercitazione: Creare entità servizio e assegnazioni di ruolo con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). Dopo aver verificato che sia possibile accedere a desktop virtuale Windows con l'entità servizio, eseguire di nuovo l'offerta di Azure Marketplace o il modello di Azure Resource Manager di GitHub, a seconda del metodo in uso. Seguire le istruzioni riportate di seguito per immettere i parametri corretti per il metodo.

Se si sta eseguendo l'offerta di Azure Marketplace, fornire i valori per i parametri seguenti per eseguire correttamente l'autenticazione a desktop virtuale di Windows:

- Proprietario Servizi Desktop remoto tenant di desktop virtuale Windows: Entità servizio
- ID applicazione: Identificazione dell'applicazione della nuova entità servizio creata
- Password/conferma password: Il segreto della password generato per l'entità servizio
- ID tenant Azure AD: ID del tenant di Azure AD dell'entità servizio creata

Se si esegue il modello di Azure Resource Manager di GitHub, fornire i valori per i seguenti parametri per l'autenticazione corretta per desktop virtuale di Windows:

- Nome dell'entità utente (UPN) dell'amministratore del tenant o ID applicazione: Identificazione dell'applicazione della nuova entità servizio creata
- Password amministratore tenant: Il segreto della password generato per l'entità servizio
- IsServicePrincipal: **true**
- AadTenantId: ID del tenant di Azure AD dell'entità servizio creata

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica sulla risoluzione dei problemi relativi a desktop virtuale Windows e alle tracce di escalation, vedere [panoramica sulla risoluzione dei problemi, commenti e suggerimenti e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in desktop virtuale di Windows, vedere [configurazione della macchina virtuale host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [Desktop remoto connessioni client](troubleshoot-client-connection.md).
- Per risolvere i problemi relativi all'uso di PowerShell con desktop virtuale di Windows, vedere [PowerShell per desktop virtuale di Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio di anteprima, vedere [ambiente Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi relativi alle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)distribuzioni gestione risorse modello.
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
