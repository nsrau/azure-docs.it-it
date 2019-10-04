---
title: Ottenere supporto per il portale delle app personali-Azure Active Directory | Microsoft Docs
description: Ottenere assistenza per l'accesso e l'esecuzione di attività comuni nel portale app personali.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11a7376720661e13a03be65df4ee180e0bca20c3
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383072"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Risolvere i problemi del portale app personali

Se si verificano problemi durante l'accesso a o tramite il portale **app personali** , provare questi suggerimenti per la risoluzione dei problemi prima di contattare il supporto tecnico o l'amministratore per assistenza.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Si verificano problemi durante l'installazione dell'estensione per l'accesso sicuro alle app personali

Se si verificano problemi durante l'installazione dell'estensione per l'accesso sicuro alle app personali:

- Verificare che sia in uso un browser supportato, tra cui:

    - **Microsoft Edge.** In esecuzione in Windows 10 Anniversary Edition o versioni successive.

    - **Google Chrome.** In esecuzione su Windows 7 o versione successiva e in Mac OS X o versione successiva.

    - **Mozilla Firefox 26,0 o versione successiva.** In esecuzione in Windows XP SP2 o versioni successive e in Mac OS X 10,6 o versione successiva.

    - **Internet Explorer 11.** Esecuzione in Windows 7 o versione successiva (supporto limitato).

- Verificare che le impostazioni dell'estensione del browser siano attivate.

- Provare a riavviare il browser e accedere di nuovo al portale **app personali** .

- Provare a cancellare i cookie del browser e quindi a riavviare e accedere di nuovo al portale **app personali** .

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Non è possibile accedere al portale **app personali**

Se si verificano problemi durante l'accesso al portale **app personali** , è possibile provare a eseguire le operazioni seguenti:

- Assicurarsi di usare l'URL corretto. Deve essere https://myapps.microsoft.com o una pagina personalizzata per l'organizzazione, https://myapps.microsoft.com/contoso.com ad esempio.

- Assicurarsi che la password sia corretta e non sia scaduta. Per altre informazioni, vedere [reimpostare la password aziendale o dell'Istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Verificare che le informazioni di verifica siano aggiornate e accurate. Per altre informazioni, vedere [cosa significa Azure Multifactor Authentication per me?](multi-factor-authentication-end-user.md) o [modificare i metodi e le informazioni di sicurezza](security-info-add-update-methods-overview.md).

- Aggiungere l'URL del portale dell' **app** all'impostazione **Proprietà Internet > sicurezza > siti attendibili** .

- Cancellare la cache del browser e riprovare ad accedere.

## <a name="my-password-isnt-working"></a>La password non funziona

Se la password è stata dimenticata, non ne è mai stata ricevuta una dall'organizzazione, l'account è bloccato o si vuole modificare la password, vedere la pagina della [Guida, ho dimenticato la password del Azure ad](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Desidero poter reimpostare la password personale

Per poter reimpostare la propria password, l'amministratore deve innanzitutto attivare la funzionalità per l'organizzazione, quindi è necessario aggiornare e verificare i metodi di verifica richiesti. Per altre informazioni su come aggiornare i metodi di verifica, vedere eseguire la [registrazione per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Quando si avvia un'app, viene ricevuto un messaggio di accesso negato

Se si sta ricevendo un messaggio di **accesso negato** dopo l'avvio di un'app dal portale dell' **app** , è possibile provare a eseguire le operazioni seguenti:

- Assicurarsi di aver installato l' [estensione per l'accesso sicuro alle app personali](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) e che si stia usando un [browser supportato](my-apps-portal-end-user-access.md#supported-browsers).

- Assicurarsi di usare l'URL corretto per l'app e che l'URL sia presente nelle **Proprietà Internet > sicurezza >** elenco dei siti attendibili.

- Assicurarsi che la password sia corretta e non sia scaduta. Per altre informazioni, vedere [reimpostare la password aziendale o dell'Istituto di istruzione](active-directory-passwords-update-your-own-password.md).

- Verificare che le informazioni di verifica siano aggiornate e accurate. Per altre informazioni, vedere [cosa significa Azure Multifactor Authentication per me?](multi-factor-authentication-end-user.md) o [modificare i metodi e le informazioni di sicurezza](security-info-add-update-methods-overview.md).

- Cancellare la cache del browser e riprovare ad accedere.

Se dopo aver provato questi elementi non è ancora possibile accedere all'app, è necessario contattare il supporto tecnico dell'organizzazione per assistenza.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver effettuato l'accesso al portale **app personali** , è anche possibile aggiornare il profilo e le informazioni sull'account, le informazioni sul gruppo e le informazioni di verifica di accesso (se si dispone dell'autorizzazione).

- [Accedere e usare le app nel portale app personali](my-apps-portal-end-user-access.md).

- [Modificare le informazioni del profilo](my-apps-portal-end-user-update-profile.md).

- [Visualizzare e aggiornare le informazioni relative ai gruppi](my-apps-portal-end-user-groups.md).

- [Eseguire le proprie](my-apps-portal-end-user-access-reviews.md)verifiche di accesso.
