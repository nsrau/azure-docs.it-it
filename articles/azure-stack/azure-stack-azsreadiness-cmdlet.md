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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 63de39fa97850c1df72bdcd964ed670e4e8b0608
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767193"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Riferimento ai cmdlet Start-AzsReadinessChecker

Modulo: Microsoft.AzureStack.ReadinessChecker

Questo modulo contiene solo un singolo cmdlet.  Questo cmdlet esegue uno o più funzioni di pre-distribuzione o pre-servizio per Azure Stack.

## <a name="syntax"></a>Sintassi

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

Il **Start-AzsReadinessChecker** cmdlet convalida i certificati, gli account di Azure, le sottoscrizioni di Azure e Azure Active Directory. Eseguire la convalida prima di distribuire Azure Stack oppure prima della manutenzione di azioni come la rotazione di chiave privata di Azure Stack. Il cmdlet è anche utilizzabile per generare le richieste di certificati di firma di certificati di infrastruttura e, facoltativamente, i certificati di PaaS.  Infine, il cmdlet possibile riassemblare i certificati PFX per risolvere i problemi comuni di creazione dei pacchetti.

## <a name="examples"></a>Esempi

### <a name="example-generate-certificate-signing-request"></a>Esempio: Generare una richiesta di firma del certificato

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

In questo esempio, Start-AzsReadinessChecker genera più certificati le richieste di firma dei certificati adatti per una distribuzione di ad FS Azure Stack con un nome di "east" regione e un nome di dominio completo esterna di "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Esempio: La convalida dei certificati

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

In questo esempio, viene richiesta la password PFX in modo sicuro e avvio AzsReadinessChecker controlla la relativa cartella "Certificati" per i certificati validi per una distribuzione di AAD con un nome dell'area di "east" e un nome di dominio completo esterna di "azurestack.contoso.com"

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Esempio: Convalidare i certificati con i dati di distribuzione (distribuzione e supporto)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

In questo esempio di distribuzione e supporto, la password PFX viene richiesto in modo sicuro e avvio AzsReadinessChecker controlla la cartella relativa "Certificati" per i certificati validi per una distribuzione in cui vengono letti identità, area e nome di dominio completo esterna dal file di distribuzione dei dati JSON generato per la distribuzione. 

### <a name="example-validate-paas-certificates"></a>Esempio: Convalidare i certificati di PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omessi. Start-AzsReadinessChecker controlla ogni percorso di PFX esiste e convalida tramite l'area 'east' e nome di dominio completo esterna 'azurestack.contoso.com'.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Esempio: Convalidare i certificati di PaaS con i dati della distribuzione

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omessi. Start-AzsReadinessChecker controlla ogni percorso di PFX esista e che la convalida usando l'area e nome di dominio completo esterna leggere dal file JSON di dati di distribuzione generato per la distribuzione. 

### <a name="example-validate-azure-identity"></a>Esempio: Convalidare l'identità di Azure

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

In questo esempio, vengono richieste le credenziali dell'account amministratore del servizio in modo sicuro e avvio AzsReadinessChecker controlla che l'account di Azure e Azure Active Directory sono validi per una distribuzione di AAD con un nome di directory del tenant di "azurestack.contoso.com"

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Esempio: Convalidare l'identità di Azure con i dati della distribuzione (supporto per la distribuzione)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

In questo esempio, vengono richieste le credenziali dell'account amministratore del servizio in modo sicuro e avvio AzsReadinessChecker controlla che l'account di Azure e Azure Active Directory sono validi per la distribuzione di AAD in cui vengono letti AzureCloud e Nometenant dai dati di distribuzione File JSON generato per la distribuzione.

### <a name="example-validate-azure-registration"></a>Esempio: Convalidare la registrazione di Azure

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

In questo esempio, le credenziali del proprietario della sottoscrizione vengono chiesta in modo sicuro e avvio AzsReadinessChecker esegue quindi la convalida rispetto all'account specificato e sottoscrizione per assicurarsi che può essere utilizzata per la registrazione di Azure Stack. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Esempio: Convalidare la registrazione di Azure con i dati della distribuzione (team di distribuzione)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

In questo esempio, vengono richieste le credenziali del proprietario della sottoscrizione in modo sicuro e Start-AzsReadinessChecker esegue quindi la convalida rispetto all'account specificato e sottoscrizione per assicurarsi che può essere utilizzata per la registrazione di Azure Stack in cui i dettagli aggiuntivi lettura dal file JSON di dati di distribuzione generato per la distribuzione.

### <a name="example-importexport-pfx-package"></a>Esempio: Pacchetto di importazione/esportazione di PFX

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

In questo esempio, la password PFX viene chiesta in modo sicuro. Il file ssl.pfx verrà importato nell'archivio certificati del computer locale e nuovamente esportato con la stessa password e salvato come ssl_new.pfx.  Questa procedura viene utilizzato quando la convalida dei certificati contrassegnato che una chiave privata è privo di set di attributi del computer locale, viene interrotta la catena di certificati, certificati irrilevanti sono presenti nel file PFX o la catena di certificati è nell'ordine errato.

