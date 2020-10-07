---
title: Configurare Attestazione di Azure con Azure PowerShell
description: Come impostare e configurare un provider di attestazioni con Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 538aa29ab66fce48da944dbdf9ea79d5c8f7f330
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89421289"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Avvio rapido: Configurare Attestazione di Azure con Azure PowerShell

Seguire la procedura seguente per creare e configurare un provider di attestazioni con Azure PowerShell. Per informazioni su come installare ed eseguire Azure PowerShell, vedere [Panoramica di Azure PowerShell](/powershell/azure/?view=azps-2.8.0&viewFallbackFrom=azps-2.4.0).

Tenere presente che PowerShell Gallery ha deprecato le versioni 1.0 e 1.1 di TLS (Transport Layer Security). È consigliabile usare TLS 1.2 o versione successiva. Di conseguenza, è possibile che si ricevano gli errori seguenti:

- AVVISO: Non è possibile risolvere l'origine del pacchetto 'https://www.powershellgallery.com/api/v2 '
- PackageManagement\Install-Package: Non sono state trovate corrispondenze per i criteri di ricerca e il nome del modulo specificati 

Per continuare a interagire con PowerShell Gallery, eseguire il comando seguente prima dei comandi Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Installare il modulo Az.Attestation di PowerShell

Nel computer con Azure PowerShell installare il modulo Az.Attestation di PowerShell, che contiene i cmdlet per Attestazione di Azure.  

### <a name="initial-installation"></a>Installazione iniziale

Chiudere tutte le finestre di PowerShell esistenti.

Per eseguire l'installazione per l'"utente corrente", avviare una finestra di PowerShell senza privilegi elevati ed eseguire:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Per eseguire l'installazione per "tutti gli utenti", avviare una finestra di PowerShell con privilegi elevati ed eseguire:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Chiudere la console di PowerShell con privilegi elevati.

### <a name="update-the-installation"></a>Aggiornare l'installazione

Chiudere tutte le finestre di PowerShell esistenti.

Per eseguire l'aggiornamento per l'"utente corrente", avviare una finestra di PowerShell senza privilegi elevati ed eseguire:

```powershell
Update-Module -Name Az.Attestation
```

Per eseguire l'aggiornamento per "tutti gli utenti", avviare una finestra di PowerShell con privilegi elevati ed eseguire:

```powershell
Update-Module -Name Az.Attestation
```

Chiudere la console di PowerShell con privilegi elevati.

### <a name="get-installed-modules"></a>Ottenere i moduli installati

Versione minima dei moduli Az necessaria per supportare le operazioni di attestazione:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Eseguire il comando seguente per verificare la versione installata di tutti i moduli Az 

```powershell
Get-InstalledModule
```
Se le versioni non corrispondono al requisito minimo, eseguire i comandi Update-Module.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure nella console di PowerShell (senza privilegi di accesso elevati).

```powershell
Connect-AzAccount
```

Se necessario, passare alla sottoscrizione da usare per Attestazione di Azure.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registrare il provider di risorse Microsoft.Attestation

Registrare il provider di risorse Microsoft.Attestation nella sottoscrizione. Per altre informazioni sui provider di risorse di Azure e su come configurarli e gestirli, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md). Si noti che la registrazione di un provider di risorse è necessaria una sola volta per una sottoscrizione.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Disponibilità a livello di area di Attestazione di Azure

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Creare un gruppo di risorse di Azure

Creare un gruppo di risorse per il provider di attestazioni. Si noti che è possibile inserire altre risorse di Azure (inclusa una macchina virtuale con un'istanza dell'applicazione client) nello stesso gruppo di risorse.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Creare e gestire un provider di attestazioni

