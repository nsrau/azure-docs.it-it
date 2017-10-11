---
title: Gestire l'accesso guest con le verifiche di accesso di Azure AD | Microsoft Docs
description: Gestione degli utenti guest come membri di un gruppo o assegnati a un'applicazione con le verifiche di accesso di Azure Active Directory
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
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gestire l'accesso guest con le verifiche di accesso di Azure AD


Con Azure Active Directory è possibile abilitare facilmente la collaborazione all'interno dell'azienda usando la [funzionalità Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md). Gli utenti guest dagli altri tenant possono essere [invitati dagli amministratori](active-directory-b2b-admin-add-users.md) o dagli [ utenti finali](active-directory-b2b-how-it-works.md).  Questo vale anche per le identità di social networking, ad esempio gli account Microsoft.

È possibile anche assicurarsi che gli utenti guest dispongano dell'accesso appropriato.  È possibile eseguire questa operazione chiedendo ai guest stessi o a un responsabile delle decisioni di partecipare a una verifica di accesso e ricertificare (o "attestare") l'accesso del guest. I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso, è possibile apportare modifiche e rimuovere l'accesso ai guest per i quali non è più necessario.

> [!NOTE]
> Questo documento è incentrato sulla verifica dell'accesso degli utenti guest. Se si vuole controllare l'accesso di tutti gli utenti, non solo dei guest, leggere la guida alla [gestione dell'accesso utente con le verifiche di accesso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md).  Se invece si vuole verificare l'appartenenza dell'utente a ruoli amministrativi come amministratore globale, vedere [Come avviare una verifica dell'accesso in Azure AD PIM](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Prerequisiti 

Le verifiche di accesso sono disponibili con Azure Active Directory edizione Premium P2, incluso in EMS E5. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).  Ogni utente che interagisce con questa funzionalità per creare una verifica, eseguire una verifica di accesso o applicare una verifica richiede una licenza.  

Se si richiede agli utenti guest di verificare il proprio accesso, leggere altre informazioni sulle licenze degli utenti guest in [Licenze per la Collaborazione B2B di Azure AD](active-directory-b2b-licensing.md).

## <a name="creating-and-performing-an-access-review-for-guests"></a>Creazione ed esecuzione di una verifica di accesso per i guest

Abilitare prima la visualizzazione delle verifiche di accesso nei pannelli di accesso dei revisori.  Come amministratore globale, passare alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD consente diversi scenari per la verifica degli utenti guest.  

Selezionare uno degli elementi seguenti:
 - un gruppo in Azure Active Directory che ha uno o più utenti guest come membri 
 - o un'applicazione connessa ad Azure Active Directory a cui sono assegnati uno o più utenti guest; determinare se ogni guest deve verificare il proprio accesso oppure uno o più utenti devono verificare l'accesso di ogni guest.

 Ognuno di questi scenari verrà illustrato nelle sezioni seguenti.

