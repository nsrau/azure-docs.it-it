---
title: Come configurare i criteri di rischio utente in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di rischio utente in Azure AD Identity Protection.
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
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: c204c544e6e7062021eb30eb9f08abcd8f9c14fe
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170624"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Procedura: Configurare i criteri di rischio utente

Con il rischio utente, Azure AD rileva la probabilità che un account utente sia stato compromesso. Gli amministratori possono configurare criteri di accesso condizionale per il rischio utente per rispondere automaticamente a un determinato livello di rischio utente.
 
Questo articolo fornisce le informazioni necessarie per configurare i criteri di rischio utente.


## <a name="what-is-a-user-risk-policy"></a>Cosa sono i criteri di rischio utente?

Azure AD analizza ogni accesso di un utente. L'obiettivo dell'analisi è rilevare eventuali azioni sospette compiute contestualmente alla procedura di accesso. In Azure AD, le azioni sospette che il sistema è in grado di rilevare prendono il nome di "eventi di rischio". Mentre alcuni eventi di rischio possono essere rilevati in tempo reale, altri richiedono più tempo. Per rilevare un trasferimento impossibile in una posizione atipica, ad esempio, il sistema richiede un periodo di apprendimento iniziale di 14 giorni per capire il comportamento normale dell'utente. Sono disponibili varie opzioni per risolvere gli eventi di rischio rilevati. È possibile, ad esempio, risolvere manualmente singoli eventi di rischio oppure ricorrere a criteri di accesso condizionale per il rischio di accesso o il rischio utente.

Tutti gli eventi di rischio rilevati per un utente e non risolti prendono il nome di "eventi di rischio attivi". Con "rischio utente" si intendono gli eventi di rischio attivi associati a un utente. Sulla base del rischio utente, Azure AD calcola la probabilità (bassa, media, elevata) che un utente sia stato compromesso. Questa probabilità viene definita "livello di rischio utente".

![Rischi utente](./media/howto-user-risk-policy/1031.png)

I criteri di rischio utente sono costituiti da una risposta automatica che è possibile configurare per un determinato livello di rischio utente. Consentono, ad esempio, di bloccare l'accesso alle risorse o di richiedere una modifica della password per ripristinare un account utente a uno stato pulito.


## <a name="how-do-i-access-the-user-risk-policy"></a>Come si può accedere ai criteri di rischio utente?
   
I criteri di rischio di accesso si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di rischio utente](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Impostazioni dei criteri

Quando si configurano i criteri di rischio di accesso, è necessario impostare:

- Gli utenti e i gruppi a cui vengono applicati i criteri:

    ![Utenti e gruppi](./media/howto-user-risk-policy/11.png)

- Il livello di rischio di accesso che attiva i criteri:

    ![Livello di rischio utente](./media/howto-user-risk-policy/12.png)

- Il tipo di accesso che si intende applicare quando viene raggiunto il livello di rischio di accesso:  

    ![Accesso](./media/howto-user-risk-policy/13.png)

- Lo stato dei criteri:

    ![Applicare i criteri](./media/howto-user-risk-policy/14.png)

La finestra di dialogo di configurazione dei criteri include un'opzione che consente di stimare l'impatto della configurazione.

![Impatto stimato](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Informazioni utili

È possibile impostare criteri di sicurezza per il rischio utente per bloccare gli utenti al momento dell'accesso in base al livello di rischio.

![Blocco](./media/howto-user-risk-policy/16.png)


Il blocco dell'accesso:

* Impedisce la generazione di nuovi eventi di rischio utente per l'utente interessato
* Consente agli amministratori di correggere manualmente gli eventi di rischio che interessano l'identità dell'utente e di ripristinarne lo stato protetto

## <a name="best-practices"></a>Procedure consigliate

La scelta di una soglia **alta** riduce la frequenza di attivazione dei criteri e riduce al minimo l'impatto sugli utenti.
Esclude tuttavia dai criteri gli utenti contrassegnati per il rischio con una soglia **bassa** o **media**. Questa scelta può non garantire la protezione delle identità o dei dispositivi in precedenza sospettati di essere compromessi o noti come compromessi.

Quando si impostano i criteri:

* Escludere gli utenti che possono generare molti falsi positivi, ad esempio sviluppatori o analisti della sicurezza
* Escludere gli utenti con impostazioni locali in cui abilitare i criteri non è pratico, ad esempio per la mancanza di accesso al supporto tecnico
* Usare una soglia **alta** durante il rollout iniziale dei criteri o se è necessario ridurre al minimo gli avvisi visualizzati dagli utenti finali.
* Usare una soglia **bassa** se l'organizzazione richiede una maggiore sicurezza. La scelta di una soglia **bassa** introduce richieste di accesso aggiuntive per l'utente, ma garantisce una maggiore sicurezza.

L'impostazione predefinita consigliata per la maggior parte delle organizzazioni è la configurazione di una regola per una soglia **media** , che permette di bilanciare usabilità e sicurezza.

Per una panoramica dell'esperienza utente correlata, vedere:

* [Flusso di ripristino di account compromessi](flows.md#compromised-account-recovery).  
* [Flusso di account compromessi bloccati](flows.md#compromised-account-blocked).  

**Per aprire la relativa finestra di dialogo di configurazione**:

- Nel pannello **Azure AD Identity Protection** fare clic su **Criteri di rischio utente** nella sezione **Configura**.

    ![Criteri di rischio utente](./media/howto-user-risk-policy/1009.png "Criteri di rischio utente")




## <a name="next-steps"></a>Passaggi successivi

Per ottenere una panoramica di Azure AD Identity Protection, vedere l'articolo di [panoramica di Azure AD Identity Protection](overview.md).
