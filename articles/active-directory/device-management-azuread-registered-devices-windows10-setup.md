---
title: Configurare dispositivi registrati in Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi registrati in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 2560e51d61506389e84288bf983b0ebcb5776ff2
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurare dispositivi Windows 10 registrati in Azure Active Directory

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduction to device management in Azure Active Directory](device-management-introduction.md) (Introduzione alla gestione dei dispositivi in Azure Active Directory).

Se si vuole abilitare lo scenario **Bring Your Own Device (BYOD)**, è possibile configurare dispositivi registrati in Azure AD. In Azure AD è possibile configurare dispositivi registrati per Windows 10, iOS, Android e macOS. Questo argomento illustra i passaggi necessari per i dispositivi Windows 10. 


## <a name="before-you-begin"></a>Prima di iniziare

Per registrare un dispositivo Windows 10, il servizio di registrazione dispositivo deve essere configurato per consentire la registrazione dei dispositivi. Oltre ad avere l'autorizzazione per la registrazione di dispositivi nel tenant di Azure AD, è necessario che i dispositivi registrati siano meno del numero massimo configurato. Per altri dettagli, vedere [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Informazioni utili

Quando si registra un dispositivo, è necessario tenere presente quanto segue:

- Windows registra il dispositivo nella directory dell'organizzazione in Azure AD

- Potrebbe venire richiesta l'autenticazione a più fattori. Questa richiesta può essere configurata da un amministratore IT.

- Azure AD controlla se il dispositivo richiede la registrazione per la gestione di dispositivi mobili e, se necessario, la esegue.

- Se si è un utente gestito, Windows visualizza il desktop tramite l'accesso automatico.

- Se si è un utente federato, viene visualizzata una schermata di accesso di Windows che consente di immettere le proprie credenziali.


## <a name="registering-a-device"></a>Registrazione di un dispositivo

Questa sezione illustra i passaggi necessari per registrare il dispositivo Windows 10 in Azure AD. Se il dispositivo è stato registrato correttamente in Azure AD, la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** include l'indicazione **Account aziendale o dell'istituto di istruzione**.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Per registrare un dispositivo Windows 10:**

1. Nel menu **Start** fare clic su **Impostazioni**.

    ![Impostazioni](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Fare clic su **Account**.

    ![Account](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Fare clic su **Accedi all'azienda o all'istituto di istruzione**.

    ![Accedi all'azienda o all'istituto di istruzione](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Nella finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** fare clic su **Connetti**.

    ![Connettere](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Nella finestra di dialogo **Configura un account aziendale o dell'istituto di istruzione** immettere il nome dell'account (ad esempio, someone@example.com) e quindi fare clic su **Avanti**.

    ![Connettere](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Nella finestra di dialogo **Immettere la password** immettere la password e quindi fare clic su **Avanti**.

    ![Connettere](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Nella finestra di dialogo **È tutto pronto!** fare clic su **Fine**.

    ![Connettere](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verifica

Per verificare se un dispositivo è stato aggiunto ad Azure AD, esaminare la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** nel dispositivo.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

In alternativa, è possibile esaminare le impostazioni del dispositivo nel portale di Azure AD.

![Register ](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)

- Per altre informazioni sulla gestione di dispositivi nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md).




