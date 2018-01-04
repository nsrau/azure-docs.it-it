---
title: Come aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD | Microsoft Docs
description: Viene illustrato come inserire l'applicazione multi-tenant sviluppata personalizzata nella raccolta di applicazioni di AD Azure
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Come aggiungere un'applicazione multi-tenant alla raccolta di applicazioni di Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>Definizione della raccolta di applicazioni di Azure AD

La raccolta di applicazioni di Azure AD è un ottimo modo per esporre l'applicazione a milioni di clienti di Azure Active Directory per ampliare l'impatto e la portata dell'applicazione nel marketplace. I passaggi seguenti illustrano come inserire l'applicazione nella raccolta di applicazioni di Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se l'applicazione supporta SAML o OpenIDConnect
Se si dispone di un'applicazione multi-tenant che si vuole inserire nella raccolta di applicazioni di Azure AD, è prima necessario assicurarsi che l'applicazione supporti una delle tecnologie seguenti Single Sign-On:

1. **OpenID Connect** : integrazione diretta con Azure AD usando OpenID Connect per l'autenticazione e l'API di consenso di Azure AD per la configurazione. Se si sta iniziando un'integrazione e l'applicazione non supporta SAML, questa è la modalità consigliata.
2. **SAML** : l'applicazione può già configurare i provider di identità di terze parti tramite il protocollo SAML.

Se l'applicazione supporta una di queste modalità Single Sign-On e si intende inserirla nella raccolta di applicazioni multi-tenant di Azure AD, è possibile seguire la procedura indicata di seguito nel documento. Per iniziare rapidamente, inviare un messaggio di posta elettronica a **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se l'applicazione non supporta SAML o OpenIDConnect
Anche se l'applicazione non supporta una di queste modalità, è comunque possibile integrarla nella raccolta usando la tecnologia Single Sign-On tramite password. Se si vuole esplorare questa opzione, è possibile inviare un messaggio di posta elettronica a **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Passaggi successivi
[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
