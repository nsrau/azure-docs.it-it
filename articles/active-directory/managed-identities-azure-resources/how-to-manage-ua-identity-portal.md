---
title: Gestire un'identità gestita assegnata dall'utente nell'portale di Azure-Azure AD
description: Istruzioni dettagliate su come creare, elencare, eliminare e assegnare un ruolo a un'identità gestita assegnata dall'utente.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5045c81f280d9d7687a466aed2e918d21eec697
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266388"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure

Le identità gestite per le risorse di Azure offrono servizi di Azure con un'identità gestita in Azure Active Directory. È possibile usare questa identità per l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#managed-identity-types)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Per creare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per creare l'identità gestita assegnata dall'utente.
2. Nella casella di ricerca digitare *identità gestite*e in **Servizi**fare clic su **identità gestite**.
3. Fare clic su **Aggiungi** e immettere i valori nei campi seguenti nel riquadro **Crea identità gestita assegnata dall'utente**:
    - **Sottoscrizione**: scegliere la sottoscrizione in cui creare l'identità gestita assegnata dall'utente.
    - **Gruppo di risorse**: scegliere un gruppo di risorse per creare l'identità gestita assegnata dall'utente in oppure fare clic su **Crea nuovo** per creare un nuovo gruppo di risorse.
    - **Area**: scegliere un'area per distribuire l'identità gestita assegnata dall'utente, ad esempio **Stati Uniti occidentali**.
    - **Nome**: nome dell'identità gestita assegnata dall'utente, ad esempio UAI1.
    ![Creare un'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Fare clic su **Verifica + Crate** per esaminare le modifiche.
5. Fare clic su **Crea**.

## <a name="list-user-assigned-managed-identities"></a>Elencare le identità gestite assegnate dall'utente

Per elencare/leggere un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per elencare le identità gestite assegnate dall'utente.
2. Nella casella di ricerca digitare *Identità gestite* e in Servizi fare clic su **Identità gestite**.
3. Verrà restituito un elenco delle identità gestite assegnate dall'utente per la sottoscrizione.  Per visualizzare i dettagli di un'identità gestita assegnata dall'utente, fare clic sul nome dell'identità.

![Elencare l'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminare un'identità gestita assegnata dall'utente

Per eliminare un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Collaboratore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

L'eliminazione di un'identità assegnata dall'utente non porta alla rimozione di tale identità dalla risorsa o macchina virtuale a cui era stata assegnata.  Per rimuovere un'identità assegnata dall'utente da una macchina virtuale consultare [Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per eliminare l'identità gestita assegnata dall'utente.
2. Selezionare l'identità gestita assegnata dall'utente e fare clic su **Elimina**.
3. Nella finestra di conferma scegliere **Sì**.

![Eliminare un'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Assegnare un ruolo a un'identità gestita assegnata dall'utente 

Per assegnare un ruolo a un'identità gestita assegnata dall'utente, all'account deve essere assegnato il ruolo [Amministratore Accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator).

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account associato alla sottoscrizione di Azure per elencare le identità gestite assegnate dall'utente.
2. Nella casella di ricerca digitare *Identità gestite* e in Servizi fare clic su **Identità gestite**.
3. Verrà restituito un elenco delle identità gestite assegnate dall'utente per la sottoscrizione.  Selezionare l'identità gestita assegnata dall'utente a cui si vuole assegnare un ruolo.
4. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi assegnazione di ruolo**.

   ![Avvio dell'identità gestita assegnata dall'utente](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Nel pannello Aggiungi assegnazione ruolo configurare i valori seguenti e quindi fare clic su **Salva**:
   - **Ruolo**: il ruolo da assegnare
   - **Assegna accesso a**: la risorsa a cui assegnare l'identità gestita assegnata dall'utente
   - **Seleziona**: il membro a cui assegnare l'accesso
   
   ![Identità gestita assegnata dall'utente - IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)