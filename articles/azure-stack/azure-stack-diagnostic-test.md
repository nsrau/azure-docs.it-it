---
title: Usare lo strumento di convalida di Azure Stack | Microsoft Docs
description: Come raccogliere i file di log per la diagnostica in Azure Stack.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 63a198b082c7486de2392153291a11be5bcb2f9e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103224"
---
# <a name="validate-azure-stack-system-state"></a>Convalidare lo stato del sistema Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Un operatore di Azure Stack, di avere la possibilità di conoscere l'integrità e lo stato del sistema on demand è essenziale. Lo strumento di convalida di Azure Stack (**Test-AzureStack**) è un cmdlet di PowerShell che consente di eseguire una serie di test nel sistema per identificare gli errori, se presente. Verrà in genere richiesto per eseguire questo strumento il [privileged punto finale (PEP)](azure-stack-privileged-endpoint.md) quando si contatta il supporto di servizi clienti Microsoft (CSS) con un problema. Con l'integrità di sistema e informazioni sullo stato in questione, CSS possa raccogliere e analizzare i log dettagliati, lo stato attivo sull'area in cui si è verificato l'errore e lavorare con te per risolvere il problema.

## <a name="running-the-validation-tool-and-accessing-results"></a>Eseguire lo strumento di convalida e l'accesso ai risultati

Come affermato in precedenza, lo strumento di convalida viene eseguito tramite il PEP. Ogni test viene restituita una **esitopositivoonegativo** lo stato nella finestra di PowerShell. Inoltre, viene creato un report HTML dettagliato in un secondo momento che è possibile accedere durante [raccolta di log](azure-stack-diagnostics.md). Ecco una descrizione del processo di test di convalida di end-to-end: 

1. Accedere all'endpoint con privilegi (PEP). Eseguire i comandi seguenti per stabilire una sessione PEP:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Per accedere la PEP in un computer host ASDK, usare AzS-ERCS01 per - ComputerName.