### <a name="example-view-validation-report-deployment-support"></a>Esempio: Visualizza rapporto di convalida (supporto per la distribuzione)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

In questo esempio, il team di supporto o distribuzione riceve il report di conformità da parte del cliente (Contoso) e utilizza Start-AzsReadinessChecker per visualizzare lo stato delle esecuzioni convalida eseguita Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Esempio: Report di convalida Visualizza riepilogo certificato convalida solo (distribuzione e supporto)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

In questo esempio, il team di supporto o distribuzione riceve il report di conformità da parte del cliente di Contoso e utilizza Start-AzsReadinessChecker per visualizzare lo stato riassuntivo di esecuzioni di convalida il certificato che Contoso eseguita.

## <a name="required-parameters"></a>Parametri obbligatori

> -RegionName

Specifica il nome di area di distribuzione Azure Stack.
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

> -FQDN

Specifica il FQDN esterno della distribuzione di Azure Stack, anche un alias come ExternalFQDN ed ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |ExternalFQDN, ExternalDomainName |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

> -IdentitySystem

Specifica sistema di identità i valori validi della distribuzione di Azure Stack, AAD o ADFS, rispettivamente per Azure Active Directory e Active Directory Federated Services.
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Valori validi:                |"AAD", "ADFS"  |
|Input pipeline accettato:      |False         |
|Accettare caratteri jolly: |False         |

> -PfxPassword

Specifica la password associata ai file di certificato PFX.
|  |  |
|----------------------------|---------|
|Digitare:                        |SecureString |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -PaaSCertificates

Specifica la tabella hash che contiene i percorsi e le password per i certificati di PaaS.
|  |  |
|----------------------------|---------|
|Digitare:                        |Tabella hash |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -DeploymentDataJSONPath

Specifica il file di configurazione di Azure Stack deployment dati JSON. Questo file viene generato per la distribuzione.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -PfxPath

Specifica il percorso di un certificato problematico che richiede l'importazione/esportazione di routine da risolvere, come indicato dalla convalida del certificato in questo strumento.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -ExportPFXPath  

Specifica il percorso di destinazione per il file PFX risultante dall'importazione/esportazione di routine.  
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -Oggetto

Specifica un dizionario ordinato del soggetto per la generazione di richiesta di certificato.
|  |  |
|----------------------------|---------|
|Digitare:                        |OrderedDictionary   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -RequestType

Specifica il tipo di SAN della richiesta di certificato. Valori validi MultipleCSR, SingleCSR.

- *MultipleCSR* genera più le richieste di certificati, uno per ogni servizio.
- *SingleCSR* genera una richiesta di certificato per tutti i servizi.

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'MultipleCSR','SingleCSR' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -OutputRequestPath

Specifica il percorso di destinazione per i file di richiesta di certificato, directory deve esistere già.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -AADServiceAdministrator

Specifica l'amministratore del servizio di Azure Active Directory da utilizzare per la distribuzione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |PSCredential   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -AADDirectoryTenantName

Specifica il nome di Azure Active Directory da utilizzare per la distribuzione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -AzureEnvironment

Specifica l'istanza di servizi di Azure che contiene l'account, le directory e le sottoscrizioni da usare per la registrazione e distribuzione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -RegistrationAccount

Specifica l'Account di registrazione da utilizzare per la registrazione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -RegistrationSubscriptionID

Specifica l'ID sottoscrizione di registrazione da utilizzare per la registrazione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |Guid     |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -ReportPath

Specifica percorso di Report di conformità, per impostazione predefinita al nome di report predefinito e directory corrente.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

## <a name="optional-parameters"></a>Parametri facoltativi

> -CertificatePath

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

> -IncludePaaS  

Specifica se i servizi PaaS o nomi host devono essere aggiunte per le richieste di certificato.

|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

> -ReportSections

Specifica se per mostrare solo informazioni di riepilogo del report vengono omessi dettaglio.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Valori validi:                |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Input pipeline accettato:      |False    |
|Accettare caratteri jolly: |False    |

> -Riepilogo

Specifica se per mostrare solo informazioni di riepilogo del report vengono omessi dettaglio.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

> -CleanReport

Rimuove la cronologia precedente di esecuzione e convalida e scrive le convalide di un nuovo report.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

> -OutputPath

Specifica un percorso personalizzato per salvare i report di conformità JSON e file di log dettagliato.  Se il percorso non esiste già, lo strumento tenterà di creare la directory.

|  |  |
|----------------------------|------------------|
|Digitare:                        |string            |
|Posizione:                   |denominata             |
|Valore predefinito:              |$ENV: TEMP\AzsReadinessChecker  |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

> -Confirm

Richiede la conferma dell'utente prima di eseguire il cmdlet.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |cf                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |

> -WhatIf

Mostra l'esito in caso di esecuzione del cmdlet. Il cmdlet non viene eseguito.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |wi                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Input pipeline accettato:      |False             |
|Accettare caratteri jolly: |False             |
