---
title: Visualizzare ed eseguire ricerche per l'attività di accesso recente dalla pagina Accesso personale (anteprima) - Azure Active Directory . Documenti Microsoft
description: Dettagli su come visualizzare ed eseguire ricerche nell'attività di accesso recente dalla pagina I miei account di accesso del portale Account personale.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064020"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Visualizzare ed eseguire ricerche nell'attività di accesso recente dalla pagina I miei inquisigli (anteprima)

È possibile visualizzare tutte le attività di accesso all'account aziendale o dell'istituto di istruzione recenti, dalla pagina **I miei inquisienti** del portale **Account personale.** L'analisi della cronologia di accesso consente di verificare la presenza di attività insolite, aiutandoti a vedere:

- Se qualcuno sta cercando di indovinare la tua password.

- Se un utente malintenzionato ha eseguito correttamente l'accesso al proprio account e da quale posizione.

- Quali applicazioni l'utente malintenzionato ha tentato di accedere.

## <a name="view-your-recent-sign-in-activity"></a>Visualizzare le attività di accesso recenti

1. Accedere all'account aziendale o dell'istituto di istruzione e quindi passare alla pagina https://myprofile.microsoft.com/.

2. Selezionare **I miei inquisienti (anteprima)** nel riquadro di spostamento sinistro o selezionare il collegamento **Controlla attività recenti** dal blocco I miei **invii (anteprima).**

    ![Pagina Il mio account, con i link delle attività recenti evidenziati](media/my-account-portal/my-account-portal-sign-ins.png)

3. Espandere ed esaminare ogni attività di accesso, assicurandosi di riconoscerle tutte. Se trovi un elemento di accesso che non ti sembra familiare, ti consigliamo vivamente di modificare la password per proteggere il tuo account se è stata compromessa.

    ![Pagina Attività recenti con i dettagli di accesso espansi](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se viene visualizzato un accesso riuscito

Dovresti riconoscere la tua attività come normale. Tuttavia, se noti un accesso riuscito da una posizione, un browser o un sistema operativo strano, potrebbe significare che un utente malintenzionato ha ottenuto l'accesso al tuo account. In questo caso, si consiglia di modificare immediatamente la password e quindi passare alla pagina Informazioni di [sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

Prima di determinare che qualcosa non è corretto, assicurati di non vedere un falso positivo (dove l'elemento sembra discutibile, ma va bene). Ad esempio, determiniamo la tua posizione approssimativa e la tua mappa in base al tuo indirizzo IP. Le reti mobili sono particolarmente difficili da individuare in quanto a volte instradano il traffico attraverso luoghi lontani. Pertanto, se hai effettuato l'accesso utilizzando il tuo dispositivo mobile nello stato di Washington, la posizione potrebbe mostrare l'accesso proveniente dalla California. Per questo motivo, ti consigliamo vivamente di controllare più dettagli, oltre alla semplice posizione. Dovresti anche assicurarti che anche il sistema operativo, il browser e l'app abbiano un senso.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se viene visualizzato un accesso non riuscito

Un accesso non riuscito, senza attività della sessione, indica che il metodo di verifica principale (nome utente/password) non è riuscito. Questo potrebbe significare che hai digitato male il tuo nome utente o la password, ma potrebbe anche significare che un utente malintenzionato stava tentando di indovinare la tua password. Se si ritiene che sia stato un utente malintenzionato che ha tentato senza successo di indovinare la password, non è necessario modificare la password, ma è consigliabile eseguire la registrazione per Azure Multi-Factor Authentication (MFA). Con MFA, anche se l'hacker alla fine indovina la tua password, non sarà sufficiente per accedere al tuo account.

Se viene visualizzato un accesso non riuscito, con una nota in Attività sessione che indica Verifica **aggiuntiva non riuscita, codice non valido**, significa che l'autenticazione principale (nome utente/password) ha avuto esito positivo, ma l'autenticazione a più fattori non è riuscita. Se si tratta di un utente malintenzionato, è stato eseguito correttamente la password, ma non è stato ancora possibile superare la richiesta di autenticazione a più fattori. In questo caso, è consigliabile modificare comunque la password, poiché l'utente malintenzionato ha ottenuto la parte corretta e quindi passare alla pagina Informazioni di [sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

## <a name="search-for-specific-sign-in-activity"></a>Cercare un'attività di accesso specifica

È possibile cercare l'attività di accesso recente in base a una qualsiasi delle informazioni disponibili. Ad esempio, puoi cercare l'attività di accesso recente in base al sistema operativo, alla posizione, all'app e così via.

1. Nella pagina **Controlla attività recenti** digitare le informazioni che si desidera cercare nella barra di **ricerca.** Ad esempio, `My Account` digitare per cercare tutte le attività raccolte dall'app Account personale.

2. Selezionare il pulsante **Cerca** per iniziare la ricerca.

    ![Pagina Attività recenti, con la barra di ricerca evidenziata, il pulsante di ricerca e i risultati](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato l'attività di accesso recente, è possibile:

- Visualizzare o gestire le [informazioni di sicurezza](user-help-security-info-overview.md).

- Visualizzare o gestire i [dispositivi](my-account-portal-devices-page.md)connessi.

- Visualizzare o gestire [le organizzazioni](my-account-portal-organizations-page.md).

- Visualizzare il [modo](my-account-portal-privacy-page.md)in cui l'organizzazione utilizza i dati relativi alla privacy .
