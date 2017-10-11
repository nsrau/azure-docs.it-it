---
title: "Federazione di più istanze di Azure AD con una singola istanza di AD FS | Microsoft Docs"
description: "Questo documento illustra come eseguire la federazione di più istanze di Azure AD con una singola istanza di AD FS."
keywords: "eseguire la federazione, ADFS, AD FS, più tenant, singola istanza di AD FS, unica istanza di AD FS, federazione multi-tenant, ad fs con più foreste, aad connect, federazione, federazione tra tenant"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.openlocfilehash: 436bf5905d2b203dc4cceea97f4fb90593df7111
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
#<a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Eseguire la federazione di più istanze di Azure AD con una singola istanza di AD FS

In una singola farm AD FS a disponibilità elevata è possibile eseguire la federazione di più foreste, se tra di esse esiste un trust bidirezionale. Queste diverse foreste possono corrispondere o meno alla stessa istanza di Azure Active Directory. Questo articolo contiene istruzioni su come configurare la federazione tra una singola distribuzione di AD FS e più foreste con sincronizzazione in istanze diverse di Azure AD.

![Federazione multi-tenant con una singola istanza di AD FS](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> Il writeback dei dispositivi e l'aggiunta automatica di dispositivi non sono supportati in questo scenario.

> [!NOTE]
> Non è possibile usare Azure AD Connect per configurare la federazione in questo scenario perché Azure AD Connect può configurare la federazione per domini in una singola istanza di Azure AD.

##<a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Procedura per la federazione di AD FS con più istanze di Azure AD

Si supponga che il dominio contoso.com nell'istanza di Azure Active Directory contoso.onmicrosoft.com sia già federato con l'istanza locale di AD FS installata nell'ambiente Active Directory locale contoso.com e che Fabrikam.com sia un dominio nell'istanza di Azure Active Directory fabrikam.onmicrosoft.com.

##<a name="step-1-establish-a-two-way-trust"></a>Passaggio 1: Stabilire un trust bidirezionale
 
Per consentire all'istanza di AD FS in contoso.com di autenticare gli utenti in fabrikam.com, è necessario un trust bidirezionale tra contoso.com e fabrikam.com. Per creare il trust bidirezionale, seguire le linee guida contenute in questo [articolo](https://technet.microsoft.com/library/cc816590.aspx).
 
##<a name="step-2-modify-contosocom-federation-settings"></a>Passaggio 2: Modificare le impostazioni di federazione di contoso.com 
 
L'autorità di certificazione predefinita per un singolo dominio federato ad AD FS è "http://FQDNServizioADFS/adfs/services/trust", ad esempio "http://fs.contoso.com/adfs/services/trust". Azure Active Directory richiede un'autorità di certificazione univoca per ogni dominio federato. Dato che la stessa istanza di AD FS eseguirà la federazione di due domini, il valore dell'autorità di certificazione deve essere modificato in modo che sia univoco per ogni dominio federato con Azure Active Directory da AD FS. 
 
Nel server AD FS aprire Azure AD PowerShell e seguire questa procedura:
 
Connettersi all'istanza di Azure Active Directory contenente il dominio contoso.com: Connect-MsolService. Aggiornare le impostazioni di federazione per contoso.com: Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain.
 
L'autorità di certificazione nell'impostazione di federazione del dominio verrà modificata in "http://contoso.com/adfs/services/trust" e verrà aggiunta una regola attestazioni di rilascio per il trust della relying party di Azure AD per rilasciare il valore issuerId corretto in base al suffisso UPN.
 
##<a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Passaggio 3: Eseguire la federazione di fabrikam.com con AD FS
 
Nella sessione di Azure AD PowerShell seguire questa procedura: connettersi all'istanza di Azure Active Directory contenente il dominio fabrikam.com

    Connect-MsolService
Convertire il dominio gestito fabrikam.com in federato:

    Convert-MsolDomainToFederated -DomainName anandmsft.com -Verbose -SupportMultipleDomain
 
L'operazione riportata sopra eseguirà la federazione del dominio fabrikam.com con la stessa istanza di AD FS. È possibile verificare le impostazioni di dominio usando Get-MsolDomainFederationSettings per entrambi i domini.

## <a name="next-steps"></a>Passaggi successivi
[Connettere Active Directory ad Azure Active Directory](active-directory-aadconnect.md)
