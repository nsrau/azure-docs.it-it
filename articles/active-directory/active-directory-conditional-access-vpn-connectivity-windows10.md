---
title: "Accesso condizionale di Azure Active Directory per la connettività di rete privata virtuale (anteprima) | Microsoft Docs"
description: "Informazioni su come funziona l'accesso condizionale di Azure Active Directory per la connettività di rete privata virtuale. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.contentlocale: it-it
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>Accesso condizionale di Azure Active Directory per la connettività di rete privata virtuale (anteprima)

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), è possibile ottimizzare come gli utenti autorizzati possono accedere alle risorse. L'accesso condizionale di Azure AD per la connettività di rete privata virtuale (VPN) consente di usare questa particolare modalità di accesso per proteggere le connessioni VPN.


Per configurare l'accesso condizionale di Azure AD per la connettività VPN, è necessario completare i passaggi seguenti: 

1.  Configurare il server VPN
2.  Configurare il client VPN 
3.  Configurare i criteri di accesso condizionale
4.  Verifica


## <a name="before-you-begin"></a>Prima di iniziare

Questo argomento presuppone che l'utente abbia familiarità con i concetti seguenti:

- [Accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- [VPN e accesso condizionale](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Può inoltre essere opportuno consultare [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Miglioramento dell'accesso remoto in Windows 10 con un profilo VPN automatico) per ottenere informazioni approfondite su come Microsoft ha implementato questa funzionalità.   


## <a name="prerequisites"></a>Prerequisiti

Per configurare l'accesso condizionale di Azure Active Directory per la connettività di rete privata virtuale, è necessario che sia configurato un server VPN. 



## <a name="step-1---configure-your-vpn-server"></a>Passaggio 1: Configurare il server VPN 

L'obiettivo di questo passaggio consiste nel configurare i certificati radice per l'autenticazione VPN con Azure AD. Per configurare l'accesso condizionale per la connettività di rete privata virtuale, è necessario eseguire queste operazioni:

1. Creare un certificato della rete VPN nel portale di Azure
2. Scaricare il certificato della rete VPN
2. Distribuire il certificato al server VPN

Il certificato della rete VPN è l'autorità emittente usata da Azure AD per firmare i certificati rilasciati ai client di Windows 10 durante l'autenticazione ad Azure AD per la connettività VPN. È possibile immaginare il token richiesto dal client di Windows 10 come un certificato che verrà quindi presentato all'applicazione, ovvero il server VPN, nel caso specifico.

![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Nel portale di Azure è possibile creare due certificati per gestire le transizioni in prossimità della scadenza del certificato. Quando si crea un certificato, è possibile specificare se è primario. Il certificato primario è quello effettivamente usato durante l'autenticazione per firmare il certificato per la connessione.


**Per creare un certificato della rete VPN:**

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Sulla barra di spostamento a sinistra fare clic su **Azure Active Directory**. 

    ![Connettività VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Nella sezione **Gestisci** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

    ![Connettività VPN](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Nella sezione **Gestisci** della pagina **Accesso condizionale** fare clic su **Connettività VPN (anteprima)**.

    ![Connettività VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Nella pagina della **connettività VPN** fare clic su **Nuovo certificato**.

    ![Connettività VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Nella pagina **Nuovo** seguire questa procedura:

    ![Connettività VPN](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. Come **durata**, selezionare **1 anno**.

    b. Per **Primario**, selezionare **Sì**.

    c. Fare clic su **Crea**.

7. Nella pagina della connettività VPN fare clic su **Scarica certificato**.


A questo punto si è pronti per distribuire il certificato appena creato al server VPN. Nel server VPN è necessario aggiungere il certificato scaricato come *CA radice attendibile per l'autenticazione VPN*.

Per le distribuzioni basate su Windows RRAS, nel server dei criteri di rete è necessario aggiungere il certificato radice nell'archivio *Enterprise NTauth* eseguendo questi comandi:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>Passaggio 2: Configurare il client VPN 

In questo passaggio è necessario configurare il profilo di connettività del client VPN, come descritto in [VPN e accesso condizionale](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3---configure-your-conditional-access-policy"></a>Passaggio 3: Configurare i criteri di accesso condizionale

Questa sezione contiene le istruzioni per configurare i criteri di accesso condizionale per la connettività VPN.

**Per configurare i criteri di accesso condizionale:** 

1. Nella pagina **Accesso condizionale** fare clic su **Aggiungi** nella barra degli strumenti in alto.

    ![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Nella pagina **Nuovo** digitare un nome per i criteri nella casella di testo **Nome**, ad esempio **Criteri VPN**.

    ![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

    ![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Nella pagina **Utenti e gruppi** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Fare clic su **Seleziona utenti e gruppi**.

    b. Fare clic su **Seleziona**.

    c. Nella pagina **Seleziona** scegliere l'utente test e quindi fare clic su **Seleziona**.

    d. Nella pagina **Utenti e gruppi** fare clic su **Fatto**.

7. Nella pagina **Nuovo** seguire questa procedura.

    ![Accesso condizionale](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Nella pagina **Assegnazioni** fare clic su **App cloud**.

    b. Nella pagina **App cloud** fare clic su **Selezionare le app** e quindi su **Seleziona**.

    c. Nella casella di testo **Applicazioni** della pagina **Seleziona** digitare **vpn**.

    d. Selezionare **Server VPN**.

    e. Fare clic su **Seleziona**.


13. Nella pagina **Nuovo**, per aprire la pagina **Concedi**, fare clic su **Concedi** nella sezione **Controlli**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Nella pagina **Concedi** seguire questa procedura:

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Selezionare **Richiedi autenticazione a più fattori**.

    b. Fare clic su **Seleziona**.

15. Nella pagina **Nuovo** fare clic su **Sì** in **Abilita criteri**.

    ![Accesso condizionale](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Nella pagina **Nuovo** fare clic su **Crea**.



## <a name="next-steps"></a>Passaggi successivi

Per informazioni approfondite su come Microsoft ha implementato questa funzionalità, vedere [Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile) (Miglioramento dell'accesso remoto in Windows 10 con un profilo VPN automatico).    


