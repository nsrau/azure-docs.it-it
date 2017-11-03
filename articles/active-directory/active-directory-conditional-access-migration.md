---
title: Migrare i criteri classici nel portale di Azure | Microsoft Docs
description: Migrare i criteri classici nel portale di Azure.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrare i criteri classici nel portale di Azure 


L'[accesso condizionale](active-directory-conditional-access-azure-portal.md) è una capacità di Azure Active Directory che consente di controllare come gli utenti autorizzati accedono alle app cloud. Anche se la finalità è comunque la stessa, il nuovo portale di Azure ha introdotto miglioramenti significativi nel funzionamento dell'accesso condizionale. I criteri di accesso condizionale configurati all'esterno del portale di Azure possono coesistere con i nuovi criteri creati nel portale di Azure. Se non sono stati disabilitati o rimossi, vengono applicati nell'ambiente in uso. Si consiglia tuttavia di migrare i criteri classici ai nuovi criteri di accesso condizionale di Azure Active Directory perché:

- I nuovi criteri consentono di gestire scenari che non è possibile gestire con i criteri classici.

- Il consolidamento consente di ridurre il numero di criteri da gestire.   

Questo argomento agevola la migrazione dei criteri classici esistenti ai nuovi criteri di accesso condizionale di Active Directory.


## <a name="classic-policies"></a>Criteri classici

I criteri di accesso condizionale per Azure Active Directory e Intune che non sono stati creati nel portale di Azure sono noti anche come **criteri classici**. Per migrare i criteri classici, non è necessario avere accesso al portale di Azure classico. Il portale di Azure offre infatti una [**vista**Criteri classica (anteprima)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) che consente di visualizzare i criteri classici.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Aprire un criterio classico

**Per aprire un criterio classico**

1. Nel [portale di Azure](https://portal.azure.com), sulla barra di spostamento a sinistra, fare clic su **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/02.png)
 
2. Nella sezione **Gestisci** della pagina **Accesso condizionale - Criteri**  fare clic su **Classic policies (preview)** (Criteri classici (anteprima).

3. Dall'elenco dei criteri classici, selezionare il criterio a cui si è interessati.   

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Criteri di accesso condizionale di Azure Active Directory

Questo argomento offre i passaggi dettagliati che consentono di eseguire la migrazione dei criteri classici anche se l'utente non ha familiarità con i criteri di accesso condizionale di Azure Active Directory. Tuttavia, acquisire familiarità con i concetti di base e la terminologia di Azure Active Directory relativi all'accesso condizionale consente di migliorare l'esperienza di migrazione.

Vedere:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md) per apprendere i concetti di base e la terminologia

- [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) per acquisire familiarità con l'interfaccia utente nel portale di Azure


 





## <a name="multi-factor-authentication-policy"></a>Criteri di autenticazione a più fattori 

Questo esempio illustra come eseguire la migrazione di un criterio classico che richiede l'autenticazione a più fattori** per un'applicazione cloud. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**Per migrare un criterio classico**

1. [Aprire il criterio classico](#open-a-classic-policy) per ottenere le impostazioni di configurazione.
2. Creare un nuovo criterio di accesso condizionale di Azure Active Directory per sostituire il criterio classico. 


### <a name="create-a-new-conditional-access-policy"></a>Creare un nuovo criterio di accesso condizionale


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

11. Disabilitare il criterio classico. 

    ![Accesso condizionale](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare un criterio di accesso condizionale, vedere [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) (Introduzione all'accesso condizionale in Azure Active Directory).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 
