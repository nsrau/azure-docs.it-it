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
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a810ae13d9cfb68d11293ba883c52858aa4a2deb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164754"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Ripristinare o rimuovere un utente eliminato di recente con Azure Active Directory
Dopo aver eliminato un utente, l'account rimane in uno stato di sospensione per 30 giorni. Durante tale intervallo di 30 giorni, l'account utente può essere ripristinato, con tutte le relative proprietà. Dopo il superamento di tale periodo di 30 giorni, l'utente viene automaticamente e definitivamente eliminato.

È possibile visualizzare gli utenti da ripristinare, ripristinare un utente eliminato o eliminare definitivamente un utente con Azure Active Directory (Azure AD) nel portale di Azure.

>[!Important]
>Un utente eliminato definitivamente non può essere ripristinato, nemmeno dal supporto tecnico Microsoft.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per ripristinare o eliminare definitivamente gli utenti, è necessario avere uno dei ruoli seguenti.

- Amministratore società

- Supporto partner - Livello 1

- Supporto partner - Livello 2

- Amministratore account utente

## <a name="view-your-restorable-users"></a>Visualizzare gli utenti ripristinabili
È possibile visualizzare tutti gli utenti che sono stati eliminati negli ultimi 30 giorni e che possono essere ripristinati.

### <a name="to-view-your-restorable-users"></a>Per visualizzare gli utenti ripristinabili
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare **Utenti eliminati**.

    Rivedere l'elenco di utenti che sono disponibili per il ripristino.

    ![Pagina Utenti - Utenti eliminati con gli utenti che possono essere ancora ripristinati](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Ripristinare un utente eliminato di recente
Mentre un account utente è sospeso, tutte le informazioni di directory correlate vengono mantenute. Quando si ripristina un utente, vengono ripristinate anche queste informazioni di directory.

### <a name="to-restore-a-user"></a>Per ripristinare un utente
1. Nella pagina **Utenti - Utenti eliminati** cercare e selezionare uno degli utenti disponibili. Ad esempio, _Mary Parker_.

2. Selezionare **Ripristina utente**.

    ![Pagina Utenti - Utenti eliminati, con l'opzione Ripristina utente evidenziata](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>eliminare in modo definitivo un utente
È possibile eliminare definitivamente un utente dalla directory senza aspettare i 30 giorni per l'eliminazione automatica. Un utente eliminato in modo permanente non può essere ripristinato né dall'utente, né da un altro amministratore, né dal supporto tecnico Microsoft.

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

- [Aggiungere utenti guest da un'altra directory](../b2b/what-is-b2b.md) 

Per altre informazioni sulle altre azioni di gestione disponibili, vedere [Documentazione sulla gestione degli utenti in Azure Active Directory](../users-groups-roles/index.yml).
