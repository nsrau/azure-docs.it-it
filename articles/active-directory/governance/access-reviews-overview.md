---
title: Informazioni sulle verifiche di accesso - Azure Active Directory | Microsoft Docs
description: Usando le verifiche di accesso di Azure Active Directory, è possibile controllare l'appartenenza ai gruppi e l'accesso alle applicazioni per soddisfare i requisiti di governance, gestione dei rischi e conformità dell'organizzazione.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a7cafbf8223c3d9e7641851d02f61bc2ad16ae
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078893"
---
# <a name="what-are-azure-ad-access-reviews"></a>Cosa sono le verifiche di accesso di Azure AD?

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

- **Troppi utenti nei ruoli con privilegi:** è opportuno verificare quanti utenti hanno accesso amministrativo, quanti sono amministratori globali e se sono presenti utenti guest o partner invitati che non sono stati rimossi dopo l'assegnazione a un'attività amministrativa. È possibile ricertificare gli utenti a cui sono assegnati i [ruoli di Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio gli amministratori globali, o i [ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), ad esempio il ruolo Amministratore Accesso utenti, tramite l'esperienza di [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quando l'automazione non è realizzabile:** è possibile creare regole per l'appartenenza dinamica ai gruppi di sicurezza o a Gruppi di Office 365, ma cosa succede se i dati delle risorse umane non si trovano in Azure AD o se gli utenti necessitano ancora dell'accesso dopo aver lasciato il gruppo per contribuire a formare i loro sostituti. È possibile creare una verifica per tale gruppo per assicurarsi che gli utenti che necessitano ancora dell'accesso dispongano di accesso continuo.
- **Quando un gruppo viene usato per un nuovo scopo**: se c'è un gruppo che deve essere sincronizzato con Azure AD o se si prevede di abilitare l'applicazione Salesforce per tutti gli utenti nel gruppo del team di vendita, può essere utile chiedere al proprietario del gruppo di verificare l'appartenenza al gruppo prima che il gruppo venga usato in un ambito di rischio diverso.
- **Accesso ai dati business critical**: per determinate risorse, può essere necessario chiedere a persone esterne all'IT di disconnettersi regolarmente e di fornire una giustificazione in merito al motivo per cui devono eseguire l'accesso a scopo di controllo.
- **Per mantenere l'elenco eccezioni dei criteri:** in una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. L'amministratore IT può gestire questa attività, evitare problemi di supervisione delle eccezioni dei criteri e fornire ai revisori una prova che queste eccezioni vengono esaminate periodicamente.
- **Chiedere ai proprietari dei gruppi di confermare che hanno ancora bisogno di guest nei loro gruppi:** l'accesso dei dipendenti potrebbe essere automatizzato con qualche IAM locale, ma non con utenti guest invitati. Se un gruppo assegna a utenti guest l'accesso a contenuti aziendali sensibili, è responsabilità del proprietario del gruppo confermare che gli utenti abbiano ancora un'esigenza aziendale legittima per tale accesso.
- **Impostare verifiche periodiche**: è possibile configurare verifiche di accesso periodiche per gli utenti in base a una frequenza specifica, ad esempio settimanale, mensile, trimestrale o annuale, e i revisori riceveranno una notifica all'inizio di ogni verifica. I revisori possono approvare o negare l'accesso con un'interfaccia utente semplice da usare e con l'aiuto di consigli intelligenti.

## <a name="where-do-you-create-reviews"></a>Dove si creano le verifiche?

A seconda di quello che si vuole verificare, è possibile creare una verifica di accesso in Verifiche di accesso di Azure AD, App aziendali di Azure AD (in anteprima) o Azure AD PIM.

| Diritti di accesso degli utenti | I revisori possono essere | Verifica creata in | Esperienza di verifica |
| --- | --- | --- | --- |
| Membri di gruppi di sicurezza</br>Membri di gruppi di Office | Revisori specificati</br>Proprietari del gruppo</br>Verifica autonoma | Verifiche di accesso di Azure AD</br>Gruppi di Azure AD | Pannello di accesso |
| Assegnati a un'app connessa | Revisori specificati</br>Verifica autonoma | Verifiche di accesso di Azure AD</br>App aziendali di Azure AD (in anteprima) | Pannello di accesso |
| Ruolo di Azure AD | Revisori specificati</br>Verifica autonoma | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |
| Ruolo delle risorse di Azure | Revisori specificati</br>Verifica autonoma | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portale di Azure |


## <a name="create-access-reviews"></a>Creare verifiche di accesso

Per creare una verifica di accesso, seguire questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com) per gestire le verifiche di accesso e accedere come amministratore globale o amministratore utenti.

