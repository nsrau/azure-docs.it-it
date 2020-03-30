---
title: Informazioni sulle verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Usando le verifiche di accesso ad Azure Active Directory, è possibile controllare l'appartenenza ai gruppi e l'accesso alle applicazioni per soddisfare le iniziative di governance, gestione dei rischi e conformità nell'organizzazione.
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
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262087"
---
# <a name="what-are-azure-ad-access-reviews"></a>Cosa sono le verifiche di accesso di Azure AD?

Le verifiche di accesso ad Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Il video seguente presenta una rapida panoramica delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Perché le verifiche di accesso sono importanti?

Azure AD consente di collaborare internamente nell'organizzazione e con utenti di organizzazioni esterne, ad esempio i partner. Gli utenti possono unirsi ai gruppi, invitare utenti guest, connettersi alle app cloud e lavorare in remoto dai loro dispositivi personali o di lavoro. La praticità di sfruttare le capacità self-service ha portato alla necessità di funzionalità migliori di gestione dell'accesso.

- Quando viene assunto un nuovo dipendente, come è possibile garantire che abbia i diritti di accesso appropriati per lavorare in modo produttivo?
- Quando le persone passano da un team a un altro o lasciano l'azienda, come è possibile garantire che l'accesso precedente venga rimosso, in particolare se riguarda utenti guest?
- Diritti di accesso eccessivi possono portare a violazioni e mancato superamento dei controlli, in quanto indicano la mancanza di controllo sull'accesso.
- È necessario coinvolgere in modo proattivo i proprietari delle risorse per assicurarsi che verifichino regolarmente chi può accedere alle loro risorse.

## <a name="when-to-use-access-reviews"></a>Quando è opportuno usare le verifiche di accesso?

- **Troppi utenti con ruoli privilegiati:** È consigliabile verificare il numero di utenti che hanno accesso amministrativo, il numero di utenti sono amministratori globali e se sono presenti ospiti invitati o partner che non sono stati rimossi dopo l'assegnazione a un'attività amministrativa. È possibile ricertificare le assegnazioni di ruolo degli utenti nei ruoli di [Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) ad esempio Amministratori globali o Ruoli delle risorse di [Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) ad esempio Amministratore accesso utenti nell'esperienza [di Azure AD Privileged Identity Management (PIM).](../privileged-identity-management/pim-configure.md)
- **Quando l'automazione è impraticabile:** È possibile creare regole per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Office 365, ma cosa succede se i dati delle risorse umane non sono in Azure AD o se gli utenti devono ancora accedere dopo aver lasciato il gruppo per eseguire il training della sostituzione? È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati business critical:** per determinate risorse, potrebbe essere necessario chiedere alle persone esterne al settore IT di disconnettersi regolarmente e fornire una giustificazione sul motivo per cui è necessario accedere a scopo di controllo.
- **Per mantenere l'elenco delle eccezioni di un criterio:** In un mondo ideale, tutti gli utenti avrebbero seguito i criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedi ai proprietari del gruppo di confermare che hanno ancora bisogno di ospiti nei loro gruppi:** L'accesso dei dipendenti potrebbe essere automatizzato con alcuni ospiti IAM locali, ma non invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di ciò che si vuole rivedere, si creerà la revisione dell'accesso nelle verifiche di accesso di Azure AD, nelle app aziendali di Azure AD (in anteprima) o in Azure AD PIM.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Auto-recensione | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Pannello di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Auto-recensione | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Pannello di accesso |
| Ruolo di Azure AD | Revisori specificati</br>Auto-recensione | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Auto-recensione | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |


## <a name="create-access-reviews"></a>Creare verifiche di accesso

Per creare una revisione di accesso, attenersi alla seguente procedura:

1. Passare al portale di [Azure](https://portal.azure.com) per gestire le verifiche di accesso e accedere come amministratore globale o amministratore utente.

1. Cercare e selezionare **Azure Active Directory**.

      ![Azure portal search for Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. Selezionare **Identity Governance**.

1. Nella pagina Guida introduttiva fare clic sul pulsante Crea una verifica di **accesso.**

   ![Pagina iniziale delle verifiche di accesso](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>Scopri di più sulle recensioni di accesso

Per altre informazioni sulla creazione e sull'esecuzione di verifiche di accesso, guardare questa breve demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se si è pronti per distribuire le verifiche di accesso nell'organizzazione, seguire questi passaggi nel video per l'onboarding, la formazione degli amministratori e la creazione della prima verifica di accesso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario disporre?

Verificare che la directory disponga di almeno il numero di licenze di Azure AD Premium P2 rispetto ai dipendenti che eseguiranno le attività seguenti:Ensure that your directory has at least as many Azure AD Premium P2 licenses as you have employees that will be performing the following tasks:

- Utenti membri e guest assegnati come revisori
- Utenti membri e ospiti che eseguono una revisione automatica
- Proprietari del gruppo che eseguono una verifica di accesso
- Proprietari di applicazioni che eseguono una verifica di accesso

Le licenze di Azure AD Premium P2 non sono necessarie per le attività seguenti:Azure AD Premium P2 licenses are **not** required for the following tasks:

- Non sono necessarie licenze per gli utenti con i ruoli Amministratore globale o Amministratore utenti che configurano le verifiche di accesso, configurano le impostazioni o applicano le decisioni delle revisioni.

Per ogni licenza di Azure AD Premium P2 a pagamento assegnata a uno degli utenti dell'organizzazione, è possibile usare Azure AD business-to-business (B2B) per invitare fino a cinque utenti guest in base a External User Allowance. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere Indicazioni sulle licenze per la [collaborazione B2B di Azure AD.For more information, see Azure AD B2B collaboration licensing guidance](../b2b/licensing-guidance.md).

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze tramite il portale](../fundamentals/license-users-groups.md)di Azure Active Directory.For more information about licenses, see Assign or remove licenses using the Azure Active Directory portal.

### <a name="example-license-scenarios"></a>Scenari di licenza di esempio

Di seguito sono riportati alcuni scenari di licenza di esempio che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Un amministratore crea una verifica di accesso del gruppo A con 75 utenti e 1 proprietario del gruppo e assegna il proprietario del gruppo come revisore. | 1 licenza per il proprietario del gruppo come revisore | 1 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti e 3 proprietari del gruppo e assegna i 3 proprietari del gruppo come revisori. | 3 licenze per ogni proprietario del gruppo come revisori | 3 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti. Lo rende un'auto-recensione. | 500 licenze per ogni utente come autorevisori | 500 |
| Un amministratore crea una verifica di accesso del gruppo C con 50 utenti membri e 25 utenti guest. Lo rende un'auto-recensione. | 50 licenze per ogni utente come autorevisori.<br/>(gli utenti guest sono coperti dal rapporto 1:5 richiesto) | 50 |
| Un amministratore crea una verifica di accesso del gruppo D con 6 utenti membri e 108 utenti guest. Lo rende un'auto-recensione. | 6 licenze per ogni utente come autorevisori: 16 licenze aggiuntive per coprire tutti i 108 utenti guest nel rapporto 1:5 richiesto. 6 licenze, che\*coprono 6 utenti guest 5.30. Per i restanti (108-6\*5) - 78 utenti guest, sono necessarie licenze aggiuntive 78/5 -16. Pertanto, in totale, sono necessarie 6, 16 e 22 licenze. | 22 |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioniCreate an access review of groups or applications](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
