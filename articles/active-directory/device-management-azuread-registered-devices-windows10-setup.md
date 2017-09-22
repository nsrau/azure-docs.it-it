---
title: Configurare dispositivi Windows 10 registrati in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi Windows 10 registrati in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurare dispositivi Windows 10 registrati in Azure Active Directory

La gestione dei dispositivi in Azure Active Directory (Azure AD) aiuta a garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md).

L'uso di [dispositivi registrati in Azure AD](device-management-introduction.md#azure-ad-registered-devices) consente di applicare lo scenario **Bring Your Own Device (BYOD)**, che permette agli utenti di accedere alle risorse dell'organizzazione usando dispositivi personali.  

In Azure AD è possibile configurare i dispositivi registrati in AD Azure per:

- Windows 10
- iOS
- Android
- macOS.  

Questo argomento include le istruzioni per registrare i dispositivi Windows 10 con Azure AD. 


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario verificare di:

- Disporre delle autorizzazioni necessarie per registrare i dispositivi. 

    ![Registra](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- Non avere ancora superato il numero massimo di dispositivi per utente. 

    ![Registra](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Informazioni utili

Quando si registra un dispositivo, tenere presente quanto segue:

- Windows registra il dispositivo nella directory dell'organizzazione in Azure AD.

- Potrebbe essere richiesto di superare l'autenticazione a più fattori. Tale richiesta può essere impostata dall'amministratore IT.

- Azure AD controlla se un dispositivo richiede la registrazione per la gestione dei dispositivi mobili. Se necessario, registra il dispositivo.

- Windows reindirizza gli utenti gestiti al desktop tramite l'accesso automatico.

- Gli utenti federati vengono reindirizzati a una pagina di accesso di Windows in cui immettere le credenziali.


## <a name="register-a-device"></a>Registrare un dispositivo

Per registrare il dispositivo Windows 10 con Azure AD, seguire questa procedura. Se il dispositivo è stato registrato in Azure AD, nella pagina **Accedi all'azienda o all'istituto di istruzione** è indicata la voce **Account aziendale o dell'istituto di istruzione**.

![Registra](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Per registrare il dispositivo Windows 10:

1. Nel menu **Start** fare clic su **Impostazioni**.

    ![Selezionare Impostazioni](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Fare clic su **Account**.

    ![Selezionare Account](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Fare clic su **Accedi all'azienda o all'istituto di istruzione**.

    ![Selezionare Accedi all'azienda o all'istituto di istruzione](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Nella pagina **Accedi all'azienda o all'istituto di istruzione** fare clic su **Connetti**.

    ![Pagina Accedi all'azienda o all'istituto di istruzione](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Nella pagina **Configura un account aziendale o dell'istituto di istruzione** immettere il nome dell'account, ad esempio someone@example.com, quindi fare clic su **Avanti**.

    ![Pagina Configura un account aziendale o dell'istituto di istruzione](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Nella pagina **Immettere la password** immettere la password e quindi fare clic su **Avanti**.

    ![Immettere la password](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Nella finestra di dialogo **È tutto pronto!** fare clic su **Fine**.

    ![Pagina È tutto pronto](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verifica

Per verificare se un dispositivo è stato aggiunto ad Azure AD, controllare la pagina **Accedi all'azienda o all'istituto di istruzione** nel dispositivo.

![Stato dell'account aziendale o dell'istituto di istruzione](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

In alternativa, è possibile esaminare le impostazioni del dispositivo nel portale di Azure AD.

![Dispositivi registrati in Azure AD](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere: 

- [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)

- [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md)





