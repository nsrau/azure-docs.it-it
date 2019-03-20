---
title: Come configurare i criteri di rischio in Azure Active Directory Identity Protection (procedura aggiornata) | Microsoft Docs
description: Come configurare i criteri di rischio in Azure Active Directory Identity Protection (procedura aggiornata).
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestione applicazioni, sicurezza, rischio, livello di rischio, vulnerabilità, criteri di sicurezza
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b578c0187f7b804b6261fec9d299f83d7dc1832
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58015151"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Procedura: Configurare i criteri di rischio in Azure Active Directory Identity Protection (procedura aggiornata)


Azure AD rileva eventi di rischio indicanti identità potenzialmente compromesse. Configurando i criteri di rischio è possibile definire le risposte automatiche per i risultati di rilevamento:

- Con i criteri di rischio di accesso è possibile configurare una risposta per eventi di rischio in tempo reale rilevati durante l'accesso dell'utente. 
- Con i criteri di rischio utente è possibile configurare una risposta per tutti i rischi relativi a utenti attivi che sono stati rilevati per un utente nel corso del tempo.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]


## <a name="what-is-the-sign-in-risk-policy"></a>Cosa sono i criteri di rischio di accesso?

Azure AD analizza ogni accesso di un utente. L'obiettivo dell'analisi è rilevare eventuali azioni sospette compiute contestualmente alla procedura di accesso. Si controlla, ad esempio, se l'accesso viene eseguito usando un indirizzo IP anonimo o se viene avviato da una posizione insolita. In Azure AD, le azioni sospette che il sistema è in grado di rilevare prendono il nome di "eventi di rischio". In base agli eventi di rischio rilevati durante un accesso, Azure AD calcola un valore, che rappresenta la probabilità (bassa, media, elevata) che l'accesso non sia stato eseguito dall'utente legittimo. Questa probabilità viene definita **livello di rischio di accesso**.

I criteri di rischio di accesso sono costituiti da una risposta automatica che è possibile configurare per un determinato livello di rischio di accesso. Nella risposta è possibile bloccare l'accesso alle risorse o richiedere il passaggio a una richiesta di autenticazione a più fattori per ottenere l'accesso.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Com'è possibile accedere ai criteri di rischio di accesso?
   
I criteri di rischio di accesso si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di rischio di accesso](./media/howto-configure-risk-policies/1014.png "Criteri di rischio di accesso")


## <a name="sign-in-risk-policy-settings"></a>Impostazioni dei criteri di rischio di accesso

Quando si configurano i criteri di rischio di accesso, è necessario impostare:

- Gli utenti e i gruppi a cui vengono applicati i criteri:

    ![Utenti e gruppi](./media/howto-configure-risk-policies/11.png)

- Il livello di rischio di accesso che attiva i criteri:

    ![Livello di rischio di un accesso](./media/howto-configure-risk-policies/12.png)

- Il tipo di accesso che si intende applicare quando viene raggiunto il livello di rischio di accesso:  

    ![Accesso](./media/howto-configure-risk-policies/13.png)

- Lo stato dei criteri:

    ![Applicare i criteri](./media/howto-configure-risk-policies/14.png)


La finestra di dialogo di configurazione dei criteri include un'opzione che consente di stimare l'impatto della riconfigurazione.

