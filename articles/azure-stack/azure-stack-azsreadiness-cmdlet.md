---
title: Riferimento ai cmdlet Start-AzsReadinessChecker | Documenti Microsoft
description: Guida dei cmdlet PowerShell per il modulo di controllo di conformità dello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 8481fbd6c7cb82b34070f9bc8cc6d7f3f4b2518c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Riferimento ai cmdlet Start-AzsReadinessChecker

Modulo: Microsoft.AzureStack.ReadinessChecker

Questo modulo contiene solo un singolo cmdlet.  Questo cmdlet esegue uno o più funzioni di pre-distribuzione o pre-manutenzione per lo Stack di Azure.

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
Il **inizio AzsReadinessChecker** cmdlet vengono convalidati i certificati, gli account di Azure, le sottoscrizioni di Azure e Azure Active Directory. Eseguire la convalida prima di distribuire Azure Stack o prima di azioni come segreto rotazione di manutenzione dello Stack di Azure. Il cmdlet anche utilizzabile per generare richieste di certificati di firma per i certificati di infrastruttura e, facoltativamente, i certificati di PaaS.  Infine, il cmdlet è possibile riassemblare i certificati PFX per risolvere i problemi più comuni di creazione del pacchetto.

## <a name="examples"></a>Esempi
**Esempio: Generazione di richiesta di firma del certificato**

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subjectName $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

In questo esempio, Start-AzsReadinessChecker genera più certificato di firma richieste (CSR) per certificati adatti per una distribuzione di ADFS Azure Stack con un nome di "east" regione e un FQDN esterno di "azurestack.contoso.com"

**Esempio: La convalida dei certificati**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

In questo esempio, la password PFX viene richiesto di immettere in modo sicuro e Start-AzsReadinessChecker controlla la cartella relativa "Certificati" per i certificati validi per una distribuzione di Azure ad con un nome dell'area di "east" e un FQDN esterno di "azurestack.contoso.com" 

**Esempio: Convalidare i certificati con i dati di distribuzione (distribuzione e supporto)**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```
In questo esempio di distribuzione e supporto tecnico, la password PFX viene richiesto di immettere in modo sicuro e Start-AzsReadinessChecker controlla la cartella relativa "Certificati" per i certificati validi per una distribuzione in cui identità, area e nome FQDN esterno vengono lette dal file di distribuzione dei dati JSON generato per la distribuzione. 

**Esempio: Convalidare i certificati PaaS**
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

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omesse. Inizio AzsReadinessChecker controlla ogni percorso di file PFX esista e convalida mediante l'area 'est' e nome FQDN esterno 'azurestack.contoso.com'.

**Esempio: Convalidare i certificati di PaaS con i dati di distribuzione**
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

In questo esempio, una tabella hash viene costruita con i percorsi e le password per ogni certificato PaaS. I certificati possono essere omesse. Inizio AzsReadinessChecker controlla ogni percorso di file PFX esiste, li convalida utilizzando l'area e nome FQDN esterno leggere dal file JSON di dati di distribuzione generato per la distribuzione. 

**Esempio: Convalidare l'identità di Azure**
```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment AzureCloud -AzureDirectoryTenantName azurestack.contoso.com
```

In questo esempio, vengono richieste le credenziali dell'account amministratore del servizio in modo sicuro e Start-AzsReadinessChecker controlla che l'account Azure e Azure Active Directory sono validi per una distribuzione di Azure ad con un nome di directory tenant di "azurestack.contoso.com"


**Esempio: Convalidare l'identità di Azure con i dati di distribuzione (supporto per la distribuzione)**
```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

In questo esempio, vengono richieste le credenziali dell'account amministratore del servizio in modo sicuro e Start-AzsReadinessChecker controlla che l'account Azure e Azure Active Directory sono validi per una distribuzione di Azure ad in cui vengono letti i cloud e TenantName dai dati di distribuzione File JSON generato per la distribuzione.


