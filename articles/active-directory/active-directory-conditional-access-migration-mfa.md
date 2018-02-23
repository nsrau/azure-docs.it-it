---
title: "Eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure | Microsoft Docs"
description: "Questo articolo illustra come eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure."
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 77484dc3773736ea15c39ede5f9d49b6b694d960
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure 

Questo articolo illustra come eseguire la migrazione di criteri classici che richiedono  **l'autenticazione a più fattori** per un'applicazione cloud. Anche se non è un prerequisito, è consigliabile leggere [Migrare i criteri classici nel portale di Azure](active-directory-conditional-access-migration.md) prima di iniziare la migrazione dei criteri classici.


 
## <a name="overview"></a>Panoramica 

Lo scenario in questo articolo illustra come eseguire la migrazione di criteri classici che richiedono  **l'autenticazione a più fattori** per un'applicazione cloud. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Il processo di migrazione è costituito dai passaggi seguenti:

1. [Aprire il criterio classico](#open-a-classic-policy) per ottenere le impostazioni di configurazione.
2. Creare un nuovo criterio di accesso condizionale di Azure Active Directory per sostituire il criterio classico. 
3. Disabilitare il criterio classico.



## <a name="open-a-classic-policy"></a>Aprire un criterio classico

1. Nel [portale di Azure](https://portal.azure.com), sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration-mfa/02.png)

3. Nella sezione **Gestisci**, fare clic su **Criteri classici (anteprima)**.

    ![Criteri classici](./media/active-directory-conditional-access-migration-mfa/12.png)

4. Nell'elenco di criteri classici, fare clic sui criteri classici che richiedono l'**autenticazione a più fattori** per un'applicazione cloud.

    ![Criteri classici](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Creare un nuovo criterio di accesso condizionale


1. Nel [portale di Azure](https://portal.azure.com), sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/02.png)



3. Nella pagina **Accesso condizionale** aprire la **Nuovo** facendo clic su **Aggiungi** nella barra degli strumenti in alto.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/03.png)

4. Nella pagina **Nuovo** digitare un nome per il criterio nella casella di testo **Nome**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/29.png)

5. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/05.png)

    a. Se nel criterio classico sono selezionati tutti gli utenti, fare clic su **Tutti gli utenti**. 

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/35.png)

    b. Se nel criterio classico sono selezionati i gruppi, fare clic su **Utenti e gruppi**, quindi selezionare i gruppi e gli utenti necessari.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/36.png)

    c. Se sono presenti gruppi esclusi, fare clic sulla scheda**Escludi** e quindi selezionare i gruppi e gli utenti necessari. 

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/37.png)

6. Nella pagina **Nuovo**, per aprire la pagina **App cloud**, nella sezione **Assegnazioni** fare clic su **App cloud**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Nella pagina **App cloud** attenersi alla procedura seguente:

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/08.png)

    a. Fare clic su **Selezionare le app**.

    b. Fare clic su **Seleziona**.

    c. Nella pagina **Seleziona** scegliere l'app cloud e quindi fare clic su **Seleziona**.

    d. Nella pagina **App cloud** fare clic su **Fatto**.



9. Se è stato selezionato **Richiedi autenticazione a più fattori**:

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/26.png)

    a. Nella sezione **Controlli di accesso** fare clic su **Concedi**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/27.png)

    b. Nella pagina **Concedi**, fare clic su **Concedi accesso**e quindi su **Richiedi autenticazione a più fattori**.

    c. Fare clic su **Seleziona**.


10. Fare clic su **On** per abilitare il criterio.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>Disabilitare i criteri classico

Per disabilitare i criteri classici, fare clic su **Disabilita** nella visualizzazione **Dettagli**.

![Criteri classici](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione dei criteri classici, vedere [Migrare i criteri classici nel portale di Azure](active-directory-conditional-access-migration.md).


- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
