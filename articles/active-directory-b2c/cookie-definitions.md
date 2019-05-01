---
title: Definizioni di cookie - Azure Active Directory B2C | Microsoft Docs
description: Fornisce le definizioni per i cookie usati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703986"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Definizioni dei cookie per Azure Active Directory B2C

La tabella seguente elenca i cookie usati in Azure Active Directory B2C.

| NOME | Domain | Scadenza | Scopo |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Contiene i dati di appartenenza utente tra i tenant. I tenant di un utente è membro di e a livello di appartenenza (amministratore o utente). |
| x-ms-cpim-slice | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Usato per indirizzare le richieste all'istanza di produzione appropriati. |
| x-ms-cpim-trans | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Usato per tenere traccia della transazione corrente e le transazioni (numero di richieste di autenticazione per Azure AD B2C). |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Usato per la gestione della sessione SSO. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine della [sessione del browser](active-directory-b2c-token-session-sso.md), autenticazione riuscita | Utilizzato per gestire lo stato della richiesta. |
| x-ms-cpim-csrf | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Token per la protezione CRSF Cross-Site Request Forgery. |
| x-ms-cpim-dc | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Utilizzato per il routing di rete di Azure AD B2C. |
| x-ms-cpim-ctx | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Context |
| x-ms-cpim-rp | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Usato per archiviare dati di appartenenza per il tenant del provider di risorse. |
| x-ms-cpim-rc | Login.microsoftonline.com, b2clogin.com, dominio personalizzato | Fine di [sessione del browser](active-directory-b2c-token-session-sso.md) | Usato per archiviare i cookie di inoltro. |

