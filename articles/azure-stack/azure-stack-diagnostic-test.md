---
title: Eseguire un test di convalida in Azure Stack | Microsoft Docs
description: Come raccogliere i file di log per la diagnostica in Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 10/15/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 3f4dc6e4136d8d2e3eb1ca5e822306aae2217e3b
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340852"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Eseguire un test di convalida per Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*
 
È possibile convalidare lo stato di Azure Stack. Quando si verifica un problema, contattare il supporto di Microsoft Customer Services. Il supporto viene chiesto di eseguire **Test-AzureStack** dal nodo di gestione. Il test di convalida consente di isolare il problema. Supporto può quindi analizzare i log dettagliati, concentrarsi sull'area in cui si è verificato l'errore e di lavoro con l'utente la risoluzione del problema.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Quando si verifica un problema, contattare il supporto di Microsoft Customer Services e quindi eseguire **eseguire Test-AzureStack**.

1. Si verifica un problema.
2. Contatto cliente Microsoft servizi di supporto.
3. Eseguire **Test-AzureStack** dall'endpoint con privilegi.
    1. Accedere all'endpoint con privilegi. Per istruzioni, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Nel ASDK, accedere all'host di gestione come **AzureStack\CloudAdmin**.  
    In un sistema integrato, sarà necessario usare l'indirizzo IP per il punto di fine con privilegi per la gestione fornito dal fornitore dell'hardware OEM.
    3. Aprire PowerShell come amministratore.
    4. Eseguire: `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Eseguire: `Test-AzureStack`
4. Se i test di report hanno esito negativo, eseguire: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` il cmdlet raccoglie i log dai Test AzureStack. Per altre informazioni sui log di diagnostica, vedere [strumenti di diagnostica di Azure Stack](azure-stack-diagnostics.md).
5. Inviare il **SeedRing** i log al supporto tecnico di Microsoft Customer Services. Supporto di Microsoft Customer Services funziona con gli utenti per risolvere il problema.

## <a name="reference-for-test-azurestack"></a>Informazioni di riferimento per Test-AzureStack

In questa sezione contiene una panoramica per il cmdlet Test-AzureStack e un riepilogo del report di convalida.

### <a name="test-azurestack"></a>Test-AzureStack

Convalida lo stato di Azure Stack. Il cmdlet segnala lo stato dell'hardware Azure Stack e software. Personale tecnico IT possono usare questo report per ridurre il tempo necessario per risolvere i casi di supporto tecnico di Azure Stack.

> [!Note]  
> **Test-AzureStack** può rilevare gli errori che non determinino interruzioni del cloud, come non riuscita di un singolo disco o un errore in un nodo singolo host fisico.

#### <a name="syntax"></a>Sintassi

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametri

| Parametro               | Valore           | Obbligatorio | Predefinito |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | string    | No        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | No        | FALSE   |
| AdminCredential         | PSCredential    | No        | ND      |
| Elenco                    | SwitchParameter | No        | FALSE   |
| Ignora                  | string          | No        | ND      |
| Includi                 | string          | No        | ND      |
| BackupSharePath         | string          | No        | ND      |
| BackupShareCredential   | PSCredential    | No        | ND      |


Il cmdlet Test-AzureStack supporta i parametri comuni: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Per altre informazioni, vedere [sui parametri comuni](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Esempi di Test-AzureStack

Gli esempi seguenti presuppongono che si è connessi come **CloudAdmin** e accesso all'endpoint con privilegi (PEP). Per istruzioni, vedere [usando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Eseguire Test-AzureStack in modo interattivo senza scenari basati su cloud

In una sessione PEP, eseguire:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Eseguire Test-AzureStack con scenari basati su cloud

È possibile usare **Test-AzureStack** a eseguire scenari di cloud di Azure Stack. Tali scenari includono:

 - Creazione di gruppi di risorse
 - Creazione di piani
 - Creazione di offerte
 - Creazione di account di archiviazione
 - Creazione di una macchina virtuale
 - Eseguire operazioni su blob con l'account di archiviazione creato nello scenario del test
 - Eseguire operazioni di coda con l'account di archiviazione creato nello scenario del test
 - Eseguire operazioni di tabella con l'account di archiviazione creato nello scenario del test

Gli scenari di cloud richiedono credenziali di amministratore cloud. 
> [!Note]  
> Non è possibile eseguire gli scenari di cloud usando le credenziali di Active Directory Federated Services (ADFS). Il **Test-AzureStack** cmdlet è accessibile solo tramite il PEP. Tuttavia, il PEP non supporta credenziali di AD FS.

Digitare il nome utente amministratore del cloud nel formato UPN serviceadmin@contoso.onmicrosoft.com (Azure AD). Quando richiesto, digitare la password per l'account amministratore cloud.

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Eseguire Test-AzureStack senza scenari basati su cloud

In una sessione PEP, eseguire:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Elenco di scenari di test disponibili:

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Eseguire un test specificato

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Per escludere i test specifici:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Eseguire Test-AzureStack per testare le impostazioni di backup di infrastruttura

Prima di configurare il backup dell'infrastruttura, è possibile testare il percorso di condivisione di backup e di credenziali usando il **AzsBackupShareAccessibility** di test.

In una sessione PEP, eseguire:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
Dopo aver configurato il backup, è possibile eseguire AzsBackupShareAccessibility per convalidare la condivisione è accessibile da ERCS, da una sessione PEP eseguire:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Per testare le nuove credenziali con la condivisione di backup configurata da una sessione PEP eseguire:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Test di convalida

La tabella seguente riepiloga i test di convalida eseguiti dal **Test-AzureStack**.

| NOME                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Riepilogo dell'infrastruttura di Hosting nel Cloud Azure Stack                                                                                  |
| Riepilogo servizi archiviazione di Azure Stack                                                                                              |
| Riepilogo istanze ruolo infrastruttura di Azure Stack                                                                                  |
| Utilizzo dell'infrastruttura di Hosting nel Cloud Azure Stack                                                                              |
| Capacità di infrastruttura di Azure Stack                                                                                               |
| Riepilogo delle API e portale di Azure Stack                                                                                                |
| Azure Stack riepilogo di certificati di gestione risorse di Azure                                                                                               |
| Controller di gestione dell'infrastruttura, controller di rete, servizi di archiviazione e ruoli di infrastruttura di endpoint con privilegi          |
| Controller di gestione dell'infrastruttura, controller di rete, servizi di archiviazione e le istanze del ruolo di infrastruttura di endpoint con privilegi |
| Riepilogo del ruolo di infrastruttura Stack Azure                                                                                           |
| Servizi di Azure Stack Cloud Service Fabric                                                                                         |
| Prestazioni le istanze di ruolo dell'infrastruttura di Azure Stack                                                                              |
| Riepilogo delle prestazioni Host Cloud di Azure Stack                                                                                        |
| Riepilogo consumo di risorse del servizio Azure Stack                                                                                  |
| Azure Stack scalabilità unità eventi critici (ultime 8 ore)                                                                             |
| Riepilogo dischi fisici servizi archiviazione di Azure Stack                                                                               |
|Riepilogo di accessibilità condivisione di Backup di Azure Stack                                                                                     |

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni su strumenti di diagnostica di Azure Stack e la registrazione di problema, vedere [ strumenti di diagnostica di Azure Stack](azure-stack-diagnostics.md).
 - Per altre informazioni sulla risoluzione dei problemi, vedere [la risoluzione dei problemi di Microsoft Azure Stack](azure-stack-troubleshooting.md)
