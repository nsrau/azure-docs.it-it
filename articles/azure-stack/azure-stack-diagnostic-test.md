---
title: Eseguire un test di convalida nello Stack di Azure | Documenti Microsoft
description: Come raccogliere i file di log di diagnostica in Azure Stack
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: 4f86397d4db5a0e67b294befd92087166d6b8109
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>Eseguire un test di convalida per lo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*
 
È possibile convalidare lo stato dello Stack di Azure. Quando si verifica un problema, contattare il supporto tecnico di servizi Microsoft. Supporto viene chiesto di eseguire Test AzureStack dal nodo di gestione. Il test di convalida consente di isolare il problema. Supporto può quindi analizzare i log dettagliati, lo stato attivo sull'area in cui si è verificato l'errore e di lavoro con la risoluzione del problema.

## <a name="run-test-azurestack"></a>Run Test-AzureStack

Quando si verifica un problema, contattare il supporto tecnico di servizi Microsoft e quindi eseguire **eseguire Test-AzureStack**.

1. Si verifica un problema.
2. Contattare il Microsoft servizi di supporto.
3. Eseguire **Test AzureStack** dal punto di fine con privilegi.
    1. Accedere all'endpoint con privilegi. Per istruzioni, vedere [utilizzando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md). 
    2. Accedere come **AzureStack\CloudAdmin** sull'host di gestione.
    3. Aprire PowerShell come amministratore.
    4. Eseguire: `Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. Eseguire: `Test-AzureStack`
4. Se i test hanno esito negativo del report, eseguire: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` il cmdlet raccoglie i log dal Test AzureStack. Per ulteriori informazioni sui log di diagnostica, vedere [gli strumenti di diagnostica Azure Stack](azure-stack-diagnostics.md).
5. Inviare il **SeedRing** registri di servizi di supporto tecnico clienti di Microsoft. Servizi di supporto tecnico di Microsoft funziona con è possibile risolvere il problema.

## <a name="reference-for-test-azurestack"></a>Riferimento per i Test AzureStack

In questa sezione contiene una panoramica per il cmdlet Test-AzureStack e un riepilogo del report di convalida.

### <a name="test-azurestack"></a>Test-AzureStack

Convalida lo stato dello Stack di Azure. Il cmdlet riportato lo stato di Azure Stack hardware e software. Il personale di supporto è possibile utilizzare questo report per ridurre il tempo necessario per risolvere i casi di supporto dello Stack di Azure.

> [!Note]  
> Test AzureStack potrebbe rilevare errori che non determinino interruzioni cloud, ad esempio non riuscita di un singolo disco o un errore di nodo singolo host fisico.

#### <a name="syntax"></a>Sintassi

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Parametri

| Parametro               | Valore           | Obbligatoria | Predefinito |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | No        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | No        | FALSE   |
| AdminCredential         | PSCredential    | No        | ND      |
<!-- | StorageConnectionString | string          | No        | ND      | non è supportato in 1802-->
| Elenco                    | SwitchParameter | No        | FALSE   |
| Ignora                  | string          | No        | ND      |
| Includi                 | string          | No        | ND      |

Il cmdlet Test-AzureStack supporta i parametri comuni: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable e OutVariable. Per ulteriori informazioni, vedere [sui parametri comuni](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Esempi di Test AzureStack

Gli esempi seguenti presuppongono è stato effettuato come **CloudAdmin** e l'accesso all'endpoint con privilegi (PEP). Per istruzioni, vedere [utilizzando l'endpoint con privilegi in Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Eseguire Test AzureStack in modo interattivo senza scenari basati su cloud

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Eseguire Test AzureStack con scenari basati su cloud

È possibile utilizzare Test AzureStack per eseguire gli scenari di cloud per lo Stack di Azure. Tali scenari includono:

 - Creazione di gruppi di risorse
 - Creazione dei piani
 - Creazione di offerte
 - Creazione di account di archiviazione
 - Creazione di una macchina virtuale
 - Eseguire operazioni blob utilizzando l'account di archiviazione creato in uno scenario di test
 - Eseguire operazioni di coda utilizzando l'account di archiviazione creato in uno scenario di test
 - Eseguire operazioni di tabella utilizzando l'account di archiviazione creato in uno scenario di test

Gli scenari di cloud richiedono credenziali di amministratore del cloud. 
> [!Note]  
> È possibile eseguire gli scenari di cloud utilizzando le credenziali di Active Directory Federated Services (ADFS). Il **Test AzureStack** cmdlet è accessibile solo tramite il PEP. Tuttavia, il PEP non supporta le credenziali ADFS.

Digitare il nome utente dell'amministratore cloud nel formato UPN serviceadmin@contoso.onmicrosoft.com (AAD). Quando richiesto, digitare la password per l'account amministratore cloud.

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Eseguire Test AzureStack senza scenari basati su cloud

In una sessione PEP, eseguire:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Elenco di scenari di test disponibili:

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Eseguire un test specificato

In una sessione PEP, eseguire:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Per escludere i test specifici:

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>Test di convalida

Nella tabella seguente sono riepilogati i test di convalida eseguiti da Test-AzureStack.

| NOME                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Riepilogo dell'infrastruttura di Hosting del Cloud Azure Stack                                                                                  |
| Riepilogo servizi di archiviazione Azure Stack                                                                                              |
| Riepilogo di istanza del ruolo infrastruttura Azure Stack                                                                                  |
| Utilizzo dell'infrastruttura di Hosting del Cloud Azure Stack                                                                              |
| Capacità di infrastruttura di Azure Stack                                                                                               |
| Riepilogo di API e portale di Azure Stack                                                                                                |
| Azure Stack Riepilogo certificato di gestione risorse di Azure                                                                                               |
| Controller di gestione dell'infrastruttura, controller di rete, servizi di archiviazione e ruoli di infrastruttura di endpoint con privilegi          |
| Controller di gestione dell'infrastruttura, controller di rete, servizi di archiviazione e le istanze del ruolo di infrastruttura di endpoint con privilegi |
| Riepilogo di Azure il ruolo di infrastruttura di Stack                                                                                           |
| Servizi di infrastruttura servizio Cloud Azure Stack                                                                                         |
| Prestazioni di istanza del ruolo infrastruttura Azure Stack                                                                              |
| Riepilogo prestazioni Host Cloud di Azure Stack                                                                                        |
| Riepilogo consumo di risorse del servizio Azure Stack                                                                                  |
| Azure Stack scala unità eventi critici (ultime 8 ore)                                                                             |
| Riepilogo dischi fisici servizi di archiviazione Azure Stack                                                                               |

## <a name="next-steps"></a>Passaggi successivi

 - Per ulteriori informazioni sugli strumenti di diagnostica Azure Stack e la registrazione di problema, vedere [ gli strumenti di diagnostica Azure Stack](azure-stack-diagnostics.md).
 - Per ulteriori informazioni sulla risoluzione dei problemi, vedere [la risoluzione dei problemi di Microsoft Azure Stack](azure-stack-troubleshooting.md)