---
title: Visualizzare e cercare le attività di accesso recenti dalla pagina di accesso (anteprima) Azure Active Directory | Microsoft Docs
description: Informazioni dettagliate su come visualizzare e cercare le attività di accesso recenti dalla pagina di accesso personale del portale account personale.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: a45caeed8b601a845b0eb0fbcd25f1ec097a77a1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279029"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Visualizza e cerca le attività di accesso recenti dalla pagina di accesso (anteprima)

È possibile visualizzare tutte le attività di accesso all'account aziendale o dell'Istituto di istruzione recenti dalla pagina di **accesso personale** del portale **account** personale. Esaminando la cronologia di accesso è possibile verificare la presenza di attività insolite consentendo di vedere:

- Se qualcuno sta provando a indovinare la password.

- Se un utente malintenzionato ha eseguito l'accesso al proprio account e da quale località.

- App a cui l'utente malintenzionato ha provato ad accedere.

## <a name="view-your-recent-sign-in-activity"></a>Visualizzare le attività di accesso recenti

1. Accedere all'account aziendale o dell'istituto di istruzione, quindi andare alla pagina https://myprofile.microsoft.com/.

2. Selezionare **accessi personali (anteprima)** nel riquadro di spostamento a sinistra oppure selezionare il collegamento **Controlla attività recente** dal blocco **accessi (anteprima)** .

    ![Pagina account personale, che mostra i collegamenti delle attività recenti evidenziati](media/my-account-portal/my-account-portal-sign-ins.png)

3. Espandere ed esaminare ogni attività di accesso, assicurandosi di riconoscerle tutte. Se si trova un elemento di accesso che non ha un aspetto familiare, è consigliabile modificare la password per proteggere l'account se è stato compromesso.

    ![Pagina Attività recenti con i dettagli di accesso espansi](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se viene visualizzato un accesso riuscito

È necessario riconoscere una propria attività come normale. Tuttavia, se si nota un accesso riuscito da una posizione sconosciuta, un browser o un sistema operativo, è possibile che un utente malintenzionato abbia ottenuto l'accesso all'account. In questa situazione è consigliabile modificare immediatamente la password e quindi passare alla pagina delle informazioni di [sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

Prima di determinare se un elemento non è corretto, assicurarsi che non venga visualizzato un falso positivo (in cui l'elemento sembra discutibile, ma è accettabile). Si consentirà, ad esempio, di determinare la posizione approssimativa e la mappa in base all'indirizzo IP. Le reti mobili sono particolarmente difficili da individuare perché talvolta instradano il traffico attraverso posizioni distanti. Quindi, se è stato effettuato l'accesso con il dispositivo mobile nello stato di Washington, il percorso potrebbe mostrare l'accesso proveniente dalla California. Per questo motivo, è consigliabile controllare altri dettagli, oltre al solo percorso. È anche necessario assicurarsi che anche il sistema operativo, il browser e l'app abbiano senso.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se viene visualizzato un accesso non riuscito

Un accesso non riuscito, senza attività di sessione, significa che il metodo di verifica principale (nome utente/password) non è riuscito. Questo potrebbe significare che il nome utente o la password è stata digitata in modo errato, ma potrebbe anche significare che un utente malintenzionato sta provando a indovinare la password. Se si ritiene che l'utente malintenzionato provi senza riuscirvi a indovinare la password, non è necessario modificare la password, ma è consigliabile registrarsi per Azure Multi-Factor Authentication (multi-factor authentication). Con l'autenticazione a più fattori, anche se il pirata informatico Indovina la password, non sarà sufficiente per accedere all'account.

Se viene visualizzato un accesso non riuscito, con una nota nell'attività della sessione che indica che la **verifica aggiuntiva non è riuscita, il codice non è valido**, significa che l'autenticazione principale (nome utente/password) ha avuto esito positivo, ma l'autenticazione a più fattori non è riuscita. Se si trattasse di un utente malintenzionato, la password è stata indovinata, ma non è ancora stato possibile superare la richiesta di autenticazione a più fattori. In questo caso, è comunque consigliabile modificare la password, poiché l'autore dell'attacco ha ottenuto tale diritto, quindi passare alla pagina delle [informazioni di sicurezza](https://mysignins.microsoft.com/security-info) per aggiornare le impostazioni di sicurezza.

## <a name="search-for-specific-sign-in-activity"></a>Ricerca di attività di accesso specifiche

È possibile cercare l'attività di accesso recente con le informazioni disponibili. Ad esempio, è possibile cercare le attività di accesso recenti per sistema operativo, località, app e così via.

1. Nella pagina **Verifica attività recente** Digitare le informazioni che si desidera cercare nella barra di **ricerca** . Ad esempio, digitare `My Account` per cercare tutte le attività raccolte dall'app My account.

2. Selezionare il pulsante **Cerca** per iniziare la ricerca.

    ![Pagina attività recente, che mostra la barra di ricerca evidenziata, il pulsante Cerca e i risultati](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato l'attività di accesso recente, è possibile:

- Visualizzare o gestire le [info di sicurezza](user-help-security-info-overview.md).

- Visualizzare o gestire i [dispositivi](my-account-portal-devices-page.md)connessi.

- Visualizzare o gestire le [organizzazioni](my-account-portal-organizations-page.md).

- Visualizzare il modo in cui l'organizzazione [Usa i dati relativi alla privacy](my-account-portal-privacy-page.md).