New-AzAttestation crea un provider di attestazioni.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile è un file che specifica un set di chiavi di firma attendibili. Se viene specificato un nome file per il parametro PolicySignerCertificateFile, il provider di attestazioni può essere configurato solo con i criteri nel formato JWT firmato. In caso contrario, il criterio può essere configurato in formato testo o JWT non firmato.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Per un esempio di PolicySignersCertificateFile, vedere [Esempi di certificato del firmatario di criteri](policy-signer-examples.md).

Get-AzAttestation recupera le proprietà del provider di attestazioni, ad esempio lo stato e AttestURI. Prendere nota di AttestURI, perché sarà necessario in un secondo momento.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Il comando precedente dovrebbe produrre un output simile al seguente: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

I provider di attestazioni possono essere eliminati usando il cmdlet Remove-AzAttestation.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Gestione dei criteri

Per gestire i criteri, gli utenti di Azure AD devono avere le autorizzazioni seguenti per "Azioni":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

Queste autorizzazioni possono essere assegnate a un utente di AD tramite un ruolo come "Proprietario" (autorizzazioni con caratteri jolly), "Collaboratore" (autorizzazioni con caratteri jolly) o "Collaboratore per attestazioni" (autorizzazioni specifiche solo per Attestazione di Azure).  

Per leggere i criteri, gli utenti di Azure AD devono avere l'autorizzazione seguente per "Azioni":
- Microsoft.Attestation/attestationProviders/attestation/read

Questa autorizzazione può essere assegnata a un utente di Azure AD tramite un ruolo come "Lettore" (autorizzazioni con caratteri jolly) o "Ruolo con autorizzazioni di lettura per le attestazioni" (autorizzazioni specifiche solo per Attestazione di Azure).

I cmdlet di PowerShell seguenti forniscono la gestione dei criteri per un provider di attestazioni (un ambiente TEE alla volta).

Get-AzAttestationPolicy restituisce il criterio corrente per il TEE specificato. Il cmdlet visualizza il criterio in formato testo e JWT.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

I tipi di ambienti TEE supportati sono "sgxenclave" e "vbsenclave".

Set-AttestationPolicy imposta un nuovo criterio per l'ambiente TEE specificato. Il cmdlet accetta i criteri in formato testo o JWT e viene controllato dal parametro PolicyFormat. "Text" è il valore predefinito per PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Se durante la creazione di un provider di attestazioni viene specificato il parametro PolicySignerCertificateFile, i criteri possono essere configurati solo in formato JWT firmato. In caso contrario, il criterio può essere configurato in formato testo o JWT non firmato.

I criteri di attestazione nel formato JWT devono contenere un'attestazione denominata "AttestationPolicy". Per i criteri firmati, il token JWT deve essere firmato con una chiave privata corrispondente a uno dei certificati del firmatario di criteri esistenti.

Per informazioni, vedere [Esempi di un criterio di attestazione](policy-examples.md).

Reset-AzAttestationPolicy reimposta il criterio sul valore predefinito per l'ambiente TEE specificato.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Gestione dei certificati del firmatario di criteri

I cmdlet di PowerShell seguenti forniscono la gestione dei certificati del firmatario di criteri per un provider di attestazioni:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Il certificato del firmatario di criteri è un token JWT firmato con un'attestazione denominata "maa-policyCertificate". Il valore dell'attestazione è un token JWK che contiene la chiave di firma attendibile da aggiungere. Il token JWT deve essere firmato con una chiave privata corrispondente a uno dei certificati del firmatario di criteri esistenti.

Si noti che tutte le modifiche semantiche del certificato del firmatario di criteri devono essere eseguite all'esterno di PowerShell. Per quanto riguarda PowerShell, si tratta di una stringa semplice.

Per un esempio di certificato del firmatario di criteri, vedere [Esempi di certificato del firmatario dei criteri](policy-signer-examples.md).

Per altre informazioni sui cmdlet e sui relativi parametri, vedere [Cmdlet di PowerShell per Attestazione di Azure](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>Passaggi successivi

- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Attestare un'enclave SGX con esempi di codice](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
