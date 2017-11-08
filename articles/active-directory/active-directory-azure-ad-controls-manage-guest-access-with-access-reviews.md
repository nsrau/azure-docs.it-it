---
title: Gestire l'accesso guest con le verifiche di accesso di Azure AD | Microsoft Docs
description: Gestire gli utenti guest come membri di un gruppo o assegnati a un'applicazione con le verifiche di accesso di Azure Active Directory
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: d67b12eaac17e278724ddf8670e65afba235f099
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gestire l'accesso guest con le verifiche di accesso di Azure AD


Con Azure Active Directory (Azure AD) è possibile abilitare facilmente la collaborazione all'interno dell'azienda usando la [funzionalità Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Gli utenti guest dagli altri tenant possono essere [invitati dagli amministratori](active-directory-b2b-admin-add-users.md) o da [altri utenti](active-directory-b2b-how-it-works.md). Questa funzionalità si applica anche alle identità di social networking, ad esempio gli account Microsoft.

È possibile anche assicurarsi che gli utenti guest dispongano dell'accesso appropriato. Si può chiedere ai guest stessi o a un decision maker di partecipare a una verifica di accesso e certificare di nuovo (o "attestare") l'accesso dei guest. I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso è possibile apportare modifiche e rimuovere l'accesso per i guest per i quali non è più necessario.

> [!NOTE]
> Questo documento è incentrato sulla verifica dell'accesso degli utenti guest. Se si vuole controllare l'accesso di tutti gli utenti, non solo dei guest, vedere l'argomento su come [gestire l'accesso utente con le verifiche di accesso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Se si vuole verificare l'appartenenza degli utenti a ruoli amministrativi come amministratore globale, vedere l'articolo su come [avviare una verifica di accesso in Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Prerequisiti 

Le verifiche di accesso sono disponibili con Azure AD edizione Premium P2, incluso in Microsoft Enterprise Mobility + Security E5. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md). Ogni utente che interagisce con questa funzionalità per accedere a una verifica oppure crearne o applicarne una necessita di una licenza.

Se si prevede di chiedere agli utenti guest di verificare il proprio accesso, leggere le informazioni sulle licenze per gli utenti guest. Per altre informazioni, vedere [Licenze di collaborazione B2B di Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Creare ed eseguire una verifica di accesso per i guest

Abilitare prima la visualizzazione delle verifiche di accesso nei pannelli di accesso dei revisori. Come amministratore globale, passare alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD consente diversi scenari per la verifica degli utenti guest.

Selezionare uno degli elementi seguenti:

 - Un gruppo in Azure AD con uno o più guest come membri.
 - Un'applicazione connessa ad Azure AD a cui sono assegnati uno o più utenti guest. 

È quindi possibile decidere se chiedere a ogni guest di verificare il proprio accesso o chiedere a uno o più utenti di verificare l'accesso di ogni guest.

 Questi scenari verranno illustrati nelle sezioni seguenti.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Chiedere ai guest di verificare la propria appartenenza a un gruppo

È possibile usare le verifiche di accesso per controllare se gli utenti invitati e aggiunti a un gruppo continuano ad avere necessità dell'accesso. È possibile chiedere facilmente ai guest di verificare la propria appartenenza a un gruppo.

1. Per iniziare una verifica di accesso per il gruppo, selezionare la verifica in modo da includere solo i membri utenti guest e in modo che i membri stessi eseguano la verifica. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

2. Chiedere a ogni guest di verificare la propria appartenenza. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso. Azure AD include istruzioni per i guest su [come verificare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Oltre agli utenti che hanno negato la necessità di un accesso continuo, è possibile rimuovere anche gli utenti che non hanno risposto. Gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica.

5. Se il gruppo non viene usato per la gestione dell'accesso, è possibile rimuovere anche gli utenti non selezionati per partecipare alla verifica perché non hanno accettato in precedenza il proprio invito. Non accettare può indicare che l'indirizzo di posta elettronica dell'utente invitato conteneva un errore di digitazione. Se un gruppo viene usato come lista di distribuzione, è possibile che alcuni utenti guest non siano stati selezionati per partecipare poiché erano oggetti contatto.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Chiedere agli sponsor di verificare l'appartenenza del guest a un gruppo

È possibile chiedere a uno sponsor, ad esempio i proprietari di un gruppo, di verificare la necessità del guest di avere un'appartenenza continua al gruppo.

1. Per iniziare una verifica di accesso per il gruppo, selezionare la verifica in modo da includere solo i membri utenti guest. Specificare quindi uno o più revisori. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceve un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso per [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Chiedere ai guest di verificare il proprio accesso a un'applicazione

È possibile usare le verifiche di accesso per controllare che gli utenti invitati per una specifica applicazione continuino ad avere necessità dell'accesso. È possibile chiedere semplicemente ai guest stessi di verificare la propria necessità di accesso.

1. Per iniziare una verifica di accesso per l'applicazione, selezionare la verifica in modo da includere solo i guest e in modo che gli utenti verifichino il proprio accesso. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

2. Chiedere a ogni guest di verificare il proprio accesso all'applicazione. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione. Azure AD include istruzioni per i guest su [come verificare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Oltre agli utenti che hanno negato la necessità di un accesso continuo, è possibile rimuovere anche gli utenti guest che non hanno risposto. Gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica. È possibile anche rimuovere gli utenti guest che non sono stati selezionati per partecipare, soprattutto se non sono stati invitati di recente. Questi utenti non hanno accettato l'invito e quindi non hanno accesso all'applicazione. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Chiedere a uno sponsor di verificare l'accesso del guest a un'applicazione

È possibile chiedere a uno sponsor, ad esempio il proprietario di un'applicazione, di verificare la necessità del guest di un accesso continuo all'applicazione.

1. Per iniziare una verifica di accesso per l'applicazione, selezionando la verifica in modo da includere solo i guest. Specificare quindi uno o più utenti come revisori. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceve un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso per [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Chiedere ai guest di verificare la propria necessità di accesso in generale

In alcune organizzazioni i guest potrebbero non essere consapevoli delle proprie appartenenze a un gruppo.

> [!NOTE]
> Le versioni precedenti del portale di Azure non consentivano l'accesso amministrativo da parte degli utenti con l'attributo UserType impostato su Guest. In alcuni casi, un amministratore nella directory potrebbe avere modificato il valore dell'attributo UserType del guest su membro tramite PowerShell. Se questa modifica è avvenuta in precedenza nella directory, la query precedente potrebbe non includere tutti gli utenti guest che avevano diritti di accesso amministrativo. In questo caso è necessario modificare l'attributo UserType del guest o includere manualmente il guest nell'appartenenza al gruppo.

1. Creare un gruppo di sicurezza di Azure AD con i guest come membri, se non ne esiste già un gruppo appropriato. Ad esempio, è possibile creare un gruppo con un'appartenenza gestita manualmente di guest. In alternativa, è possibile creare un gruppo dinamico con un nome, ad esempio "Guest di Contoso" per gli utenti nel tenant Contoso con l'attributo UserType impostato su valore Guest.

2. Per iniziare una verifica di accesso per questo gruppo, selezionare come revisori i membri stessi. Per altre informazioni, vedere l'articolo su come [creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).

3. Chiedere a ogni guest di verificare la propria appartenenza. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione. Azure AD include istruzioni per i guest su [come verificare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).

4. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso. Per altre informazioni, vedere l'articolo su come [completare una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md).

5. Rimuovere l'accesso guest per i guest per cui è stato negato l'accesso, che non hanno completato la verifica o non hanno accettato in precedenza l'invito. Se alcuni guest sono contatti che erano stati selezionati per partecipare alla verifica perché non avevano accettato un invito in precedenza, è possibile disabilitare i relativi account usando il portale di Azure o PowerShell. Se il guest non necessita più dell'accesso e non è un contatto, è possibile rimuovere il relativo oggetto utente dalla directory usando il portale di Azure o PowerShell.

## <a name="next-steps"></a>Passaggi successivi

[Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)







