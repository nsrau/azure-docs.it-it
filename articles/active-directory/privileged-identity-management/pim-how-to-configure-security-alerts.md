---
title: Configurare gli avvisi di sicurezza per i ruoli della directory di Azure AD in PIM | Microsoft Docs
description: Informazioni su come configurare gli avvisi di sicurezza per i ruoli della directory di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/04/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2cb78d048559a011756362b58848c5e91ca40dd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172383"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Configurare gli avvisi di sicurezza per i ruoli della directory di Azure AD in PIM

Azure AD Privileged Identity Management (PIM) genera avvisi quando si verificano attività sospette o non sicure nell'ambiente. Una avviso attivato viene visualizzato nel dashboard di PIM. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

![Avvisi di sicurezza di PIM - screenshot](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

Questa sezione elenca tutti gli avvisi di sicurezza per i ruoli della directory, oltre a fornire indicazioni su come risolverli ed evitarli. Ecco il significato dei vari livelli di gravità:

* **Alta**: richiede un'azione immediata a causa di una violazione dei criteri.
* **Medium**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

### <a name="administrators-arent-using-their-privileged-roles"></a>Gli amministratori non usano i ruoli con privilegi

| | |
| --- | --- |
| **Severity** | Basso |
| **Perché viene visualizzato questo avviso?** | Se sono presenti utenti a cui sono stati assegnati ruoli con privilegi non necessari, le probabilità di un attacco aumentano. Inoltre, gli utenti malintenzionati possono passare più facilmente inosservati in account che non sono attivamente in uso. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi di cui non hanno bisogno. |
| **Prevenzione** | Assegnare ruoli con privilegi solo agli utenti che hanno una motivazione aziendale. </br>Pianificare [verifiche di accesso](pim-how-to-start-security-review.md) regolari per determinare se gli utenti hanno ancora bisogno dell'accesso. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Trigger** | L'avviso viene attivato se un utente fa trascorrere una certa quantità di tempo senza attivare un ruolo. |
| **Numero di giorni** | Questa impostazione specifica il numero di giorni, da 0 a 100, che possono trascorrere senza che un utente attivi un ruolo.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>I ruoli non richiedono l'autenticazione a più fattori per l'attivazione

| | |
| --- | --- |
| **Severity** | Basso |
| **Perché viene visualizzato questo avviso?** | Senza l'autenticazione a più fattori, gli utenti compromessi possono attivare ruoli con privilegi. |
| **Come correggerlo?** | Esaminare l'elenco dei ruoli e [richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo. |
| **Prevenzione** | [Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo.  |
| **Azione di mitigazione nel portale** | Rende obbligatoria l'autenticazione a più fattori per l'attivazione del ruolo con privilegi. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>Il tenant non ha Azure AD Premium P2

| | |
| --- | --- |
| **Severity** | Basso |
| **Perché viene visualizzato questo avviso?** | Il tenant corrente non ha Azure AD Premium P2. |
| **Come correggerlo?** | Esaminare le informazioni sulle [edizioni di Azure AD](../fundamentals/active-directory-whatis.md). Eseguire l'aggiornamento ad Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Possibili account non aggiornati in un ruolo con privilegi

| | |
| --- | --- |
| **Severity** | Media |
| **Perché viene visualizzato questo avviso?** | Account in un ruolo con privilegi che non hanno cambiato la password negli ultimi 90 giorni. Questi account potrebbero essere account di servizio o condivisi che non vengono mantenuti aggiornati e sono vulnerabili agli attacchi. |
| **Come correggerlo?** | Esaminare gli account nell'elenco. Se l'accesso non è più necessario, rimuovere gli account dai ruoli con privilegi. |
| **Prevenzione** | Assicurarsi che per gli account condivisi venga eseguita la rotazione di password complesse quando cambiano gli utenti che conoscono la password. </br>Esaminare regolarmente gli account con ruoli con privilegi usando le [verifiche di accesso](pim-how-to-start-security-review.md) e rimuovere le assegnazioni di ruolo che non sono più necessarie. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Procedure consigliate** | Gli account condivisi, di servizio e di accesso di emergenza che eseguono l'autenticazione usando una password e sono assegnati a ruoli amministrativi con privilegi elevati, come amministratore globale o amministratore della sicurezza, richiedono la rotazione della password nei casi seguenti:<ul><li>Dopo un evento imprevisto per la sicurezza che comporta l'uso improprio o la violazione dei diritti di accesso amministrativo</li><li>Dopo la modifica dei privilegi di un utente in seguito alla quale l'utente non è più amministratore (ad esempio, dopo che un dipendente che era amministratore lascia il team IT o l'organizzazione)</li><li>A intervalli regolari (ad esempio, trimestralmente o annualmente), anche se non si è verificata alcuna violazione nota o modifica del personale IT</li></ul>Poiché più utenti hanno accesso alle credenziali di questi account, le credenziali devono essere ruotate per garantire che le persone che hanno lasciato i propri ruoli non possano più accedere all'account. [Altre informazioni](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-pim"></a>È in corso l'assegnazione di ruoli all'esterno di PIM

| | |
| --- | --- |
| **Severity** | Alto |
| **Perché viene visualizzato questo avviso?** | Le assegnazioni di ruoli con privilegi eseguite all'esterno di PIM non vengono monitorate in modo corretto e possono essere indicative di un attacco in corso. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi assegnati all'esterno di PIM. |
| **Prevenzione** | Analizzare la posizione in cui vengono assegnati ruoli con privilegi all'esterno di PIM e impedire assegnazioni future da tale posizione. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |

### <a name="there-are-too-many-global-administrators"></a>Il numero di amministratori globali presenti è eccessivo

| | |
| --- | --- |
| **Severity** | Basso |
| **Perché viene visualizzato questo avviso?** | Amministratore globale è il ruolo con privilegi più elevato. Se un amministratore globale è compromesso, l'utente malintenzionato ottiene l'accesso a tutte le relative autorizzazioni, ponendo così a rischio l'intero sistema. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuovere quelli per cui non è assolutamente necessario il ruolo di amministratore globale. </br>Assegnare ruoli con privilegi più bassi a tali utenti. |
| **Prevenzione** | Assegnare agli utenti il ruolo con privilegi minimo di cui hanno bisogno. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Trigger** | L'avviso viene attivato se sono soddisfatti due diversi criteri ed è possibile configurarli entrambi. In primo luogo, è necessario raggiungere una determinata soglia di amministratori globali. In secondo luogo, una determinata percentuale delle assegnazioni di ruolo totali deve essere rappresentata da amministratori globali. Se viene soddisfatto uno solo di questi criteri, l'avviso non verrà visualizzato. |
| **Numero minimo di amministratori globali** | Questa impostazione specifica il numero di amministratori globali, da 2 a 100, che si considera non sicuro. |
| **Percentuale di amministratori globali** | Questa impostazione specifica la percentuale minima di amministratori globali, da 0% a 100%, che si considera non sicura nell'ambiente. |

### <a name="roles-are-being-activated-too-frequently"></a>I ruoli vengono attivati con una frequenza eccessiva

| | |
| --- | --- |
| **Severity** | Basso |
| **Perché viene visualizzato questo avviso?** | L'attivazione ripetuta dello stesso ruolo con privilegi da parte dello stesso utente è indicativa di un attacco. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività. |
| **Prevenzione** | Verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività.</br>[Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per i ruoli con privilegi che dispongono di account condivisi da più amministratori. |
| **Azione di mitigazione nel portale** | N/D |
| **Trigger** | Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo di tempo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni. |
| **Intervallo di tempo per il rinnovo delle attivazioni** | Questa impostazione specifica in giorni, ore, minuti e secondi il periodo per cui si vogliono rilevare i rinnovi sospetti. |
| **Numero di rinnovi di attivazioni** | Questa impostazione specifica il numero di attivazioni, da 2 a 100, considerati idonei per un avviso, entro l'intervallo di tempo specificato. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo. |

## <a name="configure-security-alert-settings"></a>Configurare le impostazioni degli avvisi di sicurezza

È possibile personalizzare alcuni avvisi di sicurezza in PIM in linea con gli obiettivi di protezione e l'ambiente. Per aprire le impostazioni degli avvisi di sicurezza, seguire questi passaggi:

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **Impostazioni** e quindi su **Avvisi**.

    ![Passare a impostazioni degli avvisi di sicurezza](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Fare clic sul nome di un avviso per configurarne l'impostazione.

    ![Impostazioni degli avvisi di sicurezza](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le impostazioni dei ruoli della directory di Azure AD in PIM](pim-how-to-change-default-settings.md)