* [Chiedere ai guest di verificare la propria appartenenza a un gruppo](#asking-guests-to-review-their-own-membership-in-a-group)
* [Chiedere agli sponsor di verificare l'appartenenza del guest a un gruppo](#asking-sponsors-to-review-guests-membership-in-a-group)
* [Chiedere ai guest di verificare il proprio accesso a un'applicazione](#asking-guests-to-review-their-own-access-to-an-application)
* [Chiedere agli sponsor di verificare l'accesso di guest a un'applicazione](#asking-sponsors-to-review-guests-access-to-an-application) 
* [Chiedere ai guest di verificare la propria necessità di accesso in generale](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>Chiedere agli utenti guest di verificare la propria appartenenza a un gruppo

Le verifiche di accesso possono essere usate per controllare se gli utenti invitati e aggiunti a un gruppo continuano ad avere necessità dell'accesso.  Un modo semplice consiste nel chiedere ai guest stessi di verificare autonomamente la propria appartenenza al gruppo.

1. Iniziare una verifica di accesso per il gruppo, selezionando la verifica in modo da includere solo i membri utenti guest e in modo che i membri stessi eseguano la verifica. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
2. Chiedere a ogni guest di verificare la propria appartenenza.  Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso.  Azure AD include istruzioni per i guest su [come controllare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md). 
4. Oltre agli utenti che hanno negato la necessità di un accesso continuo, è possibile rimuovere anche gli utenti che non hanno risposto.  Gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica.
5. Se il gruppo non viene usato per la gestione dell'accesso, è possibile rimuovere anche gli utenti non selezionati per partecipare alla verifica perché non hanno accettato in precedenza il proprio invito.  Ciò può indicare che l'indirizzo di posta elettronica dell'utente invitato conteneva un errore di digitazione.  Se tuttavia viene usato un gruppo come lista di distribuzione, alcuni utenti guest potrebbero non essere stati selezionati per partecipare poiché sono oggetti di contatto.

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>Richiedere agli sponsor di verificare l'appartenenza del guest a un gruppo

È possibile chiedere a uno sponsor, ad esempio i proprietari di un gruppo, di verificare la necessità del guest di avere un'appartenenza continua al gruppo.

1. Iniziare una verifica di accesso per il gruppo, selezionando la verifica in modo da includere solo i membri utenti guest e specificando uno o più revisori. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceverà un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso in cui potranno [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>Chiedere ai guest di verificare il proprio accesso a un'applicazione

Le verifiche di accesso possono essere usate per controllare che gli utenti invitati per una specifica applicazione continuino ad avere necessità dell'accesso.  Un modo semplice consiste nel chiedere ai guest stessi di verificare la propria necessità di accesso.

1. Iniziare una verifica di accesso per l'applicazione, selezionando la verifica in modo da includere solo i guest e in modo che gli utenti verifichino il proprio accesso. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
2. Chiedere a ogni guest di verificare il proprio accesso all'applicazione.  Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione.  Azure AD include istruzioni per i guest su [come controllare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md).
4. Oltre agli utenti che hanno negato la necessità di accesso continuo, è possibile rimuovere anche gli utenti guest che non hanno risposto perché gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica.  È possibile anche rimuovere gli utenti guest non selezionati per partecipare, soprattutto se il guest non è stato invitato di recente perché questi utenti non hanno accettato in precedenza l'invito e quindi non hanno accesso all'applicazione. 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>Chiedere agli sponsor di verificare l'accesso del guest a un'applicazione


È possibile chiedere a uno sponsor, ad esempio i proprietari di un'applicazione, di verificare la necessità del guest di un accesso continuo all'applicazione.

1. Iniziare una verifica di accesso per l'applicazione, selezionando la verifica in modo da includere solo i guest e specificando uno o più utenti come revisori. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ognuno di essi riceverà un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso in cui potranno [eseguire la verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md).
3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>Chiedere ai guest di verificare la necessità di accesso in generale

In alcune organizzazioni i guest potrebbero non essere consapevoli delle proprie appartenenze a un gruppo.

> [!NOTE]
> Le versioni precedenti del portale di Azure non consentivano l'accesso amministrativo da parte degli utenti con valore Guest per l'attributo UserType e, in alcuni casi, un amministratore nella directory può avere modificato il valore UserType del guest su membro usando PowerShell.  Se ciò si è verificato in precedenza nella directory, la query di cui sopra potrebbe non includere tutti gli utenti guest a cui in passato sono stati assegnati diritti di accesso amministrativi; è quindi necessario modificare il valore UserType di questo guest oppure includerlo manualmente nell'appartenenza al gruppo.

1. Creare un gruppo di sicurezza di Azure AD con i guest come membri, se non ne esiste già un gruppo appropriato.  Ad esempio, è possibile creare un gruppo con un'appartenenza gestita manualmente di guest.  In alternativa, è possibile creare un gruppo dinamico con un nome, ad esempio "Guest di Contoso" per gli utenti nel tenant Contoso con l'attributo UserType impostato su valore Guest.
2. Iniziare una verifica di accesso per questo gruppo selezionando come revisori i membri stessi. Per altre informazioni, vedere l'articolo su [come creare una verifica di accesso](active-directory-azure-ad-controls-create-access-review.md).
3. Chiedere a ogni guest di verificare la propria appartenenza.  Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione.  Azure AD include istruzioni per i guest su [come controllare l'accesso](active-directory-azure-ad-controls-perform-access-review.md).
4. Dopo che i revisori hanno espresso il proprio giudizio, interrompere la verifica di accesso. Per altre informazioni, vedere [Come completare una verifica dell'accesso](active-directory-azure-ad-controls-complete-access-review.md).
5. Rimuovere l'accesso guest per i guest per cui è stato negato l'accesso, che non hanno completato la verifica o non hanno accettato in precedenza l'invito.   Se alcuni guest sono contatti che erano stati selezionati per partecipare alla verifica perché non avevano accettato un invito in precedenza, è consigliabile disabilitare l'account dall'accesso usando il portale di Azure o PowerShell.  Se il guest non necessita più dell'accesso e non è un contatto,l'oggetto utente può essere rimosso dalla directory usando il portale di Azure o PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso per i membri di un gruppo o per l'accesso a un'applicazione](active-directory-azure-ad-controls-create-access-review.md)








