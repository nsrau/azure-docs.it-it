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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189515"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definizioni dei cookie per Azure AD B2C

Nelle sezioni seguenti vengono fornite informazioni sui cookie utilizzati in Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>Navigava sullostesso sito

Il servizio Microsoft Azure AD B2C è compatibile con le configurazioni del browser navigava sullostesso sito, incluso il supporto per `SameSite=None` con l'attributo `Secure`.

Per salvaguardare l'accesso ai siti, i Web browser introdurranno un nuovo modello sicuro per impostazione predefinita che presuppone che tutti i cookie siano protetti dall'accesso esterno se non diversamente specificato. Il browser Chrome è il primo a implementare questa modifica, a partire da [Chrome 80 nel febbraio 2020](https://www.chromium.org/updates/same-site). Per ulteriori informazioni sulla preparazione per la modifica in Chrome, vedere [Developers: Get Ready for New navigava sullostesso sito = None; Impostazioni sicure dei cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) nel Blog di Chromium.

Gli sviluppatori devono usare la nuova impostazione del cookie, `SameSite=None`, per designare i cookie per l'accesso tra siti. Quando è presente l'attributo `SameSite=None`, è necessario usare un attributo `Secure` aggiuntivo, in modo che i cookie tra siti possano essere accessibili solo tramite connessioni HTTPS. Convalidare e testare tutte le applicazioni, incluse le applicazioni che usano Azure AD B2C.

Per altre informazioni, vedere:

* [Gestire le modifiche ai cookie navigava sullostesso sito nel browser Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Effetti sui siti Web dei clienti e sui prodotti e servizi Microsoft in Chrome versione 80 o successiva](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookie

Nella tabella seguente sono elencati i cookie utilizzati in Azure AD B2C.

| Nome | Dominio | Scadenza | Scopo |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Fine della [sessione del browser](session-behavior.md) | Include i dati di appartenenza degli utenti tra i tenant. I tenant di cui un utente è membro e il livello di appartenenza (amministratore o utente). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per indirizzare le richieste all'istanza di produzione appropriata. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per tenere traccia delle transazioni (numero di richieste di autenticazione a Azure AD B2C) e della transazione corrente. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per la gestione della sessione SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md), autenticazione riuscita | Utilizzato per mantenere lo stato della richiesta. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Richiesta tra siti: token di falsificazione usato per la protezione tipo. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Usato per Azure AD B2C il routing di rete. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Usato per archiviare i dati di appartenenza per il tenant del provider di risorse. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, dominio con personalizzazione | Fine della [sessione del browser](session-behavior.md) | Utilizzato per archiviare il cookie di inoltro. |
