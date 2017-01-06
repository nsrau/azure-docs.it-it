---
title: Introduzione all&quot;accesso condizionale in Azure Active Directory (anteprima) | Documentazione Microsoft
description: "Verificare l&quot;accesso condizionale usando una condizione della località."
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f4f159c832865bc2ea0c24a8a072cade3bca8e33
ms.openlocfilehash: 327238372d84efdb7f2d58867a366821534fc945


---
# <a name="get-started-with-conditional-access-in-azure-active-directory---preview"></a>Introduzione all'accesso condizionale in Azure Active Directory (anteprima)

Il comportamento illustrato in questo argomento è attualmente in [anteprima](active-directory-preview-explainer.md).

L'accesso condizionale è una funzionalità di Azure Active Directory che consente di definire le condizioni in base alle quali gli utenti autorizzati possono accedere alle app. 

Questo argomento fornisce istruzioni per testare un accesso condizionale in base a una condizione della località nell'ambiente usato.  


## <a name="scenario-description"></a>Descrizione dello scenario

In molte organizzazioni è necessaria solo l'autenticazione a più fattori per l'accesso alle app che non viene eseguito dalla Intranet aziendale. Azure Active Directory permette di ottenere facilmente questo risultato mediante la configurazione di criteri di accesso condizionale basati sulla località. Questo argomento fornisce istruzioni dettagliate per la configurazione di criteri correlati, che fanno uso di [indirizzi IP attendibili](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) per distinguere tra i tentativi di accesso eseguiti dalla Intranet aziendale e da tutte le altre località.


## <a name="prerequisites"></a>Prerequisiti

Lo scenario descritto in questo argomento presuppone che si abbia familiarità con i concetti illustrati in [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Per testare questo scenario, seguire questa procedura:

- Creare un utente test. 

- Assegnare una licenza Azure AD Premium all'utente test.

- Configurare un'app gestita e assegnarle l'utente test.

- Configurare indirizzi IP attendibili.

Per altre informazioni sull'argomento, vedere [Indirizzi IP attendibili](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Procedura di configurazione dei criteri

**Per configurare i criteri di accesso condizionale, seguire questa procedura:**

1. Sulla barra di spostamento a sinistra nel portale di Azure fare clic su **Azure Active Directory**. 

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. Nella sezione **Gestisci** del pannello **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Nel pannello **Accesso condizionale** aprire il pannello **Nuovo** facendo clic su **Aggiungi** nella barra degli strumenti in alto.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Nel pannello **Nuovo** digitare un nome per i criteri nella casella di testo **Nome**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Nel pannello **Utenti e gruppi** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Fare clic su **Seleziona utenti e gruppi**.

    b. Fare clic su **Seleziona**.

    c. Nel pannello **Seleziona** scegliere l'utente test e quindi fare clic su **Seleziona**.

    d. Nel pannello **Utenti e gruppi** fare clic su **Fatto**.

7. Nel pannello **Nuovo** aprire il pannello **App cloud** nella sezione **Assegnazioni** facendo clic su **App cloud**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Nel pannello **App cloud** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Fare clic su **Selezionare le app**.

    b. Fare clic su **Seleziona**.

    c. Nel pannello **Seleziona** scegliere l'app cloud e quindi fare clic su **Seleziona**.

    d. Nel pannello **App cloud** fare clic su **Fatto**.

9. Nel pannello **Nuovo** aprire il pannello **Condizioni** nella sezione **Assegnazioni** facendo clic su **Condizioni**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. Nel pannello **Condizioni** aprire il pannello **Località** facendo clic su **Località**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Nel pannello **Località** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. In **Configura** fare clic su **Sì**.

    b. In **Includi** fare clic su **Tutte le località**.

    c. Fare clic su **Escludi** e quindi su **Tutti gli indirizzi IP attendibili**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Fare clic su **Done**.

12. Nel pannello **Condizioni** fare clic su **Fatto**.

13. Nel pannello **Nuovo** aprire il pannello **Concedi** nella sezione **Controlli** facendo clic su **Concedi**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Nel pannello **Concedi** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selezionare **Richiedi autenticazione a più fattori**.

    b. Fare clic su **Seleziona**.

15. Nel pannello **Nuovo** in **Abilita criteri** fare clic su **Sì**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Nel pannello **Nuovo** fare clic su **Crea**.


## <a name="testing-the-policy"></a>Test dei criteri

Per testare i criteri, seguire questa procedura: 

1. Accedere all'app da un dispositivo con un indirizzo IP compreso nell'intervallo di indirizzi IP attendibili configurato. 

1. Accedere all'app da un dispositivo con un indirizzo IP non compreso nell'intervallo di indirizzi IP attendibili configurato.

L'autenticazione a più fattori deve essere necessaria solo durante il tentativo di connessione da un dispositivo con un indirizzo IP non compreso nell'intervallo di indirizzi attendibili. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'argomento, vedere [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md).




<!--HONumber=Dec16_HO4-->


