---
title: Guida alla risoluzione dei problemi di attestazione di Azure
description: Guida alla risoluzione dei problemi relativi ai problemi comunemente osservati
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 46e3521a54f6bfdfbfb25634a09b8c8e0cfdcac0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343107"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Guida alla risoluzione dei problemi di attestazione Microsoft Azure

La gestione degli errori nell'attestazione di Azure viene implementata seguendo le [linee guida dell'API REST Microsoft](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). La risposta di errore restituita dalle API di attestazione di Azure contiene il codice di stato HTTP e le coppie nome/valore con i nomi "code" e "message". Il valore di "code" è leggibile ed è un indicatore del tipo di errore. Il valore di "message" intende supportare l'utente e fornisce i dettagli dell'errore.

Se il problema non viene risolto in questo articolo, è anche possibile inviare una richiesta di supporto tecnico di Azure nella [pagina del supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Di seguito sono riportati alcuni esempi degli errori restituiti dall'attestazione di Azure:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: eccezione non autorizzata

### <a name="http-status-code"></a>Codice di stato HTTP
401

**Codice di errore** Autorizzato

**Esempi di scenario**
  - Errore di attestazione se l'utente non è assegnato al ruolo lettore di attestazione
  - Non è possibile gestire i criteri di attestazione perché l'utente non è assegnato ai ruoli appropriati
  - Non è possibile gestire i firmatari dei criteri di attestazione perché l'utente non è assegnato ai ruoli appropriati

Utente con ruolo lettore che tenta di modificare un criterio di attestazione in PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Passaggi per la risoluzione dei problemi**

Per visualizzare i criteri di attestazione/firmatari dei criteri, un utente Azure AD richiede l'autorizzazione per "azioni":
- Microsoft.Attestation/attestationProviders/attestation/read

  Questa autorizzazione può essere assegnata a un utente di Active Directory tramite un ruolo, ad esempio "proprietario" (autorizzazioni con caratteri jolly) o "Reader" (autorizzazioni con caratteri jolly) o "lettore di attestazione" (autorizzazioni specifiche solo per l'attestazione di Azure).

Per aggiungere o eliminare i firmatari dei criteri o per configurare i criteri, un utente Azure AD richiede le autorizzazioni seguenti per "azioni":
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Queste autorizzazioni possono essere assegnate a un utente di Active Directory tramite un ruolo, ad esempio "proprietario" (autorizzazioni con caratteri jolly), "collaboratore" (autorizzazioni con caratteri jolly) o "collaboratore attestazione" (autorizzazioni specifiche solo per l'attestazione di Azure).

I clienti possono scegliere di usare il provider predefinito per l'attestazione o di creare i propri provider con criteri personalizzati. Per inviare richieste di attestazione a provider di attestazione personalizzati, per l'utente è richiesto il ruolo "proprietario" (autorizzazioni con caratteri jolly) o "lettore" (autorizzazioni con caratteri jolly) o "lettore di attestazione". I provider predefiniti sono accessibili da qualsiasi utente Azure AD.

Per verificare i ruoli in PowerShell, eseguire di seguito:

a. Avviare PowerShell e accedere ad Azure tramite il cmdlet "Connect-AzAccount"

b. Verificare le impostazioni di assegnazione di ruolo RBAC


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  L'output dovrebbe essere simile al seguente:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Se nell'elenco non è presente un'assegnazione di ruolo appropriata, seguire le istruzioni riportate in [qui](/azure/role-based-access-control/role-assignments-powershell) .

## <a name="2-http--400-errors"></a>2. HTTP – 400 errori

### <a name="http-status-code"></a>Codice di stato HTTP
400

Esistono diversi motivi per cui una richiesta può restituire 400. Di seguito sono riportati alcuni esempi di errori restituiti dalle API di attestazione di Azure:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Errore di attestazione dovuto a errori di valutazione del criterio

I criteri di attestazione includono regole di autorizzazione e regole di rilascio. L'evidenza dell'enclave viene valutata in base alle regole di autorizzazione. Le regole di rilascio definiscono le attestazioni da includere nel token di attestazione. Se le attestazioni nell'evidenza enclave non sono conformi alle regole di autorizzazione, le chiamate di attestazione restituiranno un errore di valutazione dei criteri 

**Codice di errore** PolicyEvaluationError

**Esempi di scenario** Quando le attestazioni nella quota dell'enclave non corrispondono alle regole di autorizzazione dei criteri di attestazione

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Procedura per la risoluzione dei problemi** Gli utenti possono valutare l'evidenza dell'enclave rispetto a un criterio di attestazione SGX prima di configurare lo stesso.

Inviare una richiesta all'API di attestazione fornendo il testo del criterio nel parametro "draftPolicyForAttestation". L'API AttestSgxEnclave utilizzerà questo documento di criteri durante la chiamata di attestazione e può essere usato per testare i criteri di attestazione prima che vengano utilizzati. Il token di attestazione generato quando questo campo è presente non sarà protetto.

Vedere [esempi di criteri di attestazione](/azure/attestation/policy-examples)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Errore di attestazione dovuto a un input non valido

**Codice di errore** InvalidParameter

**Esempi di scenario** Errore di attestazione SGX dovuto a un input non valido. Di seguito sono riportati alcuni esempi per i messaggi di errore:
- Il preventivo specificato non è valido a causa di un errore nell'offerta di garanzia 
- Il preventivo specificato non è valido perché il dispositivo in cui è stata generata l'offerta non soddisfa i requisiti di base di Azure
- Il preventivo specificato non è valido perché il TCBInfo o QEID fornito dal servizio cache PCK non è valido

**Passaggi per la risoluzione dei problemi**

Microsoft Azure attestazione supporta l'attestazione di virgolette SGX generate da Intel SDK e Open enclave SDK.

Vedere gli [esempi di codice](/samples/browse/?expanded=azure&terms=attestation) per l'esecuzione dell'attestazione con Open enclave SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Errore di catena di certificati non valido durante il caricamento dei criteri/firmatario dei criteri

**Codice di errore** InvalidParameter

**Esempi di scenario** Configurare i criteri firmati o aggiungere/eliminare il firmatario del criterio, che è firmato con una catena di certificati non valida (ad esempio, quando l'estensione dei vincoli di base del certificato radice non è impostata su Subject Type = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Procedura per la risoluzione dei problemi** Il certificato radice deve essere contrassegnato come emesso da una CA (i vincoli di base X. 509); in caso contrario, non verrà considerato come un certificato valido. 

Verificare che l'estensione dei vincoli di base del certificato radice sia impostata in modo da indicare che tipo soggetto = CA

In caso contrario, la catena di certificati è considerata non valida.

Vedere gli esempi di [criteri](/azure/attestation/policy-examples) e [firmatari](/azure/attestation/policy-signer-examples) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Errore del firmatario del criterio di aggiunta/eliminazione

**Codice di errore** InvalidOperation

**Esempi di scenario**

Quando l'utente carica JWS senza attestazione "Maa-policyCertificate"

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Quando l'utente non carica un certificato in formato JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Procedura per la risoluzione dei problemi** Per aggiungere o eliminare un nuovo certificato del firmatario dei criteri, usare RFC7519 JSON Web Token (JWT) con un'attestazione denominata "x-ms-policyCertificate". Il valore dell'attestazione è una chiave Web JSON RFC7517 che contiene il certificato da aggiungere. JWT deve essere firmato con la chiave privata di uno dei certificati di firma dei criteri validi associati al provider. Vedere [esempi di firmatari dei criteri](/azure/attestation/policy-signer-examples).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Errore di configurazione dei criteri di attestazione

**Codice di errore** PolicyParsingError

**Esempi di scenario** Criterio fornito con sintassi non corretta (ad esempio, punto e virgola mancante)/valid JWT Policy)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Codice di errore** InvalidOperation

**Esempi di scenario** Fornito contenuto non valido (ad esempio, caricare criteri/criteri non firmati quando è richiesta la firma dei criteri)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Procedura per la risoluzione dei problemi** Verificare che il criterio in formato testo sia codificato con UTF-8.

Se è necessaria la firma dei criteri, i criteri di attestazione devono essere configurati solo nel formato JWT (RFC7519 JSON Web Token). Se la firma dei criteri non è necessaria, è possibile configurare i criteri in formato testo o JWT.

Per configurare i criteri in formato JWT, usare JWT con un'attestazione denominata "AttestationPolicy". Il valore dell'attestazione è la versione codificata Base64URL del testo del criterio. Se il provider di attestazione è configurato con i certificati del firmatario dei criteri, il JWT deve essere firmato con la chiave privata di uno dei certificati di firma dei criteri validi associati al provider. 

Per configurare un criterio in formato testo, specificare direttamente il testo del criterio.

In PowerShell specificare PolicyFormat come JWT per configurare i criteri in formato JWT. Il formato predefinito del criterio è testo.

Vedere esempi di [criteri](/azure/attestation/policy-examples) di attestazione e [come creare un criterio di attestazione](/azure/attestation/author-sign-policy) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. AZ. Certification problemi di installazione in PowerShell

Non è possibile installare i moduli AZ o AZ. Attestation in PowerShell

### <a name="error"></a>Errore

AVVISO: Impossibile risolvere l'origine del pacchetto ' https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package: non è stata trovata alcuna corrispondenza per i criteri di ricerca e il nome del modulo specificati

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

PowerShell Gallery ha deprecato Transport Layer Security (TLS) versioni 1,0 e 1,1. 

È consigliabile usare TLS 1.2 o versione successiva. 

Per continuare a interagire con PowerShell Gallery, eseguire il comando seguente prima dei comandi Install-Module

**[NET. ServicePointManager]:: SecurityProtocol = [NET. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. problemi di accesso/configurazione dei criteri in PowerShell

Utente assegnato con i ruoli appropriati. Ma con problemi di autorizzazione per la gestione dei criteri di attestazione tramite PowerShell.

### <a name="error"></a>Errore
Il client con ID oggetto non <object Id>  dispone dell'autorizzazione per eseguire l'azione Microsoft. Authorization/roleAssignments/Write Over Scope ' sottoscrizioni/ <subscriptionId> resourcegroups/secure_enclave_poc/Providers/Microsoft.Authorization/roleAssignments/ <role assignmentId> ' o l'ambito non è valido. Se l'accesso è stato concesso di recente, aggiornare le credenziali

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

La versione minima di AZ Modules necessaria per supportare le operazioni di attestazione è la seguente: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Eseguire il comando seguente per verificare la versione installata di tutti i moduli Az 

```powershell
Get-InstalledModule 
```

Se le versioni non sono corrispondenti al requisito minimo, eseguire Update-Module comandi

ad esempio,-Update-Module-Name AZ. Attestation

