---
title: Avvisi di sicurezza per i ruoli di Azure AD in PIM - Azure AD Documenti Microsoft
description: Configurare gli avvisi di sicurezza per i ruoli di Azure AD Gestione delle identità con privilegi in Azure Active Directory.Configure security alerts for Azure AD roles Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253312"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configurare gli avvisi di sicurezza per i ruoli di Azure AD in Gestione delle identità con privilegiConfigure security alerts for Azure AD roles in Privileged Identity Management

La gestione delle identità con privilegi (PIM, Privileged Identity Management) genera avvisi quando è presente un'attività sospetta o non sicura nell'organizzazione di Azure Active Directory (Azure AD). Quando viene attivato, un avviso viene visualizzato nel dashboard Gestione identità con privilegi. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure.Start in November 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure resource roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Accedere al [portale](https://portal.azure.com/) di Azure con un utente che risieda il ruolo di amministratore del ruolo Privileged.Sign in to the Azure portal with a user who is in the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role role role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Seguire i passaggi descritti in questo articolo per analizzare gli avvisi di sicurezza per i ruoli di Azure AD.

# <a name="new-version"></a>[Nuova versione](#tab/new)

![Ruoli di Azure AD - Riquadro degli avvisi che elencano gli avvisi e la gravitàAzure AD roles - Alert pane listing alerts and the severity](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

Questa sezione elenca tutti gli avvisi di sicurezza per i ruoli di Azure AD, insieme a come risolvere e come prevenire. Ecco il significato dei vari livelli di gravità:

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri.
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

### <a name="administrators-arent-using-their-privileged-roles"></a>Gli amministratori non usano i ruoli con privilegi

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | Se sono presenti utenti a cui sono stati assegnati ruoli con privilegi non necessari, le probabilità di un attacco aumentano. Inoltre, gli utenti malintenzionati possono passare più facilmente inosservati in account che non sono attivamente in uso. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi non necessari. |
| **Prevenzione** | Assegnare ruoli privilegiati solo agli utenti che hanno una motivazione aziendale. </br>Pianificare [verifiche di accesso](pim-how-to-start-security-review.md) regolari per determinare se gli utenti hanno ancora bisogno dell'accesso. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Grilletto** | Attivato se un utente supera un numero specificato di giorni senza attivare un ruolo. |
| **Numero di giorni** | Questa impostazione specifica il numero massimo di giorni, da 0 a 100, che un utente può utilizzare senza attivare un ruolo.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>I ruoli non richiedono l'autenticazione a più fattori per l'attivazione

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | Senza l'autenticazione a più fattori, gli utenti compromessi possono attivare i ruoli con privilegi. |
| **Come risolvere?** | Esaminare l'elenco dei ruoli e [richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per ogni ruolo. |
| **Prevenzione** | [Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo.  |
| **Azione di mitigazione nel portale** | Rende necessaria l'autenticazione a più fattori per l'attivazione del ruolo con privilegi. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>L'organizzazione non dispone di Azure AD Premium P2The organization doesn't have Azure AD Premium P2

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'organizzazione di Azure AD corrente non dispone di Azure AD Premium P2. |
| **Come risolvere?** | Esaminare le informazioni sulle [edizioni di Azure AD](../fundamentals/active-directory-whatis.md). Eseguire l'aggiornamento ad Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Possibili account non aggiornati in un ruolo con privilegi

| | |
| --- | --- |
| **Gravità** | Media |
| **Perché viene visualizzato questo avviso?** | Gli account con un ruolo privilegiato non hanno modificato la password negli ultimi 90 giorni. Questi account potrebbero essere account di servizio o condivisi che non vengono mantenuti aggiornati e sono vulnerabili agli attacchi. |
| **Come risolvere?** | Esaminare gli account nell'elenco. Se l'accesso non è più necessario, rimuovere gli account dai ruoli con privilegi. |
| **Prevenzione** | Assicurarsi che per gli account condivisi venga eseguita la rotazione di password complesse quando cambiano gli utenti che conoscono la password. </br>Esaminare regolarmente gli account con ruoli con privilegi utilizzando [le verifiche di accesso](pim-how-to-start-security-review.md) e rimuovere le assegnazioni di ruolo non più necessarie. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Procedure consigliate** | Gli account di accesso condiviso, di servizio e di emergenza che eseguono l'autenticazione tramite password e vengono assegnati a ruoli amministrativi con privilegi elevati, ad esempio Amministratore globale o Amministratore sicurezza, devono avere le password ruotate per i casi seguenti:<ul><li>Dopo un evento imprevisto per la sicurezza che comporta l'uso improprio o la violazione dei diritti di accesso amministrativo</li><li>Dopo la modifica dei privilegi di un utente in seguito alla quale l'utente non è più amministratore (ad esempio, dopo che un dipendente che era amministratore lascia il team IT o l'organizzazione)</li><li>A intervalli regolari (ad esempio, trimestralmente o annualmente), anche se non si è verificata alcuna violazione nota o modifica del personale IT</li></ul>Poiché più utenti hanno accesso alle credenziali di questi account, le credenziali devono essere ruotate per garantire che le persone che hanno lasciato i propri ruoli non possano più accedere all'account. [Ulteriori informazioni sulla protezione degli account](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>I ruoli vengono assegnati all'esterno di Gestione identità con privilegi

| | |
| --- | --- |
| **Gravità** | Alto |
| **Perché viene visualizzato questo avviso?** | Le assegnazioni di ruolo con privilegi effettuate all'esterno di Gestione identità con privilegi non vengono monitorate correttamente e possono indicare un attacco attivo. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi assegnati all'esterno di Gestione identità con privilegi. |
| **Prevenzione** | Verificare dove agli utenti vengono assegnati ruoli con privilegi all'esterno di Gestione identità con privilegi e impedire assegnazioni future da lì. |
| **Azione di mitigazione nel portale** | Rimuove l'utente dal ruolo con privilegi. |

### <a name="there-are-too-many-global-administrators"></a>Il numero di amministratori globali presenti è eccessivo

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'amministratore globale è il ruolo con privilegi più elevati. Se un amministratore globale viene compromesso, l'utente malintenzionato ottiene l'accesso a tutte le autorizzazioni, il che mette a rischio l'intero sistema. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuovere quelli che non richiedono assolutamente il ruolo di amministratore globale. </br>Assegnare invece ruoli con privilegi inferiori a questi utenti. |
| **Prevenzione** | Assegnare agli utenti il ruolo con privilegi minimo di cui hanno bisogno. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Grilletto** | L'avviso viene attivato se sono soddisfatti due diversi criteri ed è possibile configurarli entrambi. In primo luogo, è necessario raggiungere una determinata soglia di amministratori globali. In secondo luogo, una determinata percentuale delle assegnazioni di ruolo totali deve essere Amministratori globali. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato. |
| **Numero minimo di amministratori globali** | Questa impostazione specifica il numero di amministratori globali, da 2 a 100, che si considera noto per l'organizzazione di Azure AD. |
| **Percentuale di amministratori globali** | Questa impostazione specifica la percentuale minima di amministratori amministratori globali, da 0% a 100%, al di sotto della quale non si vuole che l'organizzazione di Azure AD scappi. |

### <a name="roles-are-being-activated-too-frequently"></a>I ruoli vengono attivati con una frequenza eccessiva

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'attivazione ripetuta dello stesso ruolo con privilegi da parte dello stesso utente è indicativa di un attacco. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività. |
| **Prevenzione** | Verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività.</br>[Richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per i ruoli con privilegi con account condivisi da più amministratori. |
| **Azione di mitigazione nel portale** | N/D |
| **Grilletto** | Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo di tempo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni. |
| **Intervallo di tempo per il rinnovo delle attivazioni** | Questa impostazione specifica in giorni, ore, minuti e secondi il periodo per cui si vogliono rilevare i rinnovi sospetti. |
| **Numero di rinnovi di attivazioni** | Questa impostazione specifica il numero di attivazioni, da 2 a 100, in corrispondenza del quale si desidera ricevere una notifica, entro il periodo di tempo scelto. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo. |

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

Dalla pagina Avvisi passare a **Impostazioni**.

![Pagina Avvisi con l'applicazione impostazioni evidenziata](media/pim-how-to-configure-security-alerts/alert-settings.png)

Personalizzare le impostazioni per i diversi avvisi adattandole all'ambiente e agli obiettivi di sicurezza specifici.

![Pagina Impostazione di un avviso per abilitare e configurare le impostazioni](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

![Ruoli di Azure AD - Riquadro degli avvisi che elencano gli avvisi e la gravitàAzure AD roles - Alert pane listing alerts and the severity](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

Questa sezione elenca tutti gli avvisi di sicurezza per i ruoli di Azure AD, insieme a come risolvere e come prevenire. Ecco il significato dei vari livelli di gravità:

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri.
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

### <a name="administrators-arent-using-their-privileged-roles"></a>Gli amministratori non usano i ruoli con privilegi

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | Se sono presenti utenti a cui sono stati assegnati ruoli con privilegi non necessari, le probabilità di un attacco aumentano. Inoltre, gli utenti malintenzionati possono passare più facilmente inosservati in account che non sono attivamente in uso. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi non necessari. |
| **Prevenzione** | Assegnare ruoli privilegiati solo agli utenti che hanno una motivazione aziendale. </br>Pianificare [verifiche di accesso](pim-how-to-start-security-review.md) regolari per determinare se gli utenti hanno ancora bisogno dell'accesso. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Grilletto** | Attivato se un utente supera un numero specificato di giorni senza attivare un ruolo. |
| **Numero di giorni** | Questa impostazione specifica il numero massimo di giorni, da 0 a 100, che un utente può utilizzare senza attivare un ruolo.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>I ruoli non richiedono l'autenticazione a più fattori per l'attivazione

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | Senza l'autenticazione a più fattori, gli utenti compromessi possono attivare i ruoli con privilegi. |
| **Come risolvere?** | Esaminare l'elenco dei ruoli e [richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per ogni ruolo. |
| **Prevenzione** | [Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo.  |
| **Azione di mitigazione nel portale** | Rende necessaria l'autenticazione a più fattori per l'attivazione del ruolo con privilegi. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>L'organizzazione non dispone di Azure AD Premium P2The organization doesn't have Azure AD Premium P2

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'organizzazione di Azure AD corrente non dispone di Azure AD Premium P2. |
| **Come risolvere?** | Esaminare le informazioni sulle [edizioni di Azure AD](../fundamentals/active-directory-whatis.md). Eseguire l'aggiornamento ad Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Possibili account non aggiornati in un ruolo con privilegi

| | |
| --- | --- |
| **Gravità** | Media |
| **Perché viene visualizzato questo avviso?** | Gli account con un ruolo privilegiato non hanno modificato la password negli ultimi 90 giorni. Questi account potrebbero essere account di servizio o condivisi che non vengono mantenuti aggiornati e sono vulnerabili agli attacchi. |
| **Come risolvere?** | Esaminare gli account nell'elenco. Se l'accesso non è più necessario, rimuovere gli account dai ruoli con privilegi. |
| **Prevenzione** | Assicurarsi che per gli account condivisi venga eseguita la rotazione di password complesse quando cambiano gli utenti che conoscono la password. </br>Esaminare regolarmente gli account con ruoli con privilegi utilizzando [le verifiche di accesso](pim-how-to-start-security-review.md) e rimuovere le assegnazioni di ruolo non più necessarie. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Procedure consigliate** | Gli account di accesso condiviso, di servizio e di emergenza che eseguono l'autenticazione tramite password e vengono assegnati a ruoli amministrativi con privilegi elevati, ad esempio Amministratore globale o Amministratore sicurezza, devono avere le password ruotate per i casi seguenti:<ul><li>Dopo un evento imprevisto per la sicurezza che comporta l'uso improprio o la violazione dei diritti di accesso amministrativo</li><li>Dopo la modifica dei privilegi di un utente in seguito alla quale l'utente non è più amministratore (ad esempio, dopo che un dipendente che era amministratore lascia il team IT o l'organizzazione)</li><li>A intervalli regolari (ad esempio, trimestralmente o annualmente), anche se non si è verificata alcuna violazione nota o modifica del personale IT</li></ul>Poiché più utenti hanno accesso alle credenziali di questi account, le credenziali devono essere ruotate per garantire che le persone che hanno lasciato i propri ruoli non possano più accedere all'account. [Scopri di più](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>I ruoli vengono assegnati all'esterno di Gestione identità con privilegi

| | |
| --- | --- |
| **Gravità** | Alto |
| **Perché viene visualizzato questo avviso?** | Le assegnazioni di ruolo con privilegi effettuate all'esterno di Gestione identità con privilegi non vengono monitorate correttamente e possono indicare un attacco attivo. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi assegnati all'esterno di Gestione identità con privilegi. |
| **Prevenzione** | Verificare dove agli utenti vengono assegnati ruoli con privilegi all'esterno di Gestione identità con privilegi e impedire assegnazioni future da lì. |
| **Azione di mitigazione nel portale** | Rimuove l'utente dal ruolo con privilegi. |

### <a name="there-are-too-many-global-administrators"></a>Il numero di amministratori globali presenti è eccessivo

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'amministratore globale è il ruolo con privilegi più elevati. Se un amministratore globale viene compromesso, l'utente malintenzionato ottiene l'accesso a tutte le autorizzazioni, il che mette a rischio l'intero sistema. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e rimuovere quelli che non richiedono assolutamente il ruolo di amministratore globale. </br>Assegnare invece ruoli con privilegi inferiori a questi utenti. |
| **Prevenzione** | Assegnare agli utenti il ruolo con privilegi minimo di cui hanno bisogno. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Grilletto** | L'avviso viene attivato se sono soddisfatti due diversi criteri ed è possibile configurarli entrambi. In primo luogo, è necessario raggiungere una determinata soglia di amministratori globali. In secondo luogo, una determinata percentuale delle assegnazioni di ruolo totali deve essere Amministratori globali. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato. |
| **Numero minimo di amministratori globali** | Questa impostazione specifica il numero di amministratori globali, da 2 a 100, che si considera noto per l'organizzazione di Azure AD. |
| **Percentuale di amministratori globali** | Questa impostazione specifica la percentuale minima di amministratori amministratori globali, da 0% a 100%, al di sotto della quale non si vuole che l'organizzazione di Azure AD scappi. |

### <a name="roles-are-being-activated-too-frequently"></a>I ruoli vengono attivati con una frequenza eccessiva

| | |
| --- | --- |
| **Gravità** | Basso |
| **Perché viene visualizzato questo avviso?** | L'attivazione ripetuta dello stesso ruolo con privilegi da parte dello stesso utente è indicativa di un attacco. |
| **Come risolvere?** | Esaminare gli utenti nell'elenco e verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività. |
| **Prevenzione** | Verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività.</br>[Richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per i ruoli con privilegi con account condivisi da più amministratori. |
| **Azione di mitigazione nel portale** | N/D |
| **Grilletto** | Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo di tempo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni. |
| **Intervallo di tempo per il rinnovo delle attivazioni** | Questa impostazione specifica in giorni, ore, minuti e secondi il periodo per cui si vogliono rilevare i rinnovi sospetti. |
| **Numero di rinnovi di attivazioni** | Questa impostazione specifica il numero di attivazioni, da 2 a 100, in corrispondenza del quale si desidera ricevere una notifica, entro il periodo di tempo scelto. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo. |

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

È possibile personalizzare alcuni degli avvisi di sicurezza in Gestione identità con privilegi in modo che funzionino con le esigenze e gli obiettivi di sicurezza dell'organizzazione. Per aprire le impostazioni degli avvisi di sicurezza, seguire questi passaggi:

1. Aprire **Gestione identità con privilegi** in Azure AD.

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Impostazioni** e quindi **Avvisi**.

    ![Ruoli di Azure AD - Impostazioni con avvisi selezionatiAzure AD roles - Settings with Alerts selected](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selezionare un nome di avviso per configurare l'impostazione per l'avviso.

    ![Per l'avviso selezionato, il riquadro delle impostazioni degli avvisi di sicurezza](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli di Azure AD in Gestione delle identità con privilegiConfigure Azure AD role settings in Privileged Identity Management](pim-how-to-change-default-settings.md)
