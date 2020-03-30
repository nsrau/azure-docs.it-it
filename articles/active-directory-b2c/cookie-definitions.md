---
title: Definizioni dei cookie
titleSuffix: Azure AD B2C
description: Fornisce le definizioni per i cookie utilizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189515"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definizioni dei cookie per Azure AD B2C

Nelle sezioni seguenti vengono fornite informazioni sui cookie usati in Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSito

Il servizio B2C di Microsoft Azure AD è compatibile `SameSite=None` con `Secure` le configurazioni del browser SameSite, incluso il supporto per con l'attributo.

Per salvaguardare l'accesso ai siti, i browser Web introdurranno un nuovo modello sicuro per impostazione predefinita che presuppone che tutti i cookie debbano essere protetti dall'accesso esterno, se non diversamente specificato. Il browser Chrome è il primo a implementare questa modifica, a partire da [Chrome 80 nel febbraio 2020.](https://www.chromium.org/updates/same-site) Per ulteriori informazioni sulla preparazione per la modifica in Chrome, consulta [Sviluppatori: Prepararsi per il nuovo SameSite: Nessuno; Impostazioni sicure](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) dei cookie sul blog Chromium.

Gli sviluppatori devono utilizzare `SameSite=None`la nuova impostazione dei cookie, , per designare i cookie per l'accesso intersito. Quando `SameSite=None` l'attributo è `Secure` presente, è necessario utilizzare un attributo aggiuntivo in modo che i cookie tra siti siano accessibili solo tramite connessioni HTTPS. Convalidare e testare tutte le applicazioni, incluse quelle che usano Azure AD B2C.

Per altre informazioni, vedere:

* [Gestire le modifiche ai cookie SameSite nel browser Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Effetto sui siti Web dei clienti e sui servizi e i prodotti Microsoft in Chrome versione 80 o successiva](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie

Nella tabella seguente sono elencati i cookie usati in Azure AD B2C.

| Nome | Dominio | Scadenza | Scopo |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fine della sessione del [browser](session-behavior.md) | Contiene i dati di appartenenza utente tra tenant. I tenant di cui un utente sono membri e di appartenenza (Amministratore o Utente). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per instradare le richieste all'istanza di produzione appropriata. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per tenere traccia delle transazioni (numero di richieste di autenticazione ad Azure AD B2C) e della transazione corrente. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per mantenere la sessione SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md), autenticazione riuscita | Utilizzato per mantenere lo stato della richiesta. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Token di contraffazione delle richieste cross-site utilizzato per la protezione CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per il routing di rete B2C di Azure AD. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per l'archiviazione dei dati di appartenenza per il tenant del provider di risorse. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, dominio personalizzato | Fine della sessione del [browser](session-behavior.md) | Utilizzato per l'archiviazione del cookie di inoltro. |
