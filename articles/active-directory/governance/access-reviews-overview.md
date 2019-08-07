---
title: Informazioni sulle verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Usando le verifiche di accesso di Azure Active Directory, è possibile controllare l'appartenenza ai gruppi e l'accesso alle applicazioni per soddisfare le iniziative di governance, gestione dei rischi e conformità dell'organizzazione.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6b310e2ca2c19bf4b163704627943a881501bd
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823845"
---
# <a name="what-are-azure-ad-access-reviews"></a>Informazioni sulle verifiche di accesso Azure AD

Le verifiche di accesso Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Il video seguente presenta una rapida panoramica delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Perché le verifiche di accesso sono importanti?

Azure AD consente di collaborare internamente nell'organizzazione e con utenti di organizzazioni esterne, ad esempio i partner. Gli utenti possono unirsi ai gruppi, invitare utenti guest, connettersi alle app cloud e lavorare in remoto dai loro dispositivi personali o di lavoro. La praticità di sfruttare le capacità self-service ha portato alla necessità di funzionalità migliori di gestione dell'accesso.

- Quando viene assunto un nuovo dipendente, come è possibile garantire che abbia i diritti di accesso appropriati per lavorare in modo produttivo?
- Quando le persone passano da un team a un altro o lasciano l'azienda, come è possibile garantire che l'accesso precedente venga rimosso, in particolare se riguarda utenti guest?
- Diritti di accesso eccessivi possono portare a violazioni e mancato superamento dei controlli, in quanto indicano la mancanza di controllo sull'accesso.
- È necessario coinvolgere in modo proattivo i proprietari delle risorse per assicurarsi che verifichino regolarmente chi può accedere alle loro risorse.

## <a name="when-to-use-access-reviews"></a>Quando è opportuno usare le verifiche di accesso?

- **Troppi utenti nei ruoli con privilegi:** È consigliabile controllare il numero di utenti che dispongono di accesso amministrativo, il numero di amministratori globali e se sono presenti Guest o partner invitati che non sono stati rimossi dopo essere stati assegnati per eseguire un'attività amministrativa. È possibile ricertificare gli utenti di assegnazione di ruolo in [Azure ad ruoli](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , ad esempio gli amministratori globali, o i [ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , ad esempio amministratore accesso utenti nell'esperienza [Azure ad Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **Quando l'automazione non è realizzabile:** è possibile creare regole per l'appartenenza dinamica ai gruppi di sicurezza o ai gruppi di Office 365, ma cosa accade se i dati delle risorse umane non si trovano in Azure AD o se gli utenti necessitano ancora dell'accesso dopo aver lasciato il gruppo per aiutare nella formazione del loro sostituto? È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati cruciale per l'azienda:** per alcune risorse, potrebbe essere necessario chiedere agli utenti esterni di disconnettersi regolarmente e fornire una giustificazione per i motivi per cui è necessario accedere ai fini del controllo.
- **Per mantenere l'elenco eccezioni dei criteri:** In un mondo ideale, tutti gli utenti seguono i criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedere ai proprietari dei gruppi di confermare che hanno ancora bisogno di guest nei loro gruppi:** L'accesso dei dipendenti può essere automatizzato con alcune pagine IAM locali, ma non Guest invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di ciò che si vuole rivedere, si creerà la verifica di accesso in Azure AD verifiche di accesso, Azure AD app aziendali (in anteprima) o Azure AD PIM.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Revisione automatica | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Riquadro di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Revisione automatica | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Riquadro di accesso |
| Ruolo Azure AD | Revisori specificati</br>Revisione automatica | [PIM Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Revisione automatica | [PIM Azure AD](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |

## <a name="onboard-access-reviews"></a>Onboarding delle verifiche di accesso

Per eseguire l'onboarding delle verifiche di accesso, seguire questa procedura.

1. In qualità di amministratore globale o Amministratore utenti, accedere al [portale di Azure](https://portal.azure.com) in cui si desidera utilizzare le verifiche di accesso.

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**.

1. Nel menu a sinistra fare clic su **governance identità**.

1. Fare clic su verifiche di **accesso**.
 
    ![Pagina iniziale delle verifiche di accesso](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Nella pagina fare clic sul pulsante **onboard Now** .
    
      ![Onboarding delle verifiche di accesso](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Informazioni sulle verifiche di accesso

Per altre informazioni sulla creazione e sull'esecuzione di verifiche di accesso, guardare questa breve demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se si è pronti per distribuire le verifiche di accesso nell'organizzazione, seguire questi passaggi nel video per l'onboarding, la formazione degli amministratori e la creazione della prima verifica di accesso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>Quali utenti devono avere le licenze?

Ogni utente che interagisce con le verifiche di accesso deve disporre di una licenza a pagamento Azure AD Premium P2. Ecco alcuni esempi:

- Amministratori che creano una verifica di accesso
- Proprietari del gruppo che eseguono una verifica di accesso
- Utenti assegnati come revisori
- Utenti che eseguono una verifica automatica

È anche possibile chiedere agli utenti guest di verificare il proprio accesso. Per ogni licenza Azure AD Premium P2 a pagamento assegnata a uno degli utenti dell'organizzazione, è possibile usare Azure AD business-to-business (B2B) per invitare fino a cinque utenti Guest sotto la franchigia utente esterna. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere [Azure ad Guida alle licenze di collaborazione B2B](../b2b/licensing-guidance.md).

Di seguito sono riportati alcuni scenari di esempio che consentono di determinare il numero di licenze che è necessario avere.

| Scenario | Calcolo | Numero necessario di licenze |
| --- | --- | --- |
| Un amministratore crea una verifica di accesso del gruppo A con 500 utenti. Assegna 3 proprietari del gruppo come revisori. | 1 licenza per l'amministratore + 3 licenze per ogni proprietario del gruppo come revisori. | 4 |
| Un amministratore crea una verifica di accesso del gruppo A con 500 utenti. Consente di riesaminare autonomamente. | 1 licenza per l'amministratore + 500 licenze per ogni utente come revisori. | 501 |
| Un amministratore crea una verifica di accesso del gruppo B con 5 utenti e 25 utenti guest. Consente di riesaminare autonomamente. | 1 licenza per l'amministratore + 5 licenze per ogni utente come revisori.<br/>(gli utenti Guest sono coperti dal rapporto 1:5 richiesto) | 6 |
| Un amministratore crea una verifica di accesso del gruppo C con 5 utenti e 108 utenti guest. Consente di riesaminare autonomamente. | 1 licenza per l'amministratore + 5 licenze per ogni utente come revisori autonomi + 16 licenze aggiuntive per coprire tutti gli utenti guest di 108 nel rapporto 1:5 richiesto.<br/>1 + 5 = 6 licenze, che coprono 5\*6 = 30 utenti guest. Per gli altri (108-5\*6) = 78 utenti guest, 78/5 = 16 licenze aggiuntive sono obbligatorie. Quindi, in totale, sono necessarie 6 + 16 = 22 licenze. | 22 |

Per informazioni su come assegnare licenze agli utenti, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
