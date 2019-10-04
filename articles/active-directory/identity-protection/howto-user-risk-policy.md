---
title: Come configurare i criteri di rischio utente in Azure Active Directory Identity Protection | Microsoft Docs
description: Informazioni su come configurare i criteri di rischio utente in Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92bfb921833d99a3538ffa8c4c5d16a9f0cd3acd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126292"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Procedura: Configurare i criteri di rischio utente

Con il rischio utente, Azure AD rileva la probabilità che un account utente sia stato compromesso. In qualità di amministratore, è possibile configurare i criteri di accesso condizionale per il rischio utente per rispondere automaticamente a un livello di rischio utente specifico.
 
Questo articolo fornisce le informazioni necessarie per configurare i criteri di rischio utente.

## <a name="what-is-a-user-risk-policy"></a>Cosa sono i criteri di rischio utente?

Azure AD analizza ogni accesso di un utente. L'obiettivo dell'analisi è rilevare eventuali azioni sospette compiute contestualmente alla procedura di accesso. In Azure AD, le azioni sospette che il sistema è in grado di rilevare sono note anche come rilevamenti dei rischi. Sebbene sia possibile rilevare alcuni rilevamenti dei rischi in tempo reale, esistono anche rilevamenti di rischio che richiedono più tempo. Per rilevare un trasferimento impossibile in una posizione atipica, ad esempio, il sistema richiede un periodo di apprendimento iniziale di 14 giorni per capire il comportamento normale dell'utente. Sono disponibili diverse opzioni per la risoluzione del rilevamento del rischio rilevato. Ad esempio, è possibile risolvere manualmente i singoli rilevamenti dei rischi oppure è possibile risolverli usando un rischio di accesso o un criterio di accesso condizionale a rischio utente.

Tutti i rilevamenti dei rischi rilevati per un utente che non sono stati risolti sono noti come rilevamenti dei rischi attivi. I rilevamenti dei rischi attivi associati a un utente sono noti come rischi utente. Sulla base del rischio utente, Azure AD calcola la probabilità (bassa, media, elevata) che un utente sia stato compromesso. Questa probabilità viene definita "livello di rischio utente".

![Rischi utente](./media/howto-user-risk-policy/1031.png)

I criteri di rischio utente sono costituiti da una risposta automatica che è possibile configurare per un determinato livello di rischio utente. Consentono, ad esempio, di bloccare l'accesso alle risorse o di richiedere una modifica della password per ripristinare un account utente a uno stato pulito.

## <a name="how-do-i-access-the-user-risk-policy"></a>Come si può accedere ai criteri di rischio utente?
   
I criteri di rischio di accesso si trovano nella sezione di **configurazione** della [pagina di Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Criteri di rischio utente](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Impostazioni di criteri

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

* Impedisce la generazione di nuovi rilevamenti dei rischi utente per l'utente interessato
* Consente agli amministratori di correggere manualmente i rilevamenti dei rischi che interessano l'identità dell'utente e di ripristinarli in uno stato sicuro

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
