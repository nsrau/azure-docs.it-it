---
title: Avvio rapido per visualizzare i gruppi e i membri dell'organizzazione - Azure Active Directory | Microsoft Docs
description: Istruzioni su come cercare e visualizzare i gruppi dell'organizzazione e i membri a essi assegnati.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.openlocfilehash: e03a5181bf9cbbfedfdd739eaa68e5757f29a116
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104320"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Avvio rapido: Visualizzare i gruppi e i membri dell'organizzazione in Azure Active Directory
È possibile visualizzare i gruppi esistenti dell'organizzazione e i membri dei gruppi usando il portale di Azure. I gruppi vengono usati per gestire gli utenti (membri) che necessitano tutti dello stesso accesso e delle stesse autorizzazioni per le app e i servizi con potenziali restrizioni.

In questa guida introduttiva si visualizzeranno tutti i gruppi esistenti dell'organizzazione e i membri assegnati.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario:

- Creare un tenant di Azure Active Directory. Per altre informazioni, vedere [Accedere al portale di Azure Active Directory e creare un nuovo tenant](active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
È necessario accedere al [portale di Azure](https://portal.azure.com/) con un account amministratore globale per la directory.

## <a name="create-a-new-group"></a>Creare un nuovo gruppo 
Creare un nuovo gruppo denominato _MDM policy - West_. Per altre informazioni sulla creazione di un gruppo, vedere [Come creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md).

1. Selezionare **Azure Active Directory**, **Gruppi** e quindi selezionare **Nuovo gruppo**.

2. Completare la pagina **Gruppo**:
    
    - **Tipo gruppo:** Scegliere **Sicurezza**
    
    - **Nome gruppo:** Digitare _criteri MDM - occidentale_
    
    - **Tipo di appartenenza:** Selezionare **Assegnato**.

3. Selezionare **Create**.

## <a name="create-a-new-user"></a>Creare un nuovo utente
Creare un nuovo utente denominato _Alain Charon_. Un utente deve esistere prima di essere aggiunto come membro di un gruppo. Per altre informazioni sulla creazione di un utente, vedere [Come aggiungere o eliminare utenti](add-users-azure-active-directory.md).

1. Selezionare **Azure Active Directory**, **Utenti** e quindi selezionare **Nuovo utente**.

2. Completare la pagina **Utente**:

    - **Nome:** Digitare _Alain Charon_.

    - **Nome utente:** Digitare *alain@contoso.com*.

3. Copiare la password generata automaticamente nella casella **Password** e quindi selezionare **Crea**.

## <a name="add-a-group-member"></a>Aggiungere un membro di un gruppo
Dopo aver creato un gruppo e un utente, è possibile aggiungere _Alain Charon_ come membro al gruppo _MDM policy - West_. Per altre informazioni sull'aggiunta di membri di un gruppo, vedere [Come aggiungere o rimuovere membri di un gruppo](active-directory-groups-members-azure-portal.md).

1. Selezionare **Azure Active Directory** > **Gruppi**.

2. Nella pagina **Gruppi - Tutti i gruppi** cercare e selezionare il gruppo **MDM policy - West**.

3. Nella pagina **Panoramica di MDM policy - West** selezionare **Membri** nell'area **Gestione**.

4. Selezionare **Aggiungi membri** e quindi cercare e selezionare **Alain Charon**.

5. Scegliere **Seleziona**.

## <a name="view-all-groups"></a>Visualizzare tutti i gruppi
È possibile visualizzare tutti i gruppi dell'organizzazione nella pagina **Gruppi - Tutti i gruppi** del portale di Azure.

- Selezionare **Azure Active Directory** > **Gruppi**.

    Verrà visualizzata la pagina **Gruppi - Tutti i gruppi** con tutti i gruppi attivi.

    ![Pagina Gruppi - Tutti i gruppi con tutti i gruppi esistenti](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Cercare il gruppo
Eseguire una ricerca nella pagina **Gruppi - Tutti i gruppi** per trovare il gruppo **MDM policy - West**.

1. Nella pagina **Gruppi - Tutti i gruppi** digitare _MDM_ nella casella **Cerca**.

    Nella casella **Cerca** vengono visualizzati i risultati della ricerca, incluso il gruppo _MDM policy - West_.

    ![Pagina Gruppi - Tutti i gruppi con la casella di ricerca compilata](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. Selezionare il gruppo **MDM policy - West**.

4. Visualizzare le informazioni sul gruppo nella pagina **MDM policy - West Overview**, incluso il numero di membri del gruppo.

    ![Pagina di panoramica MDM policy - West con le informazioni sui membri](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Visualizzare i membri del gruppo
Dopo aver trovato il gruppo, è possibile visualizzare tutti i membri assegnati.

- Selezionare **Membri** nell'area **Gestione** e quindi esaminare l'elenco completo dei nomi dei membri assegnati a tale gruppo specifico, incluso _Alain Charon_.

    ![Elenco dei membri assegnati al gruppo MDM policy - West](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Questo gruppo viene usato in diverse procedure disponibili nella sezione **Guide alle procedure** di questa documentazione. Se tuttavia si preferisce non usare questo gruppo, è possibile eliminarlo con i membri assegnati seguendo questa procedura:

1. Nella pagina **Gruppi - Tutti i gruppi** cercare il gruppo **MDM policy - West**.

2.  Selezionare il gruppo **MDM policy - West**.

    Viene visualizzata la pagina **Panoramica di MDM policy - West**.

3. Selezionare **Elimina**.

    Il gruppo e i membri associati vengono eliminati.

    ![Pagina di panoramica MDM policy - West con il collegamento Elimina evidenziato](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Questa operazione non elimina l'utente Alain Charon, ma solo l'appartenenza al gruppo eliminato.

## <a name="next-steps"></a>Passaggi successivi
Passare all'articolo successivo per informazioni su come associare una sottoscrizione ad Azure AD directory.

> [!div class="nextstepaction"]
> [Associare una sottoscrizione di Azure](active-directory-how-subscriptions-associated-directory.md)
