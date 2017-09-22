---
title: Configurare dispositivi aggiunti ad Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare dispositivi aggiunti ad Azure Active Directory.
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
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configurare dispositivi aggiunti ad Azure Active Directory

La gestione dei dispositivi in Azure Active Directory (Azure AD) aiuta a garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md).

Con i [dispositivi aggiunti ad Azure AD](device-management-introduction.md#azure-ad-joined-devices) è possibile trasferire i dispositivi Windows 10 che appartengono alla propria organizzazione sotto il controllo di Azure AD. Questi dispositivi non devono risultare già [aggiunti a una AD locale](device-management-introduction.md#hybrid-azure-ad-joined-devices).

Questo argomento include le istruzioni per registrare i dispositivi Windows 10 con Azure AD. 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario verificare di:

- Disporre delle autorizzazioni necessarie per aggiungere dispositivi ad Azure AD.

    ![Connesso](./media/device-management-azuread-joined-devices-setup/21.png)

- Non avere ancora superato il numero massimo di dispositivi per utente. 

    ![Connesso](./media/device-management-azuread-joined-devices-setup/22.png)


Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Informazioni utili


- Windows registra il dispositivo nella directory dell'organizzazione in Azure AD.

- Potrebbe essere richiesto di superare l'autenticazione a più fattori. Tale richiesta può essere impostata dall'amministratore IT.

- Azure AD controlla se un dispositivo richiede la registrazione per la gestione dei dispositivi mobili. Se necessario, registra il dispositivo.

- Windows reindirizza gli utenti gestiti al desktop tramite l'accesso automatico.

- Gli utenti federati vengono reindirizzati a una pagina di accesso di Windows in cui immettere le credenziali.


## <a name="joining-a-device"></a>Aggiunta di un dispositivo

Questa sezione illustra i passaggi necessari per aggiungere il dispositivo Windows 10 ad Azure AD. Se il dispositivo è stato aggiunto correttamente ad Azure AD, la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** include l'indicazione **Connesso a \<Azure AD\>**.

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

Per altre informazioni, vedere [Individuare i dispositivi](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere: 

- [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md)

- [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md)





