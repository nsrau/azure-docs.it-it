---
title: Ottenere assistenza per il portale delle app personali - Azure Active Directory Documenti Microsoft
description: Ottenere assistenza per l'accesso e l'esecuzione di attività comuni nel portale delle app personali.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253156"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Risolvere i problemi relativi al portale App personali

Se si verificano problemi con l'accesso o l'utilizzo del portale **App** personali, provare questi suggerimenti per la risoluzione dei problemi prima di contattare l'helpdesk o l'amministratore per assistenza.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Non riesco a installare l'estensione My Apps Secure Sign-in

In caso di problemi durante l'installazione dell'estensione Accesso sicuro my Apps:

- Assicurati di utilizzare un browser supportato, tra cui:

    - **Microsoft Edge.** In esecuzione su Windows 10 Anniversary Edition o versione successiva.

    - **Google Chrome.** In esecuzione su Windows 7 o versioni successive e su Mac OS X o versioni successive.

    - **Mozilla Firefox 26.0 o versione successiva.** In esecuzione su Windows XP SP2 o versione successiva e su Mac OS X 10.6 o versioni successive.

    - **Internet Explorer 11.** In esecuzione su Windows 7 o versioni successive (supporto limitato).

- Assicurati che le impostazioni dell'estensione del browser siano attivate.

- Provare a riavviare il browser e ad accedere di nuovo al portale **App personali.**

- Provare a cancellare i cookie del browser, quindi riavviare e accedere nuovamente al portale **App** personali.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Non è possibile accedere al portale **App personali**

In caso di problemi di accesso al portale **App** personali, è possibile provare a eseguire le operazioni seguenti:

- Assicurati di utilizzare l'URL corretto. Deve essere https://myapps.microsoft.com o una pagina personalizzata per https://myapps.microsoft.com/contoso.coml'organizzazione, ad esempio .

- Assicurati che la password sia corretta e che non sia scaduta. Per altre info, vedi [Reimpostare la password aziendale o dell'istituto](active-directory-passwords-update-your-own-password.md)di istruzione.

- Assicurati che le informazioni di verifica siano aggiornate e accurate. Per altre informazioni, vedere [Che cosa significa Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md) o Modifica dei metodi e delle informazioni sulle informazioni di [sicurezza.](security-info-add-update-methods-overview.md)

- Aggiungere l'URL del portale **dell'app personale** all'impostazione **Proprietà Internet > Siti > attendibili.**

- Cancella la cache del browser e riprova ad accedere.

## <a name="my-password-isnt-working"></a>La password non funziona

Se si dimentica la password, non ne è mai ricevuta una dall'organizzazione, si esce dall'account o si vuole modificare la password, vedere [Guida, è stata dimenticata la password](active-directory-passwords-update-your-own-password.md)di Azure AD.

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Voglio essere in grado di reimpostare la mia password

Per poter reimpostare la propria password, l'amministratore deve prima attivare la funzionalità per l'organizzazione, quindi aggiornare e verificare i metodi di verifica necessari. Per ulteriori informazioni su come aggiornare i metodi di verifica, vedere Registrarsi per la [reimpostazione della password self-service](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Viene visualizzato un messaggio di accesso negato all'avvio di un'app

Se viene visualizzato un messaggio **di accesso negato** dopo l'avvio di un'app dal portale dell'app personale, è possibile provare a eseguire le operazioni seguenti: **My App**

- Assicurarsi di aver installato [l'estensione My Apps Secure Sign-in Extension](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) e di utilizzare un browser [supportato.](my-apps-portal-end-user-access.md#supported-browsers)

- Assicurarsi di utilizzare l'URL corretto per l'app e che l'URL sia visualizzato nell'elenco **Cartelle Internet > Sicurezza > Siti attendibili.**

- Assicurati che la password sia corretta e che non sia scaduta. Per altre info, vedi [Reimpostare la password aziendale o dell'istituto](active-directory-passwords-update-your-own-password.md)di istruzione.

- Assicurati che le informazioni di verifica siano aggiornate e accurate. Per altre informazioni, vedere [Che cosa significa Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md) o Modifica dei metodi e delle informazioni sulle informazioni di [sicurezza.](security-info-add-update-methods-overview.md)

- Cancella la cache del browser e riprova ad accedere.

Se dopo aver provato questi elementi non riesci ancora ad accedere alla tua app, devi contattare l'help desk della tua organizzazione per assistenza.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito l'accesso al portale **delle app** personali, è anche possibile aggiornare le informazioni relative al profilo e all'account, alle informazioni del gruppo e alle informazioni di verifica di accesso (se si dispone dell'autorizzazione).

- [Accedere e utilizzare le app nel portale App personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni del profilo](my-apps-portal-end-user-update-profile.md).

- [Visualizzare e aggiornare le informazioni relative ai gruppi.](my-apps-portal-end-user-groups.md)

- [Eseguire le proprie verifiche di accesso](my-apps-portal-end-user-access-reviews.md).
