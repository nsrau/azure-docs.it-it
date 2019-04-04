---
title: Riferimento ai cmdlet Start-AzsReadinessChecker | Microsoft Docs
description: Guida dei cmdlet PowerShell per il modulo di controllo di conformità di Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757775"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Riferimento ai cmdlet Start-AzsReadinessChecker

Modulo: **Microsoft.AzureStack.ReadinessChecker**

Questo modulo contiene solo un singolo cmdlet. Il cmdlet esegue uno o più funzioni di pre-distribuzione o pre-servizio per Azure Stack.

## <a name="syntax"></a>Sintassi

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>DESCRIZIONE

Il **Start-AzsReadinessChecker** cmdlet convalida i certificati, gli account di Azure, le sottoscrizioni di Azure e Azure Active Directory. Eseguire la convalida prima di distribuire Azure Stack oppure prima della manutenzione di azioni, ad esempio la rotazione segreta di Azure Stack. Il cmdlet è anche utilizzabile per generare le richieste di certificati di infrastruttura di firma del certificato e, facoltativamente, i certificati di PaaS. Infine, il cmdlet possibile riassemblare i certificati PFX per risolvere i problemi comuni di creazione dei pacchetti.

## <a name="examples"></a>Esempi

### <a name="example-generate-certificate-signing-request"></a>Esempio: generare una richiesta di firma del certificato

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

In questo esempio `Start-AzsReadinessChecker` genera più le richieste (CSR) di firma del certificato per certificati idonei per una distribuzione di AD FS Azure Stack con il nome area geografica **orientale** e un nome di dominio completo esterna di  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Esempio: la convalida dei certificati

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

In questo esempio, è necessaria per la sicurezza, la password PFX e `Start-AzsReadinessChecker` controlla la cartella relativa **certificati** per i certificati validi per una distribuzione di AAD con un nome di area del **orientale** e un oggetto nome di dominio completo esterna del **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Esempio: convalidare i certificati con i dati di distribuzione (distribuzione e supporto)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

In questo esempio di distribuzione e supporto, è necessaria per la sicurezza, la password PFX e `Start-AzsReadinessChecker` controlla la cartella relativa **certificati** per i certificati validi per una distribuzione in cui sono identità, area e nome di dominio completo esterna lettura dal file JSON di dati di distribuzione generato per la distribuzione.

### <a name="example-validate-paas-certificates"></a>Esempio: convalidare i certificati di PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omessi. `Start-AzsReadinessChecker` verifica che ogni percorso PFX esista e che la convalida usando l'area **orientale** e nome di dominio completo esterna **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Esempio: convalidare i certificati di PaaS con i dati della distribuzione

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omessi. `Start-AzsReadinessChecker` verifica che ogni percorso PFX esistente e la convalida usando l'area e nome di dominio completo esterna leggere dal file JSON di dati di distribuzione generato per la distribuzione.

### <a name="example-validate-azure-identity"></a>Esempio: convalidare l'identità di Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

In questo esempio, sono necessari per la sicurezza, le credenziali dell'account amministratore del servizio e `Start-AzsReadinessChecker` verifica che l'account di Azure e Azure Active Directory sono valide per una distribuzione di AAD con un nome tenant directory  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Esempio: convalidare l'identità di Azure con i dati della distribuzione (supporto per la distribuzione)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

In questo esempio, sono necessari per la sicurezza, le credenziali dell'account amministratore del servizio e `Start-AzsReadinessChecker` verifica che l'account di Azure e Azure Active Directory siano validi per una distribuzione di AAD, dove **AzureCloud** e**Nometenant** vengono lette dal file JSON di dati di distribuzione generato per la distribuzione.

### <a name="example-validate-azure-registration"></a>Esempio: convalidare la registrazione di Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

In questo esempio, sono necessari per la sicurezza, le credenziali del proprietario di sottoscrizione e `Start-AzsReadinessChecker` esegue quindi la convalida con l'account specificato e la sottoscrizione per assicurarsi possa essere utilizzato per la registrazione di Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Esempio: convalidare la registrazione di Azure con i dati della distribuzione (team di distribuzione)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

In questo esempio, sono necessari per la sicurezza, le credenziali del proprietario di sottoscrizione e `Start-AzsReadinessChecker` esegue quindi la convalida con l'account specificato e la sottoscrizione per assicurarsi che può essere utilizzato per la registrazione di Azure Stack, in cui dettagli aggiuntivi vengono letti dal file JSON di distribuzione dei dati generati per la distribuzione.

### <a name="example-importexport-pfx-package"></a>Esempio: pacchetto importazione/esportazione di file PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

In questo esempio, la password PFX è obbligatorio per la sicurezza. Il file Ssl.pfx viene importato nell'archivio certificati del computer locale, nuovamente esportato con la stessa password e salvato come Ssl_new.pfx. Questa procedura viene utilizzata quando la convalida dei certificati contrassegnato che una chiave privata non ha il **computer locale** set di attributi, viene interrotta la catena di certificati, certificati irrilevanti sono presenti nel file PFX o è la catena di certificati nell'ordine errato.

### <a name="example-view-validation-report-deployment-support"></a>Esempio: visualizzare il report di convalida (supporto per la distribuzione)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

