---
title: Miglioramenti alla gestione degli utenti (anteprima)-Azure Active Directory | Microsoft Docs
description: Viene descritto come Azure Active Directory consente la ricerca, il filtraggio e altre informazioni sugli utenti.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e0c8e6fb3bab179483d03320e6d90ab712ec528
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89493349"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Miglioramenti alla gestione degli utenti (anteprima) in Azure Active Directory

Questo articolo descrive come usare l'anteprima di gestione utente avanzata nel portale di Azure Active Directory (Azure AD). Le pagine **tutti gli utenti** e **gli utenti eliminati** sono state aggiornate per fornire altre informazioni e semplificare l'individuazione degli utenti. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le modifiche nell'anteprima includono:

- Proprietà utente più visibili, tra cui ID oggetto, stato di sincronizzazione della directory, tipo di creazione e autorità emittente di identità
- La ricerca consente ora la ricerca combinata di nomi, messaggi di posta elettronica e ID oggetto
- Filtro migliorato per tipo di utente (membro e Guest), stato di sincronizzazione della directory e tipo di creazione

> [!NOTE]
> Questa versione di anteprima non è attualmente disponibile per Azure AD B2C tenant.

## <a name="find-the-preview"></a>Trova l'anteprima

L'anteprima è attivata per impostazione predefinita, quindi è possibile usarla immediatamente. È possibile consultare le funzionalità e i miglioramenti più recenti selezionando **funzionalità di anteprima** nella pagina **tutti gli utenti** . Tutte le pagine aggiornate come parte di questa versione di anteprima visualizzeranno un tag di anteprima. Se si verificano problemi, è possibile tornare all'esperienza legacy:

