---
title: Configurare dispositivi aggiunti ad Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti ad Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 83eea565cc775a0569015c04d79d627d6ba120e6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411710"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configurare dispositivi aggiunti ad Azure Active Directory

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](../device-management-introduction.md).

Per controllare i dispositivi Windows 10 aziendali con Azure AD, è necessario configurarne l'aggiunta ad Azure AD. Questo argomento illustra i passaggi necessari. 


## <a name="prerequisites"></a>Prerequisiti

Per aggiungere un dispositivo Windows 10, il servizio di registrazione dispositivo deve essere configurato per consentire la registrazione dei dispositivi. Oltre ad avere l'autorizzazione per l'aggiunta di dispositivi nel tenant di Azure AD, è necessario che i dispositivi registrati siano meno del numero massimo configurato. Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](../devices/device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Informazioni utili


- Windows aggiunge il dispositivo nella directory dell'organizzazione in Azure AD.

- Potrebbe venire richiesta l'autenticazione a più fattori. Questa richiesta può essere configurata da un amministratore IT.

- Azure AD controlla se il dispositivo richiede la registrazione per la gestione di dispositivi mobili e, se necessario, la esegue.

- Se si è un utente gestito, Windows visualizza il desktop tramite l'accesso automatico.

- Nel caso di un utente federato, è necessario accedere usando le credenziali.

- Se l'utente è federato, il provider di identità DEVE supportare l'endpoint con nome utente/password WS-Trust e WS-Fed. La versione può essere 1.3 o 2005. Il supporto di questo protocollo è necessario per aggiungere il dispositivo ad Azure AD e accedere al dispositivo con una password. 




## <a name="joining-a-device"></a>Aggiunta di un dispositivo

Questa sezione illustra i passaggi necessari per aggiungere il dispositivo Windows 10 ad Azure AD. Se il dispositivo è stato aggiunto correttamente viene indicato come **Connesso ad \<Azure AD\>**.

![Connesso](./media/device-management-azuread-joined-devices-setup/13.png)


**Per aggiungere un dispositivo Windows 10:**

1. Nel menu **Start** fare clic su **Impostazioni**.

    ![Impostazioni](./media/device-management-azuread-joined-devices-setup/01.png)

2. Fare clic su **Account**.

    ![Account](./media/device-management-azuread-joined-devices-setup/02.png)


3. Fare clic su **Accedi all'azienda o all'istituto di istruzione**.

    ![Accedi all'azienda o all'istituto di istruzione](./media/device-management-azuread-joined-devices-setup/03.png)

4. Nella finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** fare clic su **Connetti**.

    ![Connettere](./media/device-management-azuread-joined-devices-setup/04.png)


5. Nella finestra di dialogo **Configura un account aziendale o dell'istituto di istruzione** fare clic su **Aggiungi il dispositivo ad Azure Active Directory**.

    ![Connettere](./media/device-management-azuread-joined-devices-setup/08.png)


6. Nella finestra di dialogo **Effettua l'accesso** immettere il nome dell'account (ad esempio, someone@example.com) e quindi fare clic su **Avanti**.

    ![Effettua l'accesso](./media/device-management-azuread-joined-devices-setup/10.png)


6. Nella finestra di dialogo **Immettere la password** immettere la password e quindi fare clic su **Accedi**.

    ![Immettere la password](./media/device-management-azuread-joined-devices-setup/05.png)


7. Nella finestra di dialogo **Verifica che questa sia la tua organizzazione** fare clic su **Aggiungi**.

    ![Verifica che questa sia la tua organizzazione](./media/device-management-azuread-joined-devices-setup/11.png)


8. Nella finestra di dialogo **È tutto pronto!** fare clic su **Fine**.

    ![È tutto pronto!](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Verifica

Per verificare se un dispositivo è stato aggiunto ad Azure AD, esaminare la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** nel dispositivo.

![Connesso](./media/device-management-azuread-joined-devices-setup/13.png)

In alternativa, è possibile eseguire il comando seguente: `dsregcmd /status`  
Se il dispositivo è stato aggiunto correttamente, **AzureAdJoined** è **Yes**.

![Connesso](./media/device-management-azuread-joined-devices-setup/14.png)

È anche possibile esaminare le impostazioni del dispositivo nel portale di Azure AD.

![Connesso](./media/device-management-azuread-joined-devices-setup/15.png)

Per altre informazioni, vedere [Individuare i dispositivi](../devices/device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere: 

- [Introduzione alla gestione dei dispositivi in Azure Active Directory](../devices/overview.md)
- [Gestione dei dispositivi tramite il portale di Azure](../devices/device-management-azure-portal.md)


