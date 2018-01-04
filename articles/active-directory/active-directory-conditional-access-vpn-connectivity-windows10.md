---
title: "Accesso condizionale di Azure Active Directory per la connettività VPN (anteprima) | Microsoft Docs"
description: "Informazioni sul funzionamento dell'accesso condizionale di Azure Active Directory per la connettività VPN. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 8941e631976eb11966c1f9ddd207af816df5dadf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Accesso condizionale di Azure Active Directory per la connettività VPN (anteprima)

L'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) consente di ottimizzare l'accesso alle risorse da parte degli utenti autorizzati. Con l'accesso condizionale di Azure AD per la connettività di rete privata virtuale (VPN) è possibile proteggere le connessioni VPN.


Per configurare l'accesso condizionale per la connettività VPN è necessario seguire questa procedura: 

1.  Configurare il server VPN.
2.  Configurare il client VPN.
3.  Configurare i criteri di accesso condizionale.


## <a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con i concetti seguenti:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN e accesso condizionale](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Per informazioni approfondite su come Microsoft implementa questa funzionalità, vedere [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Miglioramento dell'accesso remoto in Windows 10 con un profilo VPN automatico).   


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'accesso condizionale di Azure Active Directory per la connettività VPN è necessario che sia configurato un server VPN. 



## <a name="step-1-configure-your-vpn-server"></a>Passaggio 1: Configurare il server VPN 

Con questo passaggio si configurano i certificati radice per l'autenticazione VPN con Azure AD. Per configurare l'accesso condizionale per la connettività VPN è necessario eseguire queste operazioni:

1. Creare un certificato della rete VPN nel portale di Azure.
2. Scaricare il certificato della rete VPN.
2. Distribuire il certificato nel server VPN.

Azure AD usa il certificato della rete VPN per firmare i certificati rilasciati ai client di Windows 10 durante l'autenticazione ad Azure AD per la connettività VPN. Il token richiesto dal client di Windows 10 è un certificato che verrà quindi presentato all'applicazione, in questo caso il server VPN.

![Scaricare il certificato per l'accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Nel portale di Azure è possibile creare due certificati per gestire la transizione in prossimità della scadenza di uno dei due. Quando si crea un certificato è possibile indicare se si tratta di un certificato primario, usato durante l'autenticazione per firmare il certificato per la connessione.

Per creare un certificato della rete VPN:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Nel menu a sinistra fare clic su **Azure Active Directory**. 

    ![Selezionare Azure Active Directory](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Selezionare Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Nella sezione **Gestisci** della pagina **Accesso condizionale** fare clic su **Connettività VPN (anteprima)**.

    ![Selezionare Connettività VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Nella pagina della **connettività VPN** fare clic su **Nuovo certificato**.

    ![Selezionare Nuovo certificato](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Nella pagina **Nuovo** seguire questa procedura:

    ![Opzioni Seleziona durata e Primario](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Per **Seleziona durata** selezionare **1 anno**.

    b. Per **Primario**, selezionare **Sì**.

    c. Fare clic su **Crea**.

7. Nella pagina della connettività VPN fare clic su **Scarica certificato**.


È ora possibile distribuire il certificato appena creato nel server VPN. Nel server VPN aggiungere il certificato scaricato come *CA radice attendibile per l'autenticazione VPN*.

Per le distribuzioni basate su Windows RRAS, nel Server dei criteri di rete aggiungere il certificato radice nell'archivio *Enterprise NTauth* eseguendo questi comandi:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>Passaggio 2: Configurare il client VPN 

In questo passaggio si configura il profilo di connettività del client VPN, come descritto in [VPN e accesso condizionale](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>Passaggio 3: Configurare i criteri di accesso condizionale

Questa sezione contiene le istruzioni per configurare i criteri di accesso condizionale per la connettività VPN.


1. Nella pagina **Accesso condizionale** fare clic su **Aggiungi** nella barra degli strumenti in alto.

    ![Selezionare Aggiungi nella pagina Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Nella pagina **Nuovo** digitare un nome per il criterio nella casella **Nome**, ad esempio **Criteri VPN**.

    ![Aggiungere un nome per il criterio nella pagina Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

    ![Selezionare Utenti e gruppi](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Nella pagina **Utenti e gruppi** seguire questa procedura:

    ![Selezionare un utente di test](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Fare clic su **Seleziona utenti e gruppi**.

    b. Fare clic su **Seleziona**.

    c. Nella pagina **Seleziona** scegliere l'utente test e quindi fare clic su **Seleziona**.

    d. Nella pagina **Utenti e gruppi** fare clic su **Fatto**.

7. Nella pagina **Nuovo** seguire questa procedura:

    ![Selezionare App cloud](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Nella pagina **Assegnazioni** fare clic su **App cloud**.

    b. Nella pagina **App cloud** fare clic su **Selezionare le app** e quindi su **Seleziona**.

    c. Nella casella **Applicazioni** della pagina **Seleziona** digitare **vpn**.

    d. Selezionare **Server VPN**.

    e. Fare clic su **Seleziona**.


13. Nella pagina **Nuovo**, per aprire la pagina **Concedi**, fare clic su **Concedi** nella sezione **Controlli**.

    ![Selezionare Concedi](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Nella pagina **Concedi** seguire questa procedura:

    ![Selezionare Richiedi autenticazione a più fattori](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selezionare **Richiedi autenticazione a più fattori**.

    b. Fare clic su **Seleziona**.

15. Nella pagina **Nuovo** fare clic su **Sì** in **Abilita criteri**.

    ![Abilitare i criteri](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Nella pagina **Nuovo** fare clic su **Crea**.



## <a name="next-steps"></a>Passaggi successivi

Per informazioni approfondite su come Microsoft implementa questa funzionalità, vedere [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Miglioramento dell'accesso remoto in Windows 10 con un profilo VPN automatico).    