![Impatto stimato](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Informazioni utili sui criteri di rischio di accesso

È possibile configurare i criteri di sicurezza per il rischio di accesso in modo da richiedere l'autenticazione a più fattori (MFA):

![Richiedere l'autenticazione MFA](./media/howto-configure-risk-policies/16.png)

Per motivi di sicurezza, tuttavia, questa impostazione funziona soltanto per gli utenti che sono già stati registrati per l'autenticazione a più fattori. Identity Protection blocca gli utenti con requisiti di autenticazione MFA non ancora registrati per l'autenticazione MFA.

Per richiedere l'autenticazione MFA per gli accessi a rischio, è necessario:

1. Abilitare i criteri di registrazione per l'autenticazione a più fattori per gli utenti interessati.

2. Richiedere agli utenti interessati di accedere in una sessione non rischiosa per eseguire la registrazione per l'autenticazione a più fattori.

L'esecuzione di questa procedura assicura che, in caso di accesso rischioso, venga richiesta l'autenticazione a più fattori.

I criteri di rischio di accesso:

- Vengono applicati a tutto il traffico tramite browser e agli accessi che usano l'autenticazione moderna.

- Non vengono applicati alle applicazioni che usano protocolli di sicurezza meno recenti disabilitando l'endpoint WS-Trust in corrispondenza dell'IDP federato, ad esempio ADFS.


Per una panoramica dell'esperienza utente correlata, vedere:

* [Ripristino di un accesso rischioso](flows.md#risky-sign-in-recovery)
* [Accesso rischioso bloccato](flows.md#risky-sign-in-blocked)  
* [Esperienze di accesso con Azure AD Identity Protection](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>Cosa sono i criteri di rischio utente?

Azure AD analizza ogni accesso di un utente. L'obiettivo dell'analisi è rilevare eventuali azioni sospette compiute contestualmente alla procedura di accesso. In Azure AD, le azioni sospette che il sistema è in grado di rilevare prendono il nome di "eventi di rischio". Mentre alcuni eventi di rischio possono essere rilevati in tempo reale, altri richiedono più tempo. Per rilevare un trasferimento impossibile in una posizione atipica, ad esempio, il sistema richiede un periodo di apprendimento iniziale di 14 giorni per capire il comportamento normale dell'utente. Sono disponibili varie opzioni per risolvere gli eventi di rischio rilevati. È possibile, ad esempio, risolvere manualmente singoli eventi di rischio oppure ricorrere a criteri di accesso condizionale per il rischio di accesso o il rischio utente.

Tutti gli eventi di rischio rilevati per un utente e non risolti prendono il nome di "eventi di rischio attivi". Con "rischio utente" si intendono gli eventi di rischio attivi associati a un utente. Sulla base del rischio utente, Azure AD calcola la probabilità (bassa, media, elevata) che un utente sia stato compromesso. Questa probabilità viene definita "livello di rischio utente".

![Rischi utente](./media/howto-configure-risk-policies/11031.png)

I criteri di rischio utente sono costituiti da una risposta automatica che è possibile configurare per un determinato livello di rischio utente. Consentono, ad esempio, di bloccare l'accesso alle risorse o di richiedere una modifica della password per ripristinare un account utente a uno stato pulito.


## <a name="how-do-i-access-the-user-risk-policy"></a>Come si può accedere ai criteri di rischio utente?
   
I criteri di rischio utente si trovano nella sezione **Configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di rischio utente](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Impostazioni dei criteri di rischio utente

Quando si configurano i criteri di rischio utente, è necessario impostare:

- Gli utenti e i gruppi a cui vengono applicati i criteri:

    ![Utenti e gruppi](./media/howto-configure-risk-policies/111.png)

- Il livello di rischio di accesso che attiva i criteri:

    ![Livello di rischio utente](./media/howto-configure-risk-policies/112.png)

- Il tipo di accesso che si intende applicare quando viene raggiunto il livello di rischio di accesso:  

    ![Accesso](./media/howto-configure-risk-policies/113.png)

- Lo stato dei criteri:

    ![Applicare i criteri](./media/howto-configure-risk-policies/114.png)

La finestra di dialogo di configurazione dei criteri include un'opzione che consente di stimare l'impatto della configurazione.

![Impatto stimato](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Informazioni utili sui criteri di rischio utente

È possibile impostare criteri di sicurezza per il rischio utente per bloccare gli utenti al momento dell'accesso in base al livello di rischio.

![Blocco](./media/howto-configure-risk-policies/116.png)


Il blocco dell'accesso:

* Impedisce la generazione di nuovi eventi di rischio utente per l'utente interessato
* Consente agli amministratori di correggere manualmente gli eventi di rischio che interessano l'identità dell'utente e di ripristinarne lo stato protetto


























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

 [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

