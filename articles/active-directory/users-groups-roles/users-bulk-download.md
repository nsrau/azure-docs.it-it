---
title: Scaricare un elenco di utenti (anteprima) nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco i record utente nell'interfaccia di amministrazione di Azure in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38cc8fd4e063896bbd8843a54f0a01058462c618
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901451"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Scaricare un elenco di utenti (anteprima) nel portale di Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di importazione (creazione) dell'utente in blocco.

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per la creazione di un elenco di utenti può essere eseguita per un massimo di un'ora. In questo modo è possibile creare e scaricare un elenco di almeno 500.000 utenti.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per scaricare l'elenco di utenti dall'interfaccia di amministrazione di Azure AD, è necessario aver eseguito l'accesso con un utente assegnato a uno o più ruoli di amministratore a livello di organizzazione nel Azure AD. L'invito Guest e lo sviluppatore di applicazioni non sono considerati ruoli di amministratore.

## <a name="to-download-a-list-of-users"></a>Per scaricare un elenco di utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore utente nell'organizzazione.
1. In Azure ad selezionare **utenti** > **Scarica utenti**.
1. Nella pagina **Scarica utenti** selezionare **inizia** a ricevere un file CSV che elenca le proprietà del profilo utente. Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina risultati operazione bulk. Il file contiene il motivo per ogni errore.

   ![Selezionare la posizione desiderata per l'elenco degli utenti che si desidera scaricare](./media/users-bulk-download/bulk-download.png)

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato delle richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-download/bulk-center.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Eliminare gli utenti in blocco](users-bulk-delete.md)
- [Ripristino in blocco degli utenti](users-bulk-restore.md)
