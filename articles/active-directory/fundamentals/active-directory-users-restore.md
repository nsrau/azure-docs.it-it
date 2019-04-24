---
title: Ripristinare o rimuovere definitivamente un utente eliminato di recente - Azure Active Directory | Microsoft Docs
description: Informazioni su come visualizzare gli utenti ripristinabili, ripristinare un utente eliminato o eliminare definitivamente un utente con Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60248234"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Ripristinare o rimuovere un utente eliminato di recente con Azure Active Directory
Dopo aver eliminato un utente, l'account rimane in uno stato di sospensione per 30 giorni. Durante tale intervallo di 30 giorni, l'account utente può essere ripristinato, con tutte le relative proprietà. Dopo il superamento di tale periodo di 30 giorni, l'utente viene automaticamente e definitivamente eliminato.

È possibile visualizzare gli utenti da ripristinare, ripristinare un utente eliminato o eliminare definitivamente un utente con Azure Active Directory (Azure AD) nel portale di Azure.

>[!Important]
>Un utente eliminato definitivamente non può essere ripristinato, nemmeno dal supporto tecnico Microsoft.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per ripristinare o eliminare definitivamente gli utenti, è necessario avere uno dei ruoli seguenti.

- Amministratore globale

- Supporto partner - Livello 1

- Supporto partner - Livello 2

- Amministratore utenti

## <a name="view-your-restorable-users"></a>Visualizzare gli utenti ripristinabili
È possibile visualizzare tutti gli utenti che sono stati eliminati negli ultimi 30 giorni e che possono essere ripristinati.

### <a name="to-view-your-restorable-users"></a>Per visualizzare gli utenti ripristinabili
1. Accedi per il [portale di Azure](https://portal.azure.com/) usando un account amministratore globale per l'organizzazione.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare **Utenti eliminati**.

    Rivedere l'elenco di utenti che sono disponibili per il ripristino.

    ![Pagina Utenti - Utenti eliminati con gli utenti che possono essere ancora ripristinati](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Ripristinare un utente eliminato di recente

Quando viene eliminato un account utente dell'organizzazione, l'account è in uno stato sospeso e viene mantenute tutte le informazioni correlate dell'organizzazione. Quando si ripristina un utente, viene ripristinate anche queste informazioni dell'organizzazione.

> [!Note]
> Una volta che un utente viene ripristinato, anche se sono non disponibili postazioni per le licenze vengono ripristinate anche le licenze assegnate all'utente al momento dell'eliminazione. Se quindi utilizzano più di acquistate più licenze, l'organizzazione potrebbe essere temporaneamente non conformi per l'utilizzo delle licenze.

### <a name="to-restore-a-user"></a>Per ripristinare un utente
1. Nella pagina **Utenti - Utenti eliminati** cercare e selezionare uno degli utenti disponibili. Ad esempio, _Mary Parker_.

2. Selezionare **Ripristina utente**.

    ![Pagina Utenti - Utenti eliminati, con l'opzione Ripristina utente evidenziata](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>eliminare in modo definitivo un utente
È possibile eliminare definitivamente un utente dell'organizzazione senza tempi di attesa di 30 giorni per l'eliminazione automatica. Un utente eliminato in modo permanente non può essere ripristinato né dall'utente, né da un altro amministratore, né dal supporto tecnico Microsoft.

>[!Note]
>Se si elimina definitivamente un utente per errore, sarà necessario creare un nuovo utente e immettere manualmente tutte le informazioni precedenti. Per altre informazioni sulla creazione di un utente, vedere [Aggiungere o eliminare utenti](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Per eliminare definitivamente un utente

1. Nella pagina **Utenti - Utenti eliminati** cercare e selezionare uno degli utenti disponibili. Ad esempio, _Rae Huff_.

2. Selezionare **Elimina definitivamente**.

    ![Pagina Utenti - Utenti eliminati, con l'opzione Ripristina utente evidenziata](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ripristinato o eliminato gli utenti, è possibile eseguire i processi di base seguenti:

- [Aggiungere o eliminare utenti](add-users-azure-active-directory.md)

- [Assegnare ruoli agli utenti](active-directory-users-assign-role-azure-portal.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)

- [Aggiungere utenti guest da un'altra organizzazione](../b2b/what-is-b2b.md)

Per altre informazioni sulle altre attività di gestione utente disponibili, [documentazione di gestione di Azure AD dell'utente](../users-groups-roles/index.yml).
