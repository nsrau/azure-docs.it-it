---
title: Visualizzare e cercare le attività di accesso recenti dalla pagina di accesso personale-Azure Active Directory | Microsoft Docs
description: Informazioni dettagliate su come visualizzare e cercare le attività di accesso recenti dalla pagina Accessi personali del portale Account personale.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/03/2020
ms.author: curtand
ms.openlocfilehash: 1816170da0fb6e5120f8cec6b6dda68d2ea1c678
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543204"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Visualizza e cerca le attività di accesso recenti dalla pagina di accesso personale

È possibile visualizzare tutte le attività di accesso all'account aziendale o di istruzione dalla pagina **Accessi personali** del portale **Account personale**. Controllare la cronologia degli accessi consente di verificare la presenza di attività insolite e di vedere:

- Se qualcuno sta tentando di ottenere la password.
- Se un malintenzionato è riuscito ad accedere all'account e da che posizione.
- A quali app l'utente malintenzionato ha provato ad accedere.

## <a name="view-your-recent-sign-in-activity"></a>Visualizzare le attività di accesso recenti

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myaccount.microsoft.com/.

2. Selezionare **accessi personali** dal riquadro di spostamento a sinistra oppure selezionare il collegamento **esaminare l'attività recente** dal blocco **accessi personali** .

    ![Pagina Profilo personale con il collegamento Attività recenti evidenziato](media/my-account-portal/my-account-portal-sign-ins.png)

3. Espandere ed esaminare ogni attività di accesso, assicurandosi di riconoscerle tutte. Se si trova un elemento di accesso che non ha un aspetto familiare, modificare la password per proteggere l'account in caso di compromissione.

    ![Pagina Attività recenti con i dettagli di accesso espansi](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se viene visualizzato un accesso riuscito

In alcuni casi, durante la revisione della normale attività di accesso, è possibile che venga visualizzato un accesso riuscito da una posizione, un browser o un sistema operativo non noto. Gli accessi non noti possono significare che un utente malintenzionato ha ottenuto l'accesso all'account. Se viene visualizzata un'attività non autorizzata, è consigliabile modificare immediatamente la password e quindi passare a info di [sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

Prima di concludere che un'attività non è corretta, assicurarsi che non si tratti di un falso positivo, ovvero di un elemento che sembra sospetto ma è corretto. Si consentirà, ad esempio, di determinare la posizione approssimativa e la mappa in base all'indirizzo IP. Le reti mobili sono particolarmente difficili da individuare perché talvolta instradano il traffico attraverso posizioni distanti. Anche se si accede con il dispositivo mobile nello stato di Washington, il percorso potrebbe mostrare l'accesso proveniente dalla California. Si consiglia vivamente di controllare i dettagli oltre al solo percorso. Assicurarsi anche che il sistema operativo, il browser e l'app abbiano senso.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se viene visualizzato un accesso non riuscito

Se viene visualizzato un accesso non riuscito, potrebbe significare che le credenziali sono state digitate in modo non corretto. Potrebbe anche significare che un utente malintenzionato sta provando a indovinare la password. Per rispondere a questo rischio, non è necessario modificare la password, ma si consiglia di registrarsi per Azure Multi-Factor Authentication (multi-factor authentication). Con la funzionalità di autenticazione a più fattori, anche se l'hacker Indovina la password, non sarà sufficiente per accedere all'account.

![Riquadro di accesso non riuscito](media/my-account-portal-sign-ins-page/unsuccessful.png)

Se viene visualizzato un accesso non riuscito, con una nota nell' **attività della sessione** che indica, `Additional verification failed, invalid code` , significa che le credenziali di autenticazione primarie sono state completate, ma l'autenticazione a più fattori ha avuto esito negativo. Questa condizione può indicare che un utente malintenzionato ha indovinato correttamente la password, ma non è riuscito a superare la richiesta di autenticazione a più fattori. Si consiglia comunque di modificare la password, perché potrebbe essere già presente nell'utente malintenzionato e passare alla pagina delle [informazioni di sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

## <a name="search-for-specific-sign-in-activity"></a>Ricerca di attività di accesso specifiche

È possibile cercare le attività di accesso recenti usando qualsiasi delle informazioni disponibili. Ad esempio, è possibile cercare un'attività di accesso recente in base a sistema operativo, posizione, app e così via.

1. Inserire le informazioni da cercare nella pagina **Verifica l'attività recente**, all'interno della barra di **ricerca**. Digitare, ad esempio, `Unsuccessful` per cercare tutte le attività di accesso non riuscite raccolte dall'app My account.

2. Selezionare il pulsante **Cerca** per avviare la ricerca.

    ![Pagina Attività recente con la barra di ricerca, il pulsante Cerca e i risultati evidenziati](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Conferma attività insolita

Gli accessi contrassegnati come attività insolita possono essere confermati nel riquadro per tale attività nella pagina **accessi personali** .

![Il riquadro di accesso insolito per conferma che l'utente ha fatto o non ha provato a eseguire l'accesso](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato le attività di accesso recenti è possibile:

- Visualizzare o gestire le [informazioni di sicurezza](user-help-security-info-overview.md).

- Visualizzare o gestire i [dispositivi](my-account-portal-devices-page.md) connessi.

- Visualizzare o gestire le [organizzazioni](my-account-portal-organizations-page.md).

- Visualizzare in che modo l'organizzazione [usa i dati relativi alla privacy](my-account-portal-privacy-page.md).

- Modificare le [impostazioni del portale Account personale](my-account-portal-settings.md)