**Esempio: Convalidare una registrazione di Azure**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment AzureCloud
```

In questo esempio, vengono richieste le credenziali del proprietario della sottoscrizione in modo sicuro e Start-AzsReadinessChecker esegue quindi la convalida rispetto all'account specificato e sottoscrizione per assicurarsi che può essere utilizzata per la registrazione dello Stack di Azure. 


**Esempio: Convalidare una registrazione di Azure con i dati di distribuzione (del team di distribuzione)**
```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner"e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "f7c26209-cd2d-4625-86ba-724ebeece794"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

In questo esempio, vengono richieste le credenziali del proprietario della sottoscrizione in modo sicuro e Start-AzsReadinessChecker esegue quindi la convalida rispetto all'account specificato e sottoscrizione per assicurarsi che può essere utilizzata per la registrazione dello Stack di Azure in cui le informazioni aggiuntive sono lettura dal file JSON di dati di distribuzione generato per la distribuzione.

**Esempio: Pacchetto di importazione/esportazione PFX**
```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

In questo esempio, la password PFX viene richiesto di immettere in modo sicuro. Il file ssl.pfx verrà importato nell'archivio certificati del computer locale e nuovamente esportato con la stessa password e salvato come ssl_new.pfx.  Questa procedura viene utilizzato quando la convalida dei certificati contrassegnato che una chiave privata non dispone il set di attributi del computer locale, viene interrotta la catena di certificati, sono presenti nel file PFX irrilevanti certificati o la catena di certificati è nell'ordine errato.


**Esempio: Visualizzare il rapporto di convalida (supporto per la distribuzione)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

In questo esempio, il team di distribuzione o al supporto riceve il report di conformità dal cliente (Contoso) e utilizza AzsReadinessChecker Start per visualizzare lo stato delle esecuzioni convalida eseguita Contoso.

**Esempio: Visualizzare report di convalida del riepilogo per certificato convalida solo (distribuzione e supporto)**
```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

In questo esempio, il team di distribuzione o al supporto riceve il report di conformità del cliente di Contoso e utilizza AzsReadinessChecker Start per visualizzare lo stato riepilogativo le esecuzioni di convalida certificato che Contoso eseguita.



## <a name="required-parameters"></a>Parametri obbligatori
> -RegionName

Specifica nome dell'area di distribuzione dello Stack di Azure.
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Accettare input da pipeline:      |False         |
|Accettare caratteri jolly: |False         |

> -NOME DI DOMINIO COMPLETO    

Specifica FQDN esterno di distribuzione Azure Stack, anche con alias come ExternalFQDN ed ExternalDomainName.
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |ExternalFQDN, ExternalDomainName |
|Accettare input da pipeline:      |False         |
|Accettare caratteri jolly: |False         |

 

> -IdentitySystem    

Specifica sistema di identità i valori valida, AAD o ADFS, per Azure Active Directory e Active Directory Federated Services rispettivamente. della distribuzione di Azure Stack
|  |  |
|----------------------------|--------------|
|Digitare:                        |string        |
|Posizione:                   |denominata         |
|Valore predefinito:              |Nessuna          |
|Valori validi:                |'AAD', 'ADFS'  |
|Accettare input da pipeline:      |False         |
|Accettare caratteri jolly: |False         |

> -PfxPassword    

Specifica la password associata ai file di certificato PFX.
|  |  |
|----------------------------|---------|
|Digitare:                        |SecureString |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -PaaSCertificates

Specifica la tabella hash che contiene i percorsi e le password e certificati PaaS.
|  |  |
|----------------------------|---------|
|Digitare:                        |Tabella hash |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -DeploymentDataJSONPath

Specifica il file di configurazione di Azure Stack distribuzione dei dati JSON. Questo file viene generato per la distribuzione.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -PfxPath

Specifica il percorso di un certificato problematico che richiede l'importazione/esportazione di routine da correggere, come indicato dalla convalida del certificato in questo strumento.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -ExportPFXPath  

Specifica il percorso di destinazione per il file PFX risultante dalla routine di importazione/esportazione.  
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -Oggetto

Specifica un dizionario ordinato del soggetto per la generazione di richiesta di certificato.
|  |  |
|----------------------------|---------|
|Digitare:                        |OrderedDictionary   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> RequestType-

