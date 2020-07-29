---
title: Visualizzazione e ricerca delle attività di accesso recenti nella pagina Accessi personali (anteprima) - Azure Active Directory | Microsoft Docs
description: Informazioni dettagliate su come visualizzare e cercare le attività di accesso recenti dalla pagina Accessi personali del portale Account personale.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744522"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Visualizzazione e ricerca delle attività di accesso recenti dalla pagina Accessi personali (anteprima)

È possibile visualizzare tutte le attività di accesso all'account aziendale o di istruzione dalla pagina **Accessi personali** del portale **Account personale**. Controllare la cronologia degli accessi consente di verificare la presenza di attività insolite e di vedere:

- Se qualcuno sta tentando di ottenere la password.

- Se un malintenzionato è riuscito ad accedere all'account e da che posizione.

- A quali app l'utente malintenzionato ha provato ad accedere.

## <a name="view-your-recent-sign-in-activity"></a>Visualizzare le attività di accesso recenti

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myaccount.microsoft.com/.

2. Selezionare **Accessi personali (anteprima)** dal riquadro di spostamento a sinistra, oppure il collegamento **Verifica le attività recenti** nel blocco **Accessi personali (anteprima)** .

    ![Pagina Profilo personale con il collegamento Attività recenti evidenziato](media/my-account-portal/my-account-portal-sign-ins.png)

3. Espandere ed esaminare ogni attività di accesso, assicurandosi di riconoscerle tutte. Se si hanno dubbi su un'attività di accesso, si raccomanda di cambiare la password per proteggere l'account, in caso sia stato compromesso.

    ![Pagina Attività recenti con i dettagli di accesso espansi](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se viene visualizzato un accesso completato

Le proprie attività dovrebbero avere un aspetto normale. Tuttavia, se si nota un accesso completato da una posizione, un browser o un sistema operativo sconosciuti, è possibile che un utente malintenzionato sia riuscito ad accedere all'account. In questo caso raccomandiamo di cambiare immediatamente la password e poi di accedere alla pagina [Informazioni di sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di protezione.

Prima di concludere che un'attività non è corretta, assicurarsi che non si tratti di un falso positivo, ovvero di un elemento che sembra sospetto ma è corretto. Ad esempio, in base all'indirizzo IP è possibile stabilire la posizione approssimativa e la mappa. Le reti mobili sono particolarmente difficili da individuare perché talvolta instradano il traffico attraverso posizioni distanti. Se pertanto si è effettuato l'accesso da un dispositivo mobile nello stato di Washington, la posizione potrebbe indicare che l'accesso proviene dalla California. Per questo motivo, consigliamo vivamente di controllare altri dettagli, oltre alla sola posizione. Assicurarsi anche che sistema operativo, browser e app siano corretti.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se viene visualizzato un accesso non riuscito

Un accesso non riuscito, senza attività di sessione, significa che il metodo di verifica principale (nome utente/password) non è riuscito. Ciò può significare che nome utente o password non sono stati digitati in modo corretto, ma anche che un utente malintenzionato ha tentato di indovinare la password. Se si ritiene che un utente malintenzionato abbia provato a indovinare la password senza riuscirvi, non è necessario modificare la password, ma si raccomanda di registrarsi ad Azure Multi-Factor Authentication (autenticazione a più fattori, MFA). Con l'autenticazione a più fattori, anche se il pirata informatico riesce a indovinare la password, non potrà accedere all'account.

Se si nota un accesso non riuscito con la nota nell'attività della sessione **La verifica aggiuntiva non è riuscita, il codice non è valido**, l'autenticazione principale (nome utente/password) ha avuto esito positivo, ma l'autenticazione a più fattori non è riuscita. Se si tratta di un utente malintenzionato, ha indovinato la password, ma non è stato in grado di superare la richiesta di autenticazione a più fattori. In questo caso si consiglia di cambiare la password, perché l'utente malintenzionato ha ottenuto questa informazione, quindi di accedere alla pagina [Informazioni di sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di protezione.

## <a name="search-for-specific-sign-in-activity"></a>Ricerca di attività di accesso specifiche

È possibile cercare le attività di accesso recenti usando qualsiasi delle informazioni disponibili. Ad esempio, è possibile cercare un'attività di accesso recente in base a sistema operativo, posizione, app e così via.

1. Inserire le informazioni da cercare nella pagina **Verifica l'attività recente**, all'interno della barra di **ricerca**. Ad esempio, digitare `My Account` per cercare tutte le attività raccolte dall'app Account personale.

2. Selezionare il pulsante **Cerca** per avviare la ricerca.

    ![Pagina Attività recente con la barra di ricerca, il pulsante Cerca e i risultati evidenziati](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato le attività di accesso recenti è possibile:

- Visualizzare o gestire le [informazioni di sicurezza](user-help-security-info-overview.md).

- Visualizzare o gestire i [dispositivi](my-account-portal-devices-page.md) collegati.

- Visualizzare o gestire le [organizzazioni](my-account-portal-organizations-page.md).

- Visualizzare in che modo l'organizzazione [usa i dati relativi alla privacy](my-account-portal-privacy-page.md).
