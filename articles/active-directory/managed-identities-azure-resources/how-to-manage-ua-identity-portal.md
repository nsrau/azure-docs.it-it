---
title: Gestire un'identità gestita assegnata dall'utente nell'portale di Azure-Azure AD
description: Istruzioni dettagliate su come creare, elencare, eliminare e assegnare un ruolo a un'identità gestita assegnata dall'utente.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547452"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Le identità gestite per le risorse Azure forniscono ai servizi di Azure un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente tramite il portale di Azure.

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per creare l'identità gestita assegnata dall'utente.
2. Nella casella di ricerca digitare *Identità gestite* e in **Servizi** fare clic su **Identità gestite**.
3. Fare clic su **Aggiungi** e immettere i valori nei campi seguenti nel riquadro **Crea identità gestita assegnata dall'utente**:
   - **Nome risorsa**: si tratta del nome dell'identità gestita assegnata dall'utente, ad esempio UAI1.
   - **Sottoscrizione**: scegliere la sottoscrizione in cui creare l'identità gestita assegnata dall'utente
   - **Gruppo di risorse**: creare un nuovo gruppo di risorse che conterrà l'identità gestita assegnata dall'utente o scegliere **Usa esistente** per creare l'identità gestita assegnata dall'utente in un gruppo di risorse esistente.
   - **Posizione**: scegliere la posizione in cui distribuire l'identità gestita assegnata dall'utente, ad esempio **Stati Uniti occidentali**.
4. Fare clic su **Create**(Crea).

![Creare un'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator) o [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per elencare le identità gestite assegnate dall'utente.
2. Nella casella di ricerca digitare *Identità gestite* e in Servizi fare clic su **Identità gestite**.
3. Verrà restituito un elenco delle identità gestite assegnate dall'utente per la sottoscrizione.  Per visualizzare i dettagli di un'identità gestita assegnata dall'utente, fare clic sul nome dell'identità.

![Elencare l'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

L'eliminazione di un'identità assegnata dall'utente non porta alla rimozione di tale identità dalla risorsa o macchina virtuale a cui era stata assegnata.  Per rimuovere un'identità assegnata dall'utente da una macchina virtuale consultare [Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per eliminare l'identità gestita assegnata dall'utente.
2. Selezionare l'identità gestita assegnata dall'utente e fare clic su **Elimina**.
3. Nella finestra di conferma scegliere **Sì**.

![Eliminare un'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Assegnare un ruolo a un'identità gestita assegnata dall'utente 

Per assegnare un ruolo a un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Amministratore Accesso utenti](/azure/role-based-access-control/built-in-roles#user-access-administrator).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per elencare le identità gestite assegnate dall'utente.
2. Nella casella di ricerca digitare *Identità gestite* e in Servizi fare clic su **Identità gestite**.
3. Verrà restituito un elenco delle identità gestite assegnate dall'utente per la sottoscrizione.  Selezionare l'identità gestita assegnata dall'utente a cui si vuole assegnare un ruolo.
4. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi un'assegnazione di ruolo**.

   ![Avvio dell'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Nel pannello Aggiungi un'assegnazione di ruolo configurare i valori seguenti e quindi fare clic su **Salva**:
   - **Ruolo**: il ruolo da assegnare
   - **Assegna accesso a**: la risorsa a cui assegnare l'identità gestita assegnata dall'utente
   - **Seleziona**: il membro a cui assegnare l'accesso
   
   ![Identità gestita assegnata dall'utente - IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