2. Dopo aver avviato la PEP, eseguire: 

   ```powershell
   Test-AzureStack
   ```

   Vedere le [considerazioni sui parametri](azure-stack-diagnostic-test.md#parameter-considerations) e [esempi casi d'uso](azure-stack-diagnostic-test.md#use-case-examples) sezioni per ulteriori informazioni.

3. Se qualsiasi test rapporto **esito negativo**, eseguire:

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   Il cmdlet raccoglie i log generati dal Test AzureStack. Per altre informazioni sui log di diagnostica, vedere [strumenti di diagnostica di Azure Stack](azure-stack-diagnostics.md). Non si deve raccogliere i log o contattare il servizio se i test di report **WARN**.

4. Se è stato indicato di eseguire lo strumento di convalida dal foglio di stile CSS, al rappresentante CSS richiederà i log che raccolti per continuare la risoluzione del problema.

## <a name="tests-available"></a>Test disponibili

Lo strumento di convalida consente di eseguire una serie di test a livello di sistema e gli scenari di base relativi al cloud che offrono corrente di un'informazione sullo stato e verificare i problemi nel sistema.

### <a name="cloud-infrastructure-tests"></a>Test dell'infrastruttura cloud

Questi test a basso impatto a livello di infrastruttura di lavoro e forniscono informazioni sui componenti di sistema e funzioni. Attualmente, i test sono raggruppati nelle categorie seguenti:

| Categoria di test                                        | Argomento per - includere e - ignorare |
| :--------------------------------------------------- | :-------------------------------- |
| Riepilogo avvisi di Azure Stack                            | AzsAlertSummary                   |
| Riepilogo di accessibilità condivisione di Backup di Azure Stack       | AzsBackupShareAccessibility       |
| Riepilogo del piano di controllo Azure Stack                    | AzsControlPlane                   |
| Riepilogo di Defender Azure Stack                         | AzsDefenderSummary                |
| Azure Stack riepilogo Firmware dell'infrastruttura di Hosting  | AzsHostingInfraFWSummary          |
| Riepilogo dell'infrastruttura di Hosting nel Cloud Azure Stack     | AzsHostingInfraSummary            |
| Utilizzo dell'infrastruttura di Hosting nel Cloud Azure Stack | AzsHostingInfraUtilization        |
| Capacità di infrastruttura di Azure Stack                  | AzsInfraCapacity                  |
| Prestazioni dell'infrastruttura di Azure Stack               | AzsInfraPerformance               |
| Riepilogo del ruolo dell'infrastruttura Azure Stack              | AzsInfraRoleSummary               |
| Riepilogo aggiornamenti di Azure Stack                           | AzsInfraUpdateSummary             |
| Riepilogo delle API e portale di Azure Stack                   | AzsPortalAPISummary               |
| Eventi della macchina virtuale di Azure Stack scalabilità Unit                     | AzsScaleUnitEvents                |
| Risorse delle macchine Virtuali di unità di scalabilità di Azure Stack                  | AzsScaleUnitResources             |
| Riepilogo di convalida SDN di Azure Stack                   | AzsSDNValidation                  |
| Riepilogo del ruolo dello Stack di Azure Service Fabric              | AzsSFRoleSummary                  |
| Riepilogo BMC Azure Stack                              | AzsStampBMCSummary                |
| Riepilogo servizi archiviazione di Azure Stack                 | AzsStorageSvcsSummary             |
| Riepilogo Store di SQL di Azure Stack                        | AzsStoreSummary                   |
| Riepilogo di selezione host della macchina virtuale di Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Test dello scenario di cloud

Oltre ai test infrastruttura precedente, è la possibilità di eseguire i test di scenario di cloud di verifica della funzionalità tra i componenti dell'infrastruttura. Per eseguire i test perché comportano la distribuzione di risorse sono necessarie credenziali di amministratore di cloud. 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

I seguenti scenari di cloud sono testati tramite lo strumento di convalida:
- Creazione del gruppo di risorse   
- Creazione del piano              
- Creazione dell'offerta            
- Creazione di account di archiviazione   
- Creazione della macchina virtuale 
- Operazione di archiviazione BLOB   
- Operazione di archiviazione di coda  
- Operazione di archiviazione tabelle  

## <a name="parameter-considerations"></a>Considerazioni sui parametri

- Il parametro **elenco** può essere utilizzato per visualizzare tutte le categorie di test disponibili.

- I parametri **inclusione** e **Ignore** può essere utilizzato per includere o escludere le categorie di test. Vedere le [test disponibili](azure-stack-diagnostic-test.md#tests-available) sezione per ulteriori informazioni sulla sintassi abbreviata per l'uso con questi argomenti.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Un tenant di che macchina virtuale viene distribuita come parte di una verifica lo scenario di cloud. È possibile usare **DoNotDeployTenantVm** per disabilitare questa opzione. 

- È necessario fornire il **ServiceAdminCredential** parametro per eseguire i test di scenario di cloud, come descritto nel [esempi casi d'uso](azure-stack-diagnostic-test.md#use-case-examples) sezione.

- **BackupSharePath** e **BackupShareCredential** vengono utilizzati quando le impostazioni di backup dell'infrastruttura di test come illustrato nella [esempi casi d'uso](azure-stack-diagnostic-test.md#use-case-examples) sezione.

- Lo strumento di convalida inoltre supporta parametri PowerShell comuni: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Per altre informazioni, vedere [sui parametri comuni](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Esempi di casi di utilizzo

### <a name="run-validation-without-cloud-scenarios"></a>Eseguire la convalida senza scenari basati su cloud

Eseguire lo strumento di convalida senza il **ServiceAdminCredential** parametro per ignorare i test di scenario cloud in esecuzione: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Eseguire la convalida in scenari basati su cloud

Fornisce lo strumento di convalida con il **ServiceAdminCredentials** parametro consente di eseguire il test dello scenario di cloud per impostazione predefinita: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Se si vuole eseguire solo a scenari basati su cloud senza eseguire il resto dei test, è possibile usare la **inclusione** parametro a tale scopo: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Il nome utente dell'amministratore cloud deve essere digitato in formato UPN: serviceadmin@contoso.onmicrosoft.com (Azure AD). Quando richiesto, digitare la password per l'account amministratore cloud.

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>Eseguire lo strumento di convalida per eseguire il test di conformità del sistema prima di installare l'aggiornamento o hotfix

Prima di iniziare l'installazione di un aggiornamento o hotfix, è necessario eseguire lo strumento di convalida per verificare lo stato di Azure Stack:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Eseguire lo strumento di convalida per verificare le impostazioni di backup di infrastruttura

*Prima di* configurazione del backup dell'infrastruttura, è possibile testare il percorso di condivisione di backup e di credenziali usando il **AzsBackupShareAccessibility** test: 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*Dopo aver* configurazione del backup, è possibile eseguire **AzsBackupShareAccessibility** per convalidare la condivisione è accessibile dal ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Per testare le nuove credenziali con la condivisione di backup configurata, eseguire: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su strumenti di diagnostica di Azure Stack e la registrazione di problema, vedere [strumenti di diagnostica di Azure Stack](azure-stack-diagnostics.md).

Per altre informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi di Microsoft Azure Stack](azure-stack-troubleshooting.md).