Specifica il tipo di SAN della richiesta di certificato. Valori validi MultipleCSR, SingleCSR.
- *MultipleCSR* genera l'errore più le richieste di certificato, una per ogni servizio.
- *SingleCSR* genera una richiesta di certificato per tutti i servizi.   

|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'MultipleCSR', 'SingleCSR' |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -OutputRequestPath

Specifica il percorso di destinazione per i file di richiesta di certificato directory deve esistere già.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -AADServiceAdministrator

Specifica l'amministratore del servizio Azure Active Directory da utilizzare per la distribuzione dello Stack di Azure.
|  |  |
|----------------------------|---------|
|Digitare:                        |PSCredential   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -AADDirectoryTenantName

Specifica il nome di Azure Active Directory da utilizzare per la distribuzione dello Stack di Azure.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -AzureEnvironment

Specifica l'istanza di servizi di Azure che contiene l'account, le directory e le sottoscrizioni da utilizzare per la registrazione e distribuzione di Azure Stack.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Valori validi:                |'Cloud', 'AzureChinaCloud', 'AzureGermanCloud' |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -RegistrationAccount

Specifica l'Account di registrazione da utilizzare per la registrazione dello Stack di Azure.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -RegistrationSubscriptionID

Specifica l'ID sottoscrizione di registrazione da utilizzare per la registrazione dello Stack di Azure.
|  |  |
|----------------------------|---------|
|Digitare:                        |Guid     |
|Posizione:                   |denominata    |
|Valore predefinito:              |Nessuna     |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |

> -ReportPath

Specifica percorso di Report di conformità, al nome di report predefinito e directory corrente.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |



## <a name="optional-parameters"></a>Parametri facoltativi
> -CertificatePath     

Specifica il percorso in cui solo il certificato necessario cartelle certificato sono presenti.

Le cartelle necessarie per la distribuzione di Azure Stack con il sistema di identità di Azure Active Directory sono:

ACSBlob, ACSQueue, ACSTable, portale di amministrazione, Admin ARM, ARM pubblico, KeyVault, KeyVaultInternal, portale pubblico

Richiesto cartella per lo Stack di Azure sono di distribuzione con il sistema di identità di Active Directory Federation Services:

ACSBlob, ACSQueue, ACSTable, ADFS, portale di amministrazione, amministrazione ARM, ARM pubblico, grafico, KeyVault, KeyVaultInternal, portale pubblico


|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |. \Certificates |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |


> -IncludePaaS  

Specifica se i servizi PaaS/nomi host devono essere aggiunte per le richieste di certificato.


|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |


> -ReportSections        

Specifica se per mostrare solo informazioni di riepilogo del report omette dettaglio.
|  |  |
|----------------------------|---------|
|Digitare:                        |string   |
|Posizione:                   |denominata    |
|Valore predefinito:              |Tutti      |
|Valori validi:                |'Certificate', 'AzureRegistration', 'AzureIdentity', 'Processi', 'Tutte' |
|Accettare input da pipeline:      |False    |
|Accettare caratteri jolly: |False    |


> -Summary 

Specifica se per mostrare solo informazioni di riepilogo del report omette dettaglio.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |


> -CleanReport  

Rimuove precedente della cronologia di esecuzione e convalida e scrive le convalide di un nuovo report.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |CF                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |


> -OutputPath    

Specifica il percorso personalizzato per salvare i report di conformità JSON e file di log dettagliato.  Se il percorso non esiste già, lo strumento tenterà di creare la directory.
|  |  |
|----------------------------|------------------|
|Digitare:                        |string            |
|Posizione:                   |denominata             |
|Valore predefinito:              |$ENV: TEMP\AzsReadinessChecker  |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |


> -Confirm  

Richiede una conferma prima di eseguire il cmdlet.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |CF                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |


> -WhatIf  

Viene illustrato che cosa accadrebbe se viene eseguito il cmdlet. Il cmdlet non è stato eseguito.
|  |  |
|----------------------------|------------------|
|Digitare:                        |SwitchParameter   |
|Alias:                    |Wi                |
|Posizione:                   |denominata             |
|Valore predefinito:              |False             |
|Accettare input da pipeline:      |False             |
|Accettare caratteri jolly: |False             |

 