1. Cercare e selezionare **Azure Active Directory**.

      ![Ricerca di Azure Active Directory nel portale di Azure](media/access-reviews-overview/search-azure-active-directory.png)

1. Selezionare **Identity Governance**.

1. Nella pagina Introduzione fare clic sul pulsante **Crea una verifica di accesso**.

   ![Pagina iniziale delle verifiche di accesso](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Creazione della verifica di accesso per un gruppo che può essere assegnato al ruolo di Azure AD
Se si usa la versione più recente di Verifiche di accesso (i revisori vengono indirizzati ad **Accesso personale** per impostazione predefinita), solo l'amministratore globale può creare una verifica di accesso per i gruppi assegnabili al ruolo. Tuttavia, se si usa una versione precedente di Verifiche di accesso (i revisori vengono indirizzati a **Riquadro di accesso** per impostazione predefinita), l'amministratore globale e l'amministratore utenti possono creare una verifica di accesso nei gruppi assegnabili al ruolo.  

La nuova esperienza verrà implementata per tutti i clienti il 1° agosto 2020, ma se si vuole eseguire l'aggiornamento prima, inviare una richiesta qui: [Verifiche di accesso di Azure AD - Esperienza dei revisori aggiornata nell'iscrizione ad Accesso personale](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

[Altre informazioni sull'assegnazione di gruppi ai ruoli di Azure AD](https://go.microsoft.com/fwlink/?linkid=2103037).

## <a name="learn-about-access-reviews"></a>Informazioni sulle verifiche di accesso

Per altre informazioni sulla creazione e sull'esecuzione di verifiche di accesso, guardare questa breve demo:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se si è pronti per distribuire le verifiche di accesso nell'organizzazione, seguire questi passaggi nel video per l'onboarding, la formazione degli amministratori e la creazione della prima verifica di accesso.

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisiti relativi alle licenze

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quante licenze è necessario avere?

Assicurarsi che la directory includa un numero di licenze Azure AD Premium P2 almeno uguale al numero di dipendenti che eseguiranno le attività seguenti:

- Utenti membro e guest assegnati come revisori
- Utenti membro e guest che eseguono una verifica autonoma
- Proprietari di gruppo che eseguono una verifica di accesso
- Proprietari di applicazioni che eseguono una verifica di accesso

Le licenze di Azure AD Premium P2 **non** sono necessarie per le attività seguenti:

- Non sono necessarie licenze per gli utenti con i ruoli di amministratore globale o amministratore utenti che configurano le verifiche di accesso e le impostazioni o che applicano le decisioni delle verifiche.

Per ogni licenza di Azure AD Premium P2 a pagamento assegnata a uno degli utenti della propria organizzazione, è possibile usare Azure AD B2B (business-to-business) per invitare fino a cinque utenti guest in base al limite di utenti esterni. Questi utenti guest possono anche usare le funzionalità di Azure AD Premium P2. Per altre informazioni, vedere [Linee guida sulle licenze per la collaborazione di Azure AD B2B](../b2b/licensing-guidance.md).

Per altre informazioni sulle licenze, vedere [Assegnare o rimuovere licenze usando il portale di Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Scenari di licenze di esempio

Ecco alcuni esempi di scenari che consentono di determinare il numero di licenze necessarie.

| Scenario | Calcolo | Numero di licenze |
| --- | --- | --- |
| Un amministratore crea una verifica di accesso del gruppo A con 75 utenti e 1 proprietario del gruppo, quindi assegna il proprietario del gruppo come revisore. | 1 licenza per il proprietario del gruppo come revisore | 1 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti e 3 proprietari del gruppo, quindi assegna i 3 proprietari del gruppo come revisori. | 3 licenze, una per ogni proprietario del gruppo come revisore | 3 |
| Un amministratore crea una verifica di accesso del gruppo B con 500 utenti. La crea come verifica autonoma. | 500 licenze, una per ogni utente come revisore autonomo | 500 |
| Un amministratore crea una verifica di accesso del gruppo C con 50 utenti membro e 25 utenti guest. La crea come verifica autonoma. | 50 licenze, una per ogni utente come revisore autonomo.<br/>Gli utenti guest sono coperti dal rapporto 1:5 richiesto | 50 |
| Un amministratore crea una verifica di accesso del gruppo D con 6 utenti membro e 108 utenti guest. La crea come verifica autonoma. | 6 licenze, una per ogni utente come revisore autonomo + 16 licenze aggiuntive per coprire tutti i 108 utenti guest nel rapporto 1:5 richiesto. 6 licenze, che coprono 6\*5 = 30 utenti guest. Per i rimanenti utenti sono necessarie (108-6\*5) = 78 utenti guest, 78/5 = 16 licenze aggiuntive. Quindi in totale sono necessarie 6 + 16 = 22 licenze. | 22 |

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)
