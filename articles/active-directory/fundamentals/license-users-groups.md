---
title: Assegnare o rimuovere licenze - Azure Active Directory | Microsoft Docs
description: Istruzioni su come assegnare o rimuovere le licenze di Azure Active Directory da utenti o gruppi.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f76e766d0aedae88eb11dd244f71f130f77c57e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561661"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Assegnare o rimuovere licenze usando il portale di Azure Active Directory
Molti servizi di Azure Active Directory (Azure AD) richiedono l'attivazione di un prodotto Azure AD e la concessione della licenza ai singoli utenti o gruppi (e relativi membri associati) per quel prodotto. Solo gli utenti con licenze attive saranno in grado di eseguire l'accesso e usare i servizi di Azure AD concessi in licenza.

## <a name="available-product-editions"></a>Edizioni del prodotto disponibili
Sono disponibili diverse edizioni del prodotto Azure AD.

- Azure AD Gratuito

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

Per informazioni specifiche su ogni edizione del prodotto e i dettagli di licenza associati, vedere [Informazioni sulle licenze necessarie](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Visualizzare i dettagli sull'edizione del prodotto e la licenza
È possibile visualizzare i prodotti disponibili, tra cui le singole licenze, verificando eventuali date di scadenza in sospeso e il numero di assegnazioni disponibili.

### <a name="to-find-your-product-and-license-details"></a>Per reperire i dettagli sul proprio prodotto e la licenza
1. Accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

2. Selezionare **Azure Active Directory**, e quindi **Licenze**.

    Viene visualizzata la pagina **Licenze**.

    ![Pagina licenze, con il numero di prodotti acquistati e licenze assegnate](media/license-users-groups/license-details-blade.png)
    
3. Selezionare il collegamento **Purchased products** (Prodotti acquistati) per visualizzare la pagina **Prodotti** e visualizzare i dettagli **Assegnata**, **Disponibile** e **Scadenza imminente** per ogni specifica edizione del prodotto.

    ![Pagina Prodotti con le edizioni del prodotto e le informazioni di licenza associate](media/license-users-groups/license-products-blade-with-products.png)

4. Selezionare il nome di un'edizione del prodotto per visualizzare i relativi utenti e gruppi con licenza.

## <a name="assign-licenses-to-users-or-groups"></a>Assegnare licenze a utenti o gruppi
Verificare che chiunque necessiti di usare un servizio Azure AD concesso in licenza abbia la licenza appropriata. Si può decidere se aggiungere i diritti di licenza ai singoli utenti o a un intero gruppo.

>[!Note]
>La gestione delle licenze basata su gruppi è una funzionalità in anteprima pubblica di Azure AD ed è disponibile con qualsiasi piano di licenza a pagamento di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Per informazioni dettagliate su come aggiungere gli utenti, vedere [Come aggiungere o eliminare utenti in Azure Active Directory](add-users-azure-active-directory.md). Per informazioni dettagliate su come creare gruppi e aggiungervi membri, vedere [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Per assegnare una licenza a un utente specifico
1. Nella pagina **Prodotti** selezionare il nome dell'edizione da assegnare all'utente. Ad esempio, _Azure Active Directory Premium - Piano 2_.

    ![Pagina Prodotti con l'edizione del prodotto evidenziata](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Nella pagina **Azure Active Directory Premium - Piano 2** selezionare **Assegna**.

    ![Pagina Prodotti con l'opzione Assegna evidenziata](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Nella pagina **Assegna** selezionare **Utenti e gruppi** e quindi cercare e selezionare l'utente a cui si sta assegnando la licenza. Ad esempio, _Mary Parker_.

    ![Pagina Assegna licenza con le opzioni di ricerca e selezione evidenziate](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Selezionare **Opzioni di assegnazione**, verificare che siano attivate le opzioni di licenza appropriate e fare clic su **OK**.

    ![Pagina di opzioni di licenza, con tutte le opzioni disponibili nell'edizione](media/license-users-groups/license-option-blade-assignments.png)

    La pagina **Assegna licenza** viene aggiornata per indicare che un utente è selezionato e che le assegnazioni sono configurate.

    >[!NOTE]
    >Non tutti i servizi Microsoft sono disponibili in tutte le posizioni. Prima che una licenza possa essere assegnata a un utente, è necessario specificare la **Località di utilizzo**. È possibile impostare questo valore nell'area **Azure Active Directory &gt; Utenti &gt; Profilo &gt; Impostazioni** in Azure AD.

5. Selezionare **Assegna**.

    L'utente viene aggiunto all'elenco degli utenti con licenza e può accedere ai servizi di Azure AD inclusi.

### <a name="to-assign-a-license-to-an-entire-group"></a>Per assegnare una licenza a un intero gruppo
1. Nella pagina **Prodotti** selezionare il nome dell'edizione da assegnare all'utente. Ad esempio, _Azure Active Directory Premium - Piano 2_.

    ![Pannello Prodotti con l'edizione del prodotto evidenziata](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. Nella pagina **Azure Active Directory Premium - Piano 2** selezionare **Assegna**.

    ![Pagina Prodotti con l'opzione Assegna evidenziata](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. Nella pagina **Assegna** selezionare **Utenti e gruppi** e quindi cercare e selezionare il gruppo a cui si sta assegnando la licenza. Ad esempio, _MDM policy - West_.

    ![Pagina Assegna licenza con le opzioni di ricerca e selezione evidenziate](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Selezionare **Opzioni di assegnazione**, verificare che siano attivate le opzioni di licenza appropriate e fare clic su **OK**.

    ![Pagina di opzioni di licenza, con tutte le opzioni disponibili nell'edizione](media/license-users-groups/license-option-blade-group-assignments.png)

    La pagina **Assegna licenza** viene aggiornata per indicare che un utente è selezionato e che le assegnazioni sono configurate.

    >[!NOTE]
    >Non tutti i servizi Microsoft sono disponibili in tutte le posizioni. Prima che una licenza possa essere assegnata a un gruppo, è necessario specificare la **Località di utilizzo** per tutti i membri. È possibile impostare questo valore nell'area **Azure Active Directory &gt; Utenti &gt; Profilo &gt; Impostazioni** in Azure AD. Qualsiasi utente per il quale non sia specificata la località di utilizzo eredita la località del tenant.

5. Selezionare **Assegna**.

    Il gruppo viene aggiunto all'elenco di gruppi con licenza e tutti i membri hanno accesso ai servizi di Azure AD inclusi.

## <a name="remove-a-license"></a>Rimuovere una licenza
È possibile rimuovere una licenza da un utente o un gruppo dalla pagina **Licenze**.

### <a name="to-remove-a-license-from-a-specific-user"></a>Per rimuovere una licenza da un utente specifico
1. Nella pagina **Utenti con licenza** per l'edizione del prodotto selezionare l'utente che non deve più avere la licenza. Ad esempio, _Alain Charon_.

2. Selezionare **Rimuovi licenza**.

    ![Pagina Utenti con licenza con l'opzione Rimuovi licenza evidenziata](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Per rimuovere una licenza da un gruppo
1. Nella pagina **Gruppi con licenza** per l'edizione del prodotto selezionare il gruppo che non deve più avere la licenza. Ad esempio, _MDM policy - West_.

2. Selezionare **Rimuovi licenza**.

    ![Pagina Gruppi con licenza con l'opzione Rimuovi licenza evidenziata](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Le licenze che l'utente ha ereditato da un gruppo non possono essere rimosse direttamente. In alternativa, è necessario rimuovere l'utente dal gruppo da cui sta ereditando la licenza.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver assegnato le licenze, è possibile eseguire i processi seguenti:

- [Identificare e risolvere i problemi relativi alle licenze](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Aggiungere gli utenti con licenza a un gruppo per la gestione delle licenze](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenari, limitazioni e problemi noti relativi all'uso dei gruppi per gestire le licenze in Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Aggiungere o modificare le informazioni sul profilo utente](active-directory-users-profile-azure-portal.md)
