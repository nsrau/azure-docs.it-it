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
ms.component: pim
ms.date: 11/01/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e7204c223681b9a33c439b0d9fc653167422384a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011698"
---
# <a name="configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Configurare gli avvisi di sicurezza per i ruoli della directory di Azure AD in PIM

Azure AD Privileged Identity Management (PIM) genera avvisi quando si verificano attività sospette o non sicure nell'ambiente. Una avviso attivato viene visualizzato nel dashboard di PIM. Selezionare l'avviso per visualizzare un report che elenca gli utenti o i ruoli che hanno attivato l'avviso.

![Avvisi di sicurezza di PIM - screenshot](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Avvisi di sicurezza

Questa sezione elenca tutti gli avvisi di sicurezza per i ruoli della directory, oltre a fornire indicazioni su come risolverli ed evitarli. Ecco il significato dei vari livelli di gravità:

* **Elevata**: richiede un'azione immediata a causa di una violazione dei criteri.
* **Media**: non richiede un'azione immediata ma segnala una potenziale violazione dei criteri.
* **Bassa**: non richiede un'azione immediata ma suggerisce una modifica dei criteri.

### <a name="roles-are-being-assigned-outside-of-pim"></a>È in corso l'assegnazione di ruoli all'esterno di PIM

| | |
| --- | --- |
| **Gravità** | Elevata |
| **Perché viene visualizzato questo avviso?** | Le assegnazioni di ruoli con privilegi eseguite all'esterno di PIM non vengono monitorate in modo corretto e possono essere indicative di un attacco in corso. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi assegnati all'esterno di PIM. |
| **Prevenzione** | Analizzare la posizione in cui vengono assegnati ruoli con privilegi all'esterno di PIM e impedire assegnazioni future da tale posizione. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Possibili account non aggiornati in un ruolo con privilegi

| | |
| --- | --- |
| **Gravità** | Media |
| **Perché viene visualizzato questo avviso?** | Gli account per cui non è stata cambiata la password di recente possono essere account di servizio o condivisi che non sono gestiti. Questi account nei ruoli con privilegi sono vulnerabili ad attacchi. |
| **Come correggerlo?** | Esaminare gli account nell'elenco. Se l'accesso non è più necessario, rimuovere gli account dai ruoli con privilegi. |
| **Prevenzione** | Assicurarsi che per gli account condivisi venga eseguita la rotazione di password complesse quando cambiano gli utenti che conoscono la password. </br>Esaminare regolarmente gli account nei ruoli con privilegi usando le verifiche di accesso e rimuovere le assegnazioni di ruolo che non sono più necessarie. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |

### <a name="users-arent-using-their-privileged-roles"></a>Gli utenti non usano i propri ruoli con privilegi

| | |
| --- | --- |
| **Gravità** | Bassa |
| **Perché viene visualizzato questo avviso?** | Se sono presenti utenti a cui sono stati assegnati ruoli con privilegi non necessari, le probabilità di un attacco aumentano. Inoltre, gli utenti malintenzionati possono passare più facilmente inosservati in account che non sono attivamente in uso. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e rimuoverli dai ruoli con privilegi di cui non hanno bisogno. |
| **Prevenzione** | Assegnare ruoli con privilegi solo agli utenti che hanno una motivazione aziendale. </br>Pianificare verifiche di accesso regolari per determinare se gli utenti hanno ancora bisogno dell'accesso. |
| **Azione di mitigazione nel portale** | Rimuove l'account dal ruolo con privilegi. |
| **Trigger** | L'avviso viene attivato se un utente fa trascorrere una certa quantità di tempo senza attivare un ruolo. |
| **Numero di giorni** | Questa impostazione specifica il numero di giorni, da 0 a 100, che possono trascorrere senza che un utente attivi un ruolo.|

### <a name="there-are-too-many-global-administrators"></a>Il numero di amministratori globali presenti è eccessivo

| | |
| --- | --- |
| **Gravità** | Bassa |
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
| **Gravità** | Bassa |
| **Perché viene visualizzato questo avviso?** | L'attivazione ripetuta dello stesso ruolo con privilegi da parte dello stesso utente è indicativa di un attacco. |
| **Come correggerlo?** | Esaminare gli utenti nell'elenco e verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività. |
| **Prevenzione** | Verificare che la [durata di attivazione](pim-how-to-change-default-settings.md) dei ruoli con privilegi sia sufficiente per consentire agli utenti di eseguire le proprie attività.</br>[Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per i ruoli con privilegi che dispongono di account condivisi da più amministratori. |
| **Azione di mitigazione nel portale** | N/D |
| **Trigger** | Questo avviso viene attivato se un utente attiva lo stesso ruolo con privilegi più volte entro l'intervallo di tempo specificato. È possibile configurare il periodo di tempo e il numero di attivazioni. |
| **Intervallo di tempo per il rinnovo delle attivazioni** | Questa impostazione specifica in giorni, ore, minuti e secondi il periodo per cui si vogliono rilevare i rinnovi sospetti. |
| **Numero di rinnovi di attivazioni** | Questa impostazione specifica il numero di attivazioni, da 2 a 100, considerati idonei per un avviso, entro l'intervallo di tempo specificato. È possibile modificare questa impostazione spostando il dispositivo di scorrimento o digitando un numero nella casella di testo. |

### <a name="roles-dont-require-mfa-for-activation"></a>I ruoli non richiedono l'autenticazione a più fattori per l'attivazione

| | |
| --- | --- |
| **Gravità** | Bassa |
| **Perché viene visualizzato questo avviso?** | Senza l'autenticazione a più fattori, gli utenti compromessi possono attivare ruoli con privilegi. |
| **Come correggerlo?** | Esaminare l'elenco dei ruoli e [richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo. |
| **Prevenzione** | [Richiedere l'autenticazione a più fattori](pim-how-to-change-default-settings.md) per ogni ruolo.  |
| **Azione di mitigazione nel portale** | Rende obbligatoria l'autenticazione a più fattori per l'attivazione del ruolo con privilegi. |

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