1. Accedere al centro di [amministrazione Azure ad](https://aad.portal.azure.com) e selezionare **utenti**.
1. Dalla pagina **utenti-tutti gli utenti** selezionare il banner nella parte superiore della pagina.
1. Nel riquadro **funzionalità di anteprima** disattivare **Gestione utenti avanzata** .

   ![Come e dove attivare e disattivare la gestione avanzata degli utenti](./media/users-search-enhanced/enable-preview.png)

Microsoft è lieta di ricevere commenti e suggerimenti per migliorare l'esperienza.

## <a name="more-user-properties"></a>Altre proprietà utente

Sono state apportate alcune modifiche alle colonne disponibili nelle pagine **tutti gli utenti** e **utenti eliminati** . Oltre alle colonne esistenti fornite per la gestione dell'elenco di utenti, abbiamo aggiunto altre colonne.

### <a name="all-users-page"></a>Pagina tutti gli utenti

Di seguito sono riportate le proprietà utente visualizzate nella pagina **tutti gli utenti** :

- Nome: il nome visualizzato dell'utente.
- Nome entità utente: nome dell'entità utente (UPN) dell'utente.
- Tipo di utente: il tipo di utente utente, membro o Guest.
- Directory sincronizzata: indica se l'utente è sincronizzato da una directory locale.
- Autorità emittente di identità: autorità emittenti dell'identità usata per accedere a un account utente.
- ID oggetto: ID oggetto dell'utente.
- Tipo di creazione: indica il modo in cui è stato creato l'account utente.
- Nome della società: il nome della società a cui è associato l'utente.
- Stato invito: stato dell'invito per un utente Guest.
- Mail: il messaggio di posta elettronica dell'utente.

   ![nuove proprietà utente visualizzate in tutte le pagine utenti e utenti eliminati](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Pagina utenti eliminati

Nella pagina **utenti eliminati** sono incluse tutte le colonne disponibili nella pagina **tutti gli utenti** e alcune colonne aggiuntive, ovvero:

- Data di eliminazione: la data in cui l'utente è stato eliminato per la prima volta dall'organizzazione (l'utente è ripristinabile).
- Data di eliminazione permanente: la data in cui l'utente è stato eliminato definitivamente dall'organizzazione.

Alcune colonne vengono visualizzate per impostazione predefinita. Per aggiungere altre colonne, selezionare le **colonne** nella pagina, selezionare i nomi di colonna che si desidera aggiungere e selezionare **OK** per salvare le preferenze.

### <a name="identity-issuers"></a>Autorità emittenti di identità

Selezionare una voce nella colonna **emittente identità** per qualsiasi utente per visualizzare ulteriori dettagli sull'autorità emittente, inclusi il tipo di accesso e l'ID assegnato all'autorità emittente. Le voci della colonna **Issuer Identity** possono essere multivalore. Se sono presenti più autorità emittenti dell'identità dell'utente, la parola multipla verrà visualizzata nella colonna **autorità emittente identità** in tutte le pagine **utenti** e **utenti eliminati** e nel riquadro dei dettagli verranno elencate tutte le autorità emittenti.

> [!NOTE]
> La colonna di **origine** viene sostituita da più colonne, tra cui il **tipo di creazione**, la **sincronizzazione della directory**e l'emittente di **identità** , per un filtro più granulare.

## <a name="user-list-search"></a>Ricerca elenco utenti

Quando si immette una stringa di ricerca, la ricerca utilizza la ricerca "inizia con", che ora può corrispondere ai nomi, ai messaggi di posta elettronica o agli ID oggetto in una singola ricerca. È possibile immettere uno di questi attributi nella casella di ricerca e la ricerca verrà automaticamente esaminata in tutte queste proprietà per restituire i risultati corrispondenti. È possibile eseguire la stessa ricerca nelle pagine **tutti gli utenti** e **gli utenti eliminati** .

## <a name="user-list-filtering"></a>Filtro elenco utenti

Sono state migliorate le funzionalità di filtro per offrire più opzioni di filtro per le pagine **tutti gli utenti** e **gli utenti eliminati** . È ora possibile filtrare in base a più proprietà contemporaneamente ed è possibile filtrare in base a più proprietà.

### <a name="filtering-all-users-list"></a>Elenco di filtri per tutti gli utenti

Di seguito sono riportate le proprietà filtrabili della pagina **tutti gli utenti** :

- Tipo utente-membro o Guest
- Stato sincronizzato della directory-Sì
- Tipo di creazione: invito, messaggio di posta elettronica verificato, account locale
- Stato invito-accettazione in sospeso, accettato
- Unità amministrativa: selezionare questa opzione per limitare l'ambito degli utenti che si visualizzano a una singola unità amministrativa. Per altre informazioni, vedere l' [anteprima della gestione delle unità amministrative](directory-administrative-units.md).

## <a name="filtering-deleted-users-list"></a>Filtro elenco utenti eliminati

La pagina **utenti eliminati** contiene filtri aggiuntivi non presenti nella pagina **tutti gli utenti** . Di seguito sono riportate le proprietà filtrabili della pagina **utenti eliminati** :

- Tipo utente-membro o Guest
- Stato sincronizzato della directory-Sì
- Tipo di creazione: invito, messaggio di posta elettronica verificato, account locale
- Stato invito-accettazione in sospeso, accettato
- Data di eliminazione-ultimi 7, 14 o 30 giorni
- Data di eliminazione permanente-ultimi 7, 14 o 30 giorni

## <a name="frequently-asked-questions-faq"></a>Domande frequenti

Domanda | Risposta
-------- | ------
Che cosa accade alle funzionalità bulk per utenti e Guest? | Le operazioni bulk sono sempre disponibili per gli utenti e i guest, inclusi creazione bulk, invito bulk, eliminazione bulk e download degli utenti. Sono state appena unite in un menu denominato **operazioni bulk**. È possibile trovare le opzioni per **operazioni bulk** nella parte superiore della pagina **tutti gli utenti** .
Cosa è successo alla colonna di origine? | La colonna di **origine** è stata sostituita con altre colonne che forniscono informazioni simili, consentendo al contempo di filtrare i valori in modo indipendente. Tra gli esempi sono inclusi il **tipo di creazione**, la **sincronizzazione delle directory** e l'emittente di **identità**.
Cosa è successo alla colonna nome utente? | La colonna del **nome utente** è ancora presente, ma è stata rinominata come **nome dell'entità utente**. Ciò riflette meglio le informazioni contenute in tale colonna. Si noterà anche che il nome completo dell'entità utente è ora visualizzato per i guest B2B. Corrisponde a ciò che si otterrebbe in MS Graph.  
Perché è possibile eseguire una ricerca "inizia con" e non una ricerca "Contains"? | Esistono alcune limitazioni che impediscono di consentire l'esecuzione di una ricerca "Contains". Il feedback è stato aggiornato.
Perché non è possibile ordinare le colonne? | Esistono alcune limitazioni che impediscono di consentire l'ordinamento delle colonne. Il feedback è stato aggiornato.
Perché è possibile filtrare solo la colonna **sincronizzata della directory** per Sì? | Esistono alcune limitazioni che impediscono a Microsoft di filtrare questa proprietà senza alcun valore. Il feedback è stato aggiornato.

## <a name="next-steps"></a>Passaggi successivi

Operazioni sugli utenti

- [Aggiungere o modificare le informazioni sul profilo utente](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Aggiungere o eliminare utenti](../fundamentals/add-users-azure-active-directory.md)

Operazioni bulk

- [Scaricare l'elenco degli utenti](users-bulk-download.md)
- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Eliminazione di utenti in blocco](users-bulk-delete.md)
- [Ripristinare utenti in blocco](users-bulk-restore.md)
