---
title: Informazioni sulle verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Usando le verifiche di accesso di Azure Active Directory, è possibile controllare l'accesso al gruppo di appartenenza e l'applicazione in modo da soddisfare governance, gestione dei rischi e iniziative di conformità dell'organizzazione.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/05/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fcc804db66430598e72e9ebf31a8837eda1cca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67204603"
---
# <a name="what-are-azure-ad-access-reviews"></a>Che cosa sono Azure AD access verifiche?

Le verifiche di accesso di Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire le appartenenze, accedere ad applicazioni aziendali e le assegnazioni di ruolo in modo efficiente. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Il video seguente presenta una rapida panoramica delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Perché le verifiche di accesso sono importanti?

Azure AD consente di collaborare internamente nell'organizzazione e con utenti di organizzazioni esterne, ad esempio i partner. Gli utenti possono unirsi ai gruppi, invitare utenti guest, connettersi alle app cloud e lavorare in remoto dai loro dispositivi personali o di lavoro. La praticità di sfruttare le capacità self-service ha portato alla necessità di funzionalità migliori di gestione dell'accesso.

- Quando viene assunto un nuovo dipendente, come è possibile garantire che abbia i diritti di accesso appropriati per lavorare in modo produttivo?
- Quando le persone passano da un team a un altro o lasciano l'azienda, come è possibile garantire che l'accesso precedente venga rimosso, in particolare se riguarda utenti guest?
- Diritti di accesso eccessivi possono portare a violazioni e mancato superamento dei controlli, in quanto indicano la mancanza di controllo sull'accesso.
- È necessario coinvolgere in modo proattivo i proprietari delle risorse per assicurarsi che verifichino regolarmente chi può accedere alle loro risorse.

## <a name="when-to-use-access-reviews"></a>Quando è opportuno usare le verifiche di accesso?

- **Troppi utenti nei ruoli con privilegi:** È una buona idea per verificare quanti utenti hanno accesso amministrativo, quanti di essi sono gli amministratori globali, e se sono presenti eventuali invitato utenti guest o partner che non sono stati rimossi dopo l'assegnazione per eseguire un'attività amministrativa. È possibile ricertificare gli utenti di assegnazione ruolo [ruoli di Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , ad esempio gli amministratori globali, o [ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , ad esempio amministratore accesso utenti nel [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) esperienza.
- **Quando l'automazione non è realizzabile:** è possibile creare regole per l'appartenenza dinamica ai gruppi di sicurezza o ai gruppi di Office 365, ma cosa accade se i dati delle risorse umane non si trovano in Azure AD o se gli utenti necessitano ancora dell'accesso dopo aver lasciato il gruppo per aiutare nella formazione del loro sostituto? È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati business critical**: per determinate risorse, può essere necessario chiedere alle persone esterne all'IT di disconnettersi regolarmente e di fornire una giustificazione in merito al motivo per cui devono eseguire l'accesso a scopo di controllo.
- **Per mantenere l'elenco eccezioni dei criteri:** In un mondo ideale, tutti gli utenti seguirà i criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedere ai proprietari dei gruppi di confermare che hanno ancora bisogno di guest nei loro gruppi:** Accesso dei dipendenti potrebbe essere automatizzata con alcune in locale IAM, ma non invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di ciò che si desidera esaminare, si creerà la verifica di accesso di Azure AD accedere alle recensioni, le app aziendali di Azure AD (in anteprima) o Azure AD Privileged Identity Management.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Verifica automatica | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Pannello di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Verifica automatica | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Pannello di accesso |
| Ruolo di Azure AD | Revisori specificati</br>Verifica automatica | [Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Verifica automatica | [Azure AD Privileged Identity Management](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |

## <a name="which-users-must-have-licenses"></a>Quali utenti devono avere le licenze?

Ogni utente che interagisce con le verifiche di accesso deve avere una licenza a pagamento di Azure AD Premium P2. Tra gli esempi sono inclusi:

- Amministratori che creano una verifica di accesso
- Rivedere i proprietari del gruppo che eseguono un accesso
- Utenti assegnati come revisori
- Utenti che eseguono una verifica automatica

È anche possibile chiedere agli utenti guest di verificare il proprio accesso. Per ogni licenza di Azure AD Premium P2 a pagamento che si assegna a uno degli utenti dell'organizzazione, è possibile usare Azure AD business-to-business (B2B) per invitare utenti guest fino a cinque sotto il limite di utenti esterni. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere [linee guida sulla collaborazione B2B di Azure AD](../b2b/licensing-guidance.md).

Ecco alcuni scenari di esempio che consentono di determinare il numero di licenze di che è necessario disporre.

| Scenario | Calcolo | Numero necessario di licenze |
| --- | --- | --- |
| Un amministratore crea una verifica di accesso di un gruppo con 500 utenti.<br/>Assegna proprietari del gruppo 3 come revisori. | 1 amministratore + 3 proprietari del gruppo | 4 |
| Un amministratore crea una verifica di accesso di un gruppo con 500 utenti.<br/>Rende di una verifica automatica. | 1 amministratore + 500 utenti come revisori Self-Service | 501 |
| Un amministratore crea una verifica di accesso di gruppo A 25 utenti guest con 5 utenti.<br/>Rende di una verifica automatica. | 1 amministratore + 5 utenti come revisori Self-Service<br/>(gli utenti guest sono vedere il rapporto obbligatorio 1: 5) | 6 |
| Un amministratore crea una verifica di accesso di un gruppo con 5 utenti e gli utenti guest di 28.<br/>Rende di una verifica automatica. | 1 amministratore + 5 utenti come revisori Self-Service + 1 utente per tutti gli utenti guest nel rapporto 1: 5 necessaria | 7 |

Per informazioni su come assegnare licenze agli utenti, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="learn-about-access-reviews"></a>Informazioni sulle verifiche di accesso

Per altre informazioni sulla creazione e sull'esecuzione di verifiche di accesso, guardare questa breve demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se si è pronti per distribuire le verifiche di accesso nell'organizzazione, seguire questi passaggi nel video per l'onboarding, la formazione degli amministratori e la creazione della prima verifica di accesso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Completare una verifica di accesso dei gruppi o applicazioni](complete-access-review.md)
