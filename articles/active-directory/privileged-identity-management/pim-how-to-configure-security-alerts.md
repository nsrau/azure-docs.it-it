---
title: Configurare gli avvisi di sicurezza per i ruoli Azure AD in PIM-Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e188ef651f6fe539932cf1670f914e8b57564567
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809078"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configurare gli avvisi di sicurezza per i ruoli di Azure AD in Privileged Identity Management

Privileged Identity Management (PIM) genera avvisi in caso di attività sospette o non sicure nell'organizzazione di Azure Active Directory (Azure AD). Quando viene generato un avviso, questo viene visualizzato nel dashboard Privileged Identity Management. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

![Ruoli di Azure AD-riquadro avvisi che elenca gli avvisi e la gravità](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

In questa sezione vengono elencati tutti gli avvisi di sicurezza per i ruoli di Azure AD, nonché la modalità di correzione e la modalità di prevenzione. Ecco il significato dei vari livelli di gravità:

- **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri.
- **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
- **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

### <a name="administrators-arent-using-their-privileged-roles"></a>Gli amministratori non usano i ruoli con privilegi

| | |
| --- | --- |
| **Severity** | Basse |
| **Perché viene visualizzato questo avviso?** | Se sono presenti utenti a cui sono stati assegnati ruoli con privilegi non necessari, le probabilità di un attacco aumentano. Inoltre, gli utenti malintenzionati possono passare più facilmente inosservati in account che non sono attivamente in uso. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi che non sono necessari. |
| **Prevenzione** | Assegnare ruoli con privilegi solo agli utenti che dispongono di una giustificazione aziendale. </br>Pianificare [verifiche di accesso](pim-how-to-start-security-review.md) regolari per determinare se gli utenti hanno ancora bisogno dell'accesso. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Trigger** | Attivato se un utente supera un numero specificato di giorni senza attivare un ruolo. |
| **Numero di giorni** | Questa impostazione specifica il numero massimo di giorni, da 0 a 100, che un utente può usare senza attivare un ruolo.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>I ruoli non richiedono l'autenticazione a più fattori per l'attivazione

| | |
| --- | --- |
| **Severity** | Basse |
| **Perché viene visualizzato questo avviso?** | Senza l'autenticazione a più fattori, gli utenti compromessi possono attivare ruoli con privilegi. |
| **Come correggerlo?** | Esaminare l'elenco dei ruoli e [richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per ogni ruolo. |
| **Prevenzione** | [Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo.  |
| **Azione di mitigazione nel portale** | Rende necessaria l'autenticazione a più fattori per l'attivazione del ruolo con privilegi. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Il tenant non ha Azure AD Premium P2

| | |
| --- | --- |
| **Severity** | Basse |
| **Perché viene visualizzato questo avviso?** | Il tenant corrente non ha Azure AD Premium P2. |
| **Come correggerlo?** | Esaminare le informazioni sulle [edizioni di Azure AD](../fundamentals/active-directory-whatis.md). Eseguire l'aggiornamento ad Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Possibili account non aggiornati in un ruolo con privilegi

| | |
| --- | --- |
| **Severity** | Media |
| **Perché viene visualizzato questo avviso?** | Gli account in un ruolo con privilegi non hanno modificato la password negli ultimi 90 giorni. Questi account potrebbero essere account di servizio o condivisi che non vengono mantenuti aggiornati e sono vulnerabili agli attacchi. |
| **Come correggerlo?** | Esaminare gli account nell'elenco. Se l'accesso non è più necessario, rimuovere gli account dai ruoli con privilegi. |
| **Prevenzione** | Assicurarsi che per gli account condivisi venga eseguita la rotazione di password complesse quando cambiano gli utenti che conoscono la password. </br>Esaminare regolarmente gli account con ruoli con privilegi usando le [verifiche di accesso](pim-how-to-start-security-review.md) e rimuovere le assegnazioni di ruolo che non sono più necessarie. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Procedure consigliate** | Gli account di accesso condiviso, di servizio e di emergenza che eseguono l'autenticazione con una password e sono assegnati a ruoli amministrativi con privilegi elevati, ad esempio l'amministratore globale o l'amministratore della sicurezza, devono ruotare le password nei casi seguenti:<ul><li>Dopo un evento imprevisto per la sicurezza che comporta l'uso improprio o la violazione dei diritti di accesso amministrativo</li><li>Dopo la modifica dei privilegi di un utente in seguito alla quale l'utente non è più amministratore (ad esempio, dopo che un dipendente che era amministratore lascia il team IT o l'organizzazione)</li><li>A intervalli regolari (ad esempio, trimestralmente o annualmente), anche se non si è verificata alcuna violazione nota o modifica del personale IT</li></ul>Poiché più utenti hanno accesso alle credenziali di questi account, le credenziali devono essere ruotate per garantire che le persone che hanno lasciato i propri ruoli non possano più accedere all'account. [Ulteriori informazioni](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>I ruoli vengono assegnati all'esterno di Privileged Identity Management

| | |
| --- | --- |
| **Severity** | Alte |
| **Perché viene visualizzato questo avviso?** | Le assegnazioni di ruolo con privilegi eseguite all'esterno di Privileged Identity Management non sono monitorate correttamente e possono indicare un attacco attivo. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi assegnati al di fuori della Privileged Identity Management. |
| **Prevenzione** | Individuare la posizione in cui gli utenti vengono assegnati ai ruoli con privilegi al di fuori della Privileged Identity Management e proibire le assegnazioni future. |
| **Azione di mitigazione nel portale** | Rimuove l'utente dal ruolo con privilegi. |

### <a name="there-are-too-many-global-administrators"></a>Il numero di amministratori globali presenti è eccessivo

| | |
| --- | --- |
| **Severity** | Basse |
| **Perché viene visualizzato questo avviso?** | L'amministratore globale è il ruolo con privilegi più elevati. Se un amministratore globale è compromesso, l'autore dell'attacco ottiene l'accesso a tutte le relative autorizzazioni, in modo da mettere a rischio l'intero sistema. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuovere quelli che non richiedono assolutamente il ruolo di amministratore globale. </br>Assegnare invece ruoli con privilegi più bassi a questi utenti. |
| **Prevenzione** | Assegnare agli utenti il ruolo con privilegi minimo di cui hanno bisogno. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Trigger** | L'avviso viene attivato se sono soddisfatti due diversi criteri ed è possibile configurarli entrambi. In primo luogo, è necessario raggiungere una determinata soglia di amministratori globali. In secondo luogo, una determinata percentuale delle assegnazioni di ruolo totali deve essere un amministratore globale. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato. |
| **Numero minimo di amministratori globali** | Questa impostazione specifica il numero di amministratori globali, da 2 a 100, considerati insufficienti per l'organizzazione Azure AD. |
| **Percentuale di amministratori globali** | Questa impostazione consente di specificare la percentuale minima di amministratori globali, da 0% a 100%, al di sotto della quale non si desidera che l'organizzazione del Azure AD indip. |

### <a name="roles-are-being-activated-too-frequently"></a>I ruoli vengono attivati con una frequenza eccessiva

| | |
| --- | --- |
| **Severity** | Basse |
| **Perché viene visualizzato questo avviso?** | L'attivazione ripetuta dello stesso ruolo con privilegi da parte dello stesso utente è indicativa di un attacco. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività. |
| **Prevenzione** | Verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività.</br>[Richiedere l'autenticazione](pim-how-to-change-default-settings.md) a più fattori per i ruoli con privilegi che dispongono di account condivisi da più amministratori. |
| **Azione di mitigazione nel portale** | N/D |
| **Trigger** | Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo di tempo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni. |
| **Intervallo di tempo per il rinnovo delle attivazioni** | Questa impostazione specifica in giorni, ore, minuti e secondi il periodo per cui si vogliono rilevare i rinnovi sospetti. |
| **Numero di rinnovi di attivazioni** | Questa impostazione specifica il numero di attivazioni, da 2 a 100, in cui si desidera ricevere una notifica, entro l'intervallo di tempo scelto. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo. |

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

È possibile personalizzare alcuni avvisi di sicurezza in Privileged Identity Management per lavorare con le esigenze e gli obiettivi di sicurezza dell'organizzazione. Per aprire le impostazioni degli avvisi di sicurezza, seguire questi passaggi:

1. Aprire **Privileged Identity Management** in Azure ad.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **Impostazioni** e quindi **avvisi**.

    ![Ruoli di Azure AD-impostazioni con gli avvisi selezionati](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selezionare un nome per l'avviso per configurare l'impostazione per l'avviso.

    ![Per l'avviso selezionato, riquadro Impostazioni avvisi di sicurezza](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni del ruolo Azure AD in Privileged Identity Management](pim-how-to-change-default-settings.md)
