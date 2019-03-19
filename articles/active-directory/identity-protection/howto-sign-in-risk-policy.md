---
title: Come configurare i criteri di rischio di accesso in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di rischio di accesso in Azure AD Identity Protection.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: da3f03cfcf821fa093e9f21cf9a11a2d99d67683
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999032"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Procedura: Configurare i criteri di rischio di accesso

Azure Active Directory rileva i [tipi di eventi di rischio](../reports-monitoring/concept-risk-events.md#risk-event-types) in tempo reale e offline. Ogni evento di rischio in tempo reale rilevato per un accesso di un utente rientra nel concetto logico degli accessi a rischio. Un accesso a rischio è indicativo di un tentativo di accesso che potrebbe non essere stato eseguito dal legittimo proprietario di un account utente.


## <a name="what-is-the-sign-in-risk-policy"></a>Cosa sono i criteri di rischio di accesso?

Azure AD analizza ogni accesso di un utente. L'obiettivo dell'analisi è rilevare eventuali azioni sospette compiute contestualmente alla procedura di accesso. Si controlla, ad esempio, se l'accesso viene eseguito usando un indirizzo IP anonimo o se viene avviato da una posizione insolita. In Azure AD, le azioni sospette che il sistema è in grado di rilevare prendono il nome di "eventi di rischio". In base agli eventi di rischio rilevati durante un accesso, Azure AD calcola un valore, che rappresenta la probabilità (bassa, media, elevata) che l'accesso non sia stato eseguito dall'utente legittimo. Questa probabilità viene definita **livello di rischio di accesso**.

I criteri di rischio di accesso sono costituiti da una risposta automatica che è possibile configurare per un determinato livello di rischio di accesso. Nella risposta è possibile bloccare l'accesso alle risorse o richiedere il passaggio a una richiesta di autenticazione a più fattori per ottenere l'accesso.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Com'è possibile accedere ai criteri di rischio di accesso?
   
I criteri di rischio di accesso si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di rischio di accesso](./media/howto-sign-in-risk-policy/1014.png "Criteri di rischio di accesso")


## <a name="policy-settings"></a>Impostazioni dei criteri

Quando si configurano i criteri di rischio di accesso, è necessario impostare:

- Gli utenti e i gruppi a cui vengono applicati i criteri:

    ![Utenti e gruppi](./media/howto-sign-in-risk-policy/11.png)

- Il livello di rischio di accesso che attiva i criteri:

    ![Livello di rischio di un accesso](./media/howto-sign-in-risk-policy/12.png)

- Il tipo di accesso che si intende applicare quando viene raggiunto il livello di rischio di accesso:  

    ![Accesso](./media/howto-sign-in-risk-policy/13.png)

- Lo stato dei criteri:

    ![Applicare i criteri](./media/howto-sign-in-risk-policy/14.png)


La finestra di dialogo di configurazione dei criteri include un'opzione che consente di stimare l'impatto della riconfigurazione.

![Impatto stimato](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Informazioni utili

È possibile configurare i criteri di sicurezza per il rischio di accesso in modo da richiedere l'autenticazione a più fattori (MFA):

![Richiedere l'autenticazione MFA](./media/howto-sign-in-risk-policy/16.png)

Per motivi di sicurezza, tuttavia, questa impostazione funziona soltanto per gli utenti che sono già stati registrati per l'autenticazione a più fattori. Identity Protection blocca gli utenti con requisiti di autenticazione MFA non ancora registrati per l'autenticazione MFA.

Per richiedere l'autenticazione MFA per gli accessi a rischio, è necessario:

1. Abilitare il [criterio di registrazione per l'autenticazione a più fattori](howto-mfa-policy.md) per gli utenti interessati.

2. Richiedere agli utenti interessati di accedere in una sessione non rischiosa per eseguire la registrazione per l'autenticazione MFA.

L'esecuzione di questa procedura assicura che, in caso di accesso rischioso, venga richiesta l'autenticazione a più fattori.

I criteri di rischio di accesso:

- Vengono applicati a tutto il traffico tramite browser e agli accessi che usano l'autenticazione moderna.

- Non vengono applicati alle applicazioni che usano protocolli di sicurezza meno recenti disabilitando l'endpoint WS-Trust in corrispondenza dell'IDP federato, ad esempio ADFS.


Per una panoramica dell'esperienza utente correlata, vedere:

* [Ripristino di un accesso rischioso](flows.md#risky-sign-in-recovery)
* [Accesso rischioso bloccato](flows.md#risky-sign-in-blocked)  
* [Esperienze di accesso con Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Procedure consigliate

La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti.  

Tuttavia, esclude dai criteri gli accessi contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta può non impedire a un utente malintenzionato di sfruttare un'identità compromessa.

Quando si impostano i criteri:

- Escludere gli utenti non hanno o non possono avere l'autenticazione a più fattori

- Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico

- Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza

- Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo gli avvisi visualizzati dagli utenti finali.

- Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce richieste di accesso aggiuntive per l'utente, ma garantisce una maggiore sicurezza.

L'impostazione predefinita consigliata per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media** , che permette di bilanciare usabilità e sicurezza.






## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
