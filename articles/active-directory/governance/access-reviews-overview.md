---
title: Informazioni sulle verifiche di accesso di Azure AD | Microsoft Docs
description: Usando le verifiche di accesso di Azure Active Directory, è possibile controllare l'appartenenza ai gruppi e l'accesso alle applicazioni per soddisfare i requisiti di governance, gestione dei rischi e conformità dell'organizzazione.
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
ms.component: compliance
ms.date: 11/19/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: ecaceea42d64ab15676a9cb5a42ee8659e40d517
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847287"
---
# <a name="what-are-azure-ad-access-reviews"></a>Informazioni sulle verifiche di accesso di Azure AD

Le verifiche di accesso di Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Il video seguente presenta una rapida panoramica delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Perché le verifiche di accesso sono importanti?

Azure AD consente di collaborare internamente nell'organizzazione e con utenti di organizzazioni esterne, ad esempio i partner. Gli utenti possono unirsi ai gruppi, invitare utenti guest, connettersi alle app cloud e lavorare in remoto dai loro dispositivi personali o di lavoro. La praticità di sfruttare le capacità self-service ha portato alla necessità di funzionalità migliori di gestione dell'accesso.

- Quando viene assunto un nuovo dipendente, come è possibile garantire che abbia i diritti di accesso appropriati per lavorare in modo produttivo?
- Quando le persone passano da un team a un altro o lasciano l'azienda, come è possibile garantire che l'accesso precedente venga rimosso, in particolare se riguarda utenti guest?
- Diritti di accesso eccessivi possono portare a violazioni e mancato superamento dei controlli, in quanto indicano la mancanza di controllo sull'accesso.
- È necessario coinvolgere in modo proattivo i proprietari delle risorse per assicurarsi che verifichino regolarmente chi può accedere alle loro risorse.

## <a name="when-to-use-access-reviews"></a>Quando è opportuno usare le verifiche di accesso?

- **Troppi utenti nei ruoli con privilegi:** è opportuno verificare quanti utenti hanno accesso amministrativo, quanti sono amministratori globali e se sono presenti utenti guest o partner invitati che non sono stati rimossi dopo l'assegnazione a un'attività amministrativa. È possibile ricertificare gli utenti a cui sono assegnati i [ruoli di Azure Active Directory](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio gli amministratori globali, o i [ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio il ruolo Amministratore Accesso utenti, tramite l'esperienza di [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quando l'automazione non è realizzabile:** è possibile creare regole per l'appartenenza dinamica ai gruppi di sicurezza o ai gruppi di Office 365, ma cosa accade se i dati delle risorse umane non si trovano in Azure AD o se gli utenti necessitano ancora dell'accesso dopo aver lasciato il gruppo per aiutare nella formazione del loro sostituto? È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati business critical**: per determinate risorse, può essere necessario chiedere alle persone esterne all'IT di disconnettersi regolarmente e di fornire una giustificazione in merito al motivo per cui devono eseguire l'accesso a scopo di controllo.
- **Per mantenere l'elenco eccezioni dei criteri:** In una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedere ai proprietari dei gruppi di confermare che hanno ancora bisogno di guest nei loro gruppi:** l'accesso dei dipendenti potrebbe essere automatizzato con qualche IAM locale, ma non con guest invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di ciò che si vuole verificare, è possibile creare una verifica di accesso in Verifiche di accesso di Azure AD, App aziendali di Azure AD (in anteprima) o Azure AD PIM.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Revisori self-service | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Pannello di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Revisori self-service | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Pannello di accesso |
| Ruolo della directory di Azure AD | Revisori specificati</br>Revisori self-service | Azure AD PIM | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Revisori self-service | Azure AD PIM | Portale di Azure |

## <a name="prerequisites"></a>Prerequisiti

Per usare le verifiche di accesso, è necessario avere una delle licenze seguenti:

- Azure AD Premium P2
- Licenza di Enterprise Mobility + Security (EMS) E5

Per ulteriori informazioni, consultare [Come effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) o [Versione di valutazione di Enterprise Mobility + Security E5](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Introduzione alle verifiche di accesso

Per altre informazioni sulla creazione e sull'esecuzione di verifiche di accesso, guardare questa breve demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se si è pronti per distribuire le verifiche di accesso nell'organizzazione, seguire questi passaggi nel video per l'onboarding, la formazione degli amministratori e la creazione della prima verifica di accesso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Abilitare le verifiche di accesso

Per abilitare le verifiche di accesso, seguire questa procedura.

1. Accedere come Amministratore globale o Amministratore account utente al [portale di Azure](https://portal.azure.com) in cui usare le verifiche di accesso.

1. Fare clic su **Tutti i servizi** e trovare il servizio Verifiche di accesso.

    ![Tutti i servizi - Verifiche di accesso](./media/access-reviews-overview/all-services-access-reviews.png)

1. Fare clic su **Verifiche di accesso**.

    ![Onboarding delle verifiche di accesso](./media/access-reviews-overview/onboard-button.png)

1. Nell'elenco di spostamento fare clic su **Onboarding** per aprire la pagina **Onboarding delle verifiche di accesso**.

    ![Onboarding delle verifiche di accesso](./media/access-reviews-overview/onboard-access-reviews.png)

1. Fare clic su **Crea** per abilitare le verifiche di accesso nella directory corrente. Al successivo avvio delle verifiche di accesso, le opzioni saranno abilitate.

    ![Verifiche di accesso abilitate](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso con le verifiche di accesso di Azure AD](perform-access-review.md)
- [Completare una verifica di accesso dei membri di un gruppo o degli utenti che accedono a un'applicazione in Azure AD](complete-access-review.md)
