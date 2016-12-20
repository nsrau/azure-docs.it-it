---
title: Formato del token utente esterno per l&quot;anteprima di Azure Active Directory B2B Collaboration | Documentazione Microsoft
description: Azure Active Directory B2B supporta le relazioni tra aziende abilitando i partner commerciali ad accedere in modo selettivo alle applicazioni aziendali
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 66df5c92-1603-4d26-966b-c06cbfb7931a
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7edfbd9884d337e80a449f51af6abd962d138ddb


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-token-format"></a>Anteprima di Collaborazione B2B di Azure AD: formato del token di utente esterno
Le attestazioni per un token di Azure AD standard sono descritte nell’articolo [Tipi di attestazioni e di token supportati](active-directory-token-and-claims.md) su azure.microsoft.com.

Le attestazioni che differiscono per un utente esterno autenticato di Collaborazione B2B sono le seguenti:<br/>

* **OID**: ID oggetto dal tenant delle risorse<br/>
* **TID**: ID tenant dal tenant delle risorse<br/>
* **Issuer**: tenant delle risorse<br/>
* **IDP**: tenant home dell'utente<br/>
* **AltSecId**: ID di sicurezza alternativo, invisibile all'utente<br/>

## <a name="related-articles"></a>Articoli correlati
Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Funzionamento](active-directory-b2b-how-it-works.md)
* [Procedura dettagliata](active-directory-b2b-detailed-walkthrough.md)
* [Riferimento sul formato di file CSV](active-directory-b2b-references-csv-file-format.md)
* [Modifiche agli attributi dell'oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limitazioni correnti della versione di anteprima](active-directory-b2b-current-preview-limitations.md)
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


