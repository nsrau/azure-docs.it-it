---
title: Proteggere il dominio gestito di Azure Active Directory Domain Services | Microsoft Docs
description: Proteggere il dominio gestito
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 3797c76f1537f86357f7ca68ffed4758eb1bdc9a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173763"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Proteggere il dominio gestito di Azure Active Directory Domain Services
Questo articolo indica come proteggere il dominio gestito. È possibile disattivare l'utilizzo dei pacchetti di crittografia vulnerabili e disabilitare la sincronizzazione del codice hash delle credenziali NTLM.

## <a name="install-the-required-powershell-modules"></a>Installare i moduli di PowerShell necessari

### <a name="install-and-configure-azure-ad-powershell"></a>Installare e configurare Azure AD PowerShell
Seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installare e configurare Azure PowerShell
Seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Disabilitare i pacchetti di crittografia vulnerabili e la sincronizzazione del codice hash delle credenziali NTLM
Usare lo script di PowerShell seguente:
1. Disabilitare il supporto per NTLM v1 nel dominio gestito.
2. Disabilitare la sincronizzazione degli hash delle password NTLM dall'istanza di Active Directory locale.
3. Disabilitare TLS v1 nel dominio gestito.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sulla sincronizzazione in Azure AD Domain Services](active-directory-ds-synchronization.md)