In questo esempio, il team di supporto o distribuzione riceve il report di conformità dal cliente (Contoso) e utilizza `Start-AzsReadinessChecker` per visualizzare lo stato delle esecuzioni convalida eseguita Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Esempio: visualizzazione di report di convalida Riepilogo certificato convalida solo (distribuzione e supporto)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

In questo esempio, il team di supporto o distribuzione riceve il report di conformità dal cliente (Contoso) e utilizza `Start-AzsReadinessChecker` per visualizzare lo stato riassuntivo di esecuzioni di convalida il certificato Contoso eseguita.

## <a name="required-parameters"></a>Parametri obbligatori

### <a name="-regionname"></a>-RegionName

Specifica il nome dell'area di distribuzione Azure Stack.

|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

### <a name="-fqdn"></a>-FQDN

Specifica la distribuzione di Azure Stack FQDN esterno, anche un alias come **ExternalFQDN** e **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |ExternalFQDN, ExternalDomainName |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Specifica i Azure Stack deployment identità system valori validi, AAD o ADFS, per Azure Active Directory e Active Directory Federated Services, rispettivamente.

|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Valori validi:                |"AAD", "ADFS"  |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Specifica la password associata ai file di certificato PFX.

|  |  |
|----------------------------|---------|
|Digitare:                        |SecureString |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Specifica la tabella hash che contiene i percorsi e le password per i certificati di PaaS.

|  |  |
|----------------------------|---------|
|Digitare:                        |Tabella hash |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Specifica il file di configurazione di Azure Stack deployment dati JSON. Questo file viene generato per la distribuzione.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-pfxpath"></a>-PfxPath

Specifica il percorso di un certificato problematico che richiede una routine di importazione/esportazione per correggere, come indicato dalla convalida del certificato in questo strumento.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Specifica il percorso di destinazione per il file PFX risultante dalla routine importazione/esportazione.  

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-subject"></a>-Oggetto

Specifica un dizionario ordinato del soggetto per la generazione di richiesta di certificato.

|  |  |
|----------------------------|---------|
|Digitare:                        |OrderedDictionary   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-requesttype"></a>-RequestType

Specifica il tipo di SAN della richiesta di certificato. I valori validi sono **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** genera più le richieste di certificati, uno per ogni servizio.
- **SingleCSR** genera una richiesta di certificato per tutti i servizi.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'MultipleCSR','SingleCSR' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Specifica il percorso di destinazione per i file di richiesta di certificato. Directory deve esistere già.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Specifica l'amministratore del servizio Azure Active Directory da utilizzare per la distribuzione di Azure Stack.

|  |  |
|----------------------------|---------|
|Digitare:                        |PSCredential   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Specifica il nome di Azure Active Directory da utilizzare per la distribuzione di Azure Stack.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Specifica l'istanza di servizi di Azure che contiene l'account, le directory e le sottoscrizioni da usare per la registrazione e distribuzione di Azure Stack.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Specifica l'account di registrazione da utilizzare per la registrazione di Azure Stack.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Specifica l'ID sottoscrizione di registrazione da utilizzare per la registrazione di Azure Stack.

|  |  |
|----------------------------|---------|
|Digitare:                        |Guid     |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-reportpath"></a>-ReportPath

Specifica il percorso dei report di conformità, per impostazione predefinita al nome di report predefinito e directory corrente.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

## <a name="optional-parameters"></a>Parametri facoltativi

### <a name="-certificatepath"></a>-CertificatePath

Specifica il percorso in cui solo il certificato necessario sono presenti cartelle di certificato.

Le cartelle necessarie per la distribuzione di Azure Stack con il sistema di identità di Azure Active Directory sono:

ACSBlob, ACSQueue, ACSTable, portale di amministrazione, gli amministratori ARM ARM pubblico, Key Vault, KeyVaultInternal, portale pubblico

Necessario cartella per Azure Stack sono di distribuzione con il sistema di identità di Active Directory Federation Services:

ACSBlob, ACSQueue, ACSTable, ADFS, portale di amministrazione, amministrazione ARM, pubblico ARM, Graph, KeyVault, KeyVaultInternal, portale pubblico

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |. \Certificates |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Specifica se i nomi host/servizi PaaS devono essere aggiunto per le richieste di certificato.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

### <a name="-reportsections"></a>-ReportSections

Specifica se per mostrare solo informazioni di riepilogo del report vengono omessi dettaglio.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Valori validi:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

### <a name="-summary"></a>-Riepilogo

Specifica se per mostrare solo informazioni di riepilogo del report vengono omessi dettaglio.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

### <a name="-cleanreport"></a>-CleanReport

Rimuove la cronologia precedente di esecuzione e convalida e scrive le convalide di un nuovo report.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

### <a name="-outputpath"></a>-OutputPath

Specifica un percorso personalizzato per salvare i report di conformità di JSON e file di log dettagliato. Se il percorso non esiste già, il comando prova a creare la directory.

|  |  |
|----------------------------|------------------|
|Digitare:                        |string            |
|Posizione:                   |denominata             |
|Valore predefinito:              |$ENV: TEMP\AzsReadinessChecker  |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

### <a name="-confirm"></a>-Confirm

Richiede la conferma dell'utente prima di eseguire il cmdlet.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

### <a name="-whatif"></a>-WhatIf

Mostra l'esito in caso di esecuzione del cmdlet. Il cmdlet non viene eseguito.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |wi                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |
