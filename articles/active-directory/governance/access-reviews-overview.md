---
title: Informazioni sulle verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Usando le verifiche di accesso di Azure Active Directory, è possibile controllare l'appartenenza ai gruppi e l'accesso alle applicazioni per soddisfare i requisiti di governance, gestione dei rischi e conformità dell'organizzazione.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 09/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperfq1
ms.openlocfilehash: b454ced085ec3d73f3ca0f761abb6c5de44244ab
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594340"
---
# <a name="what-are-azure-ad-access-reviews"></a>Cosa sono le verifiche di accesso di Azure AD?

Le verifiche di accesso di Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli. È possibile verificare regolarmente l'accesso degli utenti per assicurarsi che solo le persone appropriate dispongano di accesso continuo.

Il video seguente presenta una rapida panoramica delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Perché le verifiche di accesso sono importanti?

Azure AD consente di collaborare con utenti all'interno dell'organizzazione e con utenti esterni. Gli utenti possono unirsi ai gruppi, invitare utenti guest, connettersi alle app cloud e lavorare in remoto dai loro dispositivi personali o di lavoro. La praticità di utilizzo delle funzionalità self-service ha determinato l'esigenza di migliori funzionalità di gestione degli accessi.

- Quando vengono assunti nuovi dipendenti, come è possibile garantire che abbiano i diritti di accesso appropriati per lavorare in modo produttivo?
- Quando le persone passano da un team a un altro o lasciano l'azienda, come è possibile garantire che il loro precedente accesso venga rimosso?
- Diritti di accesso eccessivi possono causare compromissioni.
- Diritti di accesso eccessivi possono anche determinare il mancato superamento dei controlli, perché questi indicano la mancanza di controllo sull'accesso.
- È necessario coinvolgere in modo proattivo i proprietari delle risorse per assicurarsi che verifichino regolarmente chi può accedere alle loro risorse.

## <a name="when-should-you-use-access-reviews"></a>Quando è consigliabile usare le verifiche di accesso?

- **Troppi utenti nei ruoli con privilegi:** è opportuno verificare quanti utenti hanno accesso amministrativo, quanti sono amministratori globali e se sono presenti utenti guest o partner invitati che non sono stati rimossi dopo l'assegnazione a un'attività amministrativa. È possibile ricertificare gli utenti a cui sono assegnati i [ruoli di Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio gli amministratori globali, o i [ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio il ruolo Amministratore Accesso utenti, tramite l'esperienza di [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quando l'automazione non è possibile:** è possibile creare regole per l'appartenenza dinamica ai gruppi di sicurezza o a Gruppi di Microsoft 365, ma cosa succede se i dati delle risorse umane non si trovano in Azure AD o se gli utenti necessitano ancora dell'accesso dopo aver lasciato il gruppo per contribuire a formare i loro sostituti? È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati business critical**: per determinate risorse, può essere necessario chiedere a persone esterne all'IT di disconnettersi regolarmente e di fornire una giustificazione in merito al motivo per cui devono eseguire l'accesso a scopo di controllo.
- **Per mantenere l'elenco eccezioni dei criteri:** in una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedere ai proprietari dei gruppi di confermare che hanno ancora bisogno di guest nei loro gruppi:** l'accesso dei dipendenti potrebbe essere automatizzato con gestione delle identità e degli accessi (IAM) in locale, ma non con utenti guest invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

>[!NOTE]
>Se si è pronti per provare le verifiche di accesso, vedere [Creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di quello che si vuole verificare, è possibile creare una verifica di accesso in Verifiche di accesso di Azure AD, App aziendali di Azure AD (in anteprima) o Azure AD PIM.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Verifica autonoma | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Pannello di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Verifica autonoma | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Pannello di accesso |
| Ruolo di Azure AD | Revisori specificati</br>Verifica autonoma | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Verifica autonoma | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario avere?

Per la directory è necessario un numero di licenze di Azure AD Premium P2 pari almeno al numero dei dipendenti che eseguiranno queste attività:

- Utenti membro e guest assegnati come revisori
- Utenti membro e guest che eseguono una verifica autonoma
- Proprietari di gruppo che eseguono una verifica di accesso
- Proprietari di applicazioni che eseguono una verifica di accesso

**Non** sono necessarie licenze di Azure AD Premium P2 per gli utenti con ruolo Amministratore globale o Amministratore utenti che configurano le verifiche di accesso o le impostazioni o che applicano le decisioni delle verifiche.

Per ogni licenza di Azure AD Premium P2 a pagamento assegnata a uno degli utenti della propria organizzazione, è possibile usare Azure AD B2B (business-to-business) per invitare fino a cinque utenti guest in base al limite di utenti esterni. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere [Linee guida sulle licenze per la collaborazione di Azure AD B2B](../external-identities/licensing-guidance.md).

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Scenari di licenze di esempio

Ecco alcuni esempi di scenari che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Un amministratore crea una verifica di accesso del gruppo A con 75 utenti e 1 proprietario del gruppo, quindi assegna il proprietario del gruppo come revisore. | 1 licenza per il proprietario del gruppo come revisore | 1 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti e 3 proprietari del gruppo, quindi assegna i 3 proprietari del gruppo come revisori. | 3 licenze, una per ogni proprietario del gruppo come revisore | 3 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti. La crea come verifica autonoma. | 500 licenze, una per ogni utente come revisore autonomo | 500 |
| Un amministratore crea una verifica di accesso del gruppo C con 50 utenti membro e 25 utenti guest. La crea come verifica autonoma. | 50 licenze, una per ogni utente come revisore autonomo.* | 50 |
| Un amministratore crea una verifica di accesso del gruppo D con 6 utenti membro e 108 utenti guest. La crea come verifica autonoma. | 6 licenze, una per ogni utente come revisore autonomo. Gli utenti guest vengono fatturati in base agli utenti attivi mensili. Non occorre alcuna licenza aggiuntiva. *  | - |

\* I prezzi delle identità esterne di Azure AD (utente guest) si basano sugli utenti attivi mensili, ovvero sul numero di utenti univoci con attività di autenticazione entro un mese di calendario. Questo modello sostituisce il modello di fatturazione con rapporto 1:5, che consentiva fino a cinque utenti guest per ogni licenza di Azure AD Premium nel tenant. Se il tenant è collegato a una sottoscrizione e si usano le funzionalità di identità esterne per collaborare con utenti guest, la fatturazione verrà eseguita automaticamente usando il modello di fatturazione basato sugli utenti attivi mensili. Per altre informazioni, vedere il modello di fatturazione per identità esterne di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)