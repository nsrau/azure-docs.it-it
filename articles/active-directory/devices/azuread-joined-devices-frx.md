---
title: Aggiungere un nuovo dispositivo Windows 10 con Azure AD in fase di completamento dell'installazione | Microsoft Docs
description: Modo in cui gli utenti possono configurare aggiunta ad Azure AD durante l'esperienza predefinita.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 384157828e9c816b150e40bf3f09b74578c4a98e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482089"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Esercitazione: Aggiungere un nuovo dispositivo Windows 10 con Azure AD in fase di completamento dell'installazione

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere l'[introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md).

Con Windows 10, è possibile aggiungere un nuovo dispositivo ad Azure AD in fase di completamento dell'installazione.  
In questo modo, è possibile distribuire dispositivi già pronti ai dipendenti o agli studenti.

Se nel dispositivo è installato Windows 10 Professional o Windows 10 Enterprise, per impostazione predefinita viene avviato il processo di installazione dei dispositivi di proprietà dell'azienda.

Nell'*esperienza di Configurazione guidata* di Windows, l'aggiunta di un dominio Active Directory (AD) locale non è supportata. Se si prevede di aggiungere un computer a un dominio AD, durante l'installazione è necessario selezionare il collegamento **Configurare Windows con un account locale**. Sarà quindi possibile aggiungere il dominio dalle impostazioni del computer.
 
In questa esercitazione viene illustrato come aggiungere un dispositivo ad Azure AD in fase di completamento dell'installazione:
 > [!div class="checklist"]
> * Prerequisiti
> * Aggiunta di un dispositivo
> * Verifica

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere un dispositivo Windows 10, il servizio di registrazione dispositivo deve essere configurato per consentire la registrazione dei dispositivi. Oltre ad avere l'autorizzazione per l'aggiunta di dispositivi nel tenant di Azure AD, è necessario che i dispositivi registrati siano meno del numero massimo configurato. Per altre informazioni, vedere [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings).

Inoltre, se il tenant è federato, il provider di identità DEVE supportare l'endpoint con nome utente/password WS-Trust e WS-Fed. La versione può essere 1.3 o 2005. Questo supporto del protocollo è necessario aggiungere il dispositivo ad Azure AD sia accedere al dispositivo con una password.

## <a name="joining-a-device"></a>Aggiunta di un dispositivo

**Per aggiungere un dispositivo Windows 10 ad Azure AD in fase di completamento dell'installazione:**

1. Quando si accende il nuovo dispositivo e viene avviato il processo di installazione, viene visualizzato il messaggio **Preparazione** . Seguire le istruzioni per configurare il dispositivo.
1. Iniziare personalizzando il paese e la lingua. Quindi accettare le Condizioni di licenza software Microsoft.
 
    ![Personalizzare il paese](./media/azuread-joined-devices-frx/01.png)

1. Selezionare la rete che si desidera usare per la connessione a Internet.
1. Fare clic su **Questo dispositivo appartiene all'organizzazione**. 

    ![Schermata A chi appartiene questo PC?](./media/azuread-joined-devices-frx/02.png)

1. Immettere le credenziali fornite dall'organizzazione e quindi fare clic su **Accedi**.

    ![Schermata di accesso](./media/azuread-joined-devices-frx/03.png)

1. Il dispositivo individua un tenant corrispondente in Azure AD. Se si è in un dominio federato, si verrà reindirizzati al server del servizio token di sicurezza locale, ad esempio Active Directory Federation Services (AD FS).
1. Se si è in un dominio non federato, immettere le credenziali direttamente nella pagina ospitata da Azure AD. 
1. Viene richiesto di effettuare l'autenticazione a più fattori. 
1. Azure AD verifica se è necessaria la registrazione per la gestione di dispositivi mobili.
1. Windows registra il dispositivo nella directory dell'organizzazione in Azure AD e per la gestione di dispositivi mobili, se appropriato.
1. In caso di:
   - Un utente gestito, Windows visualizza il desktop tramite il processo di accesso automatico.
   - Un utente federato, viene visualizzata la schermata di accesso di Windows dove immettere le credenziali.

## <a name="verification"></a>Verifica

Per verificare se un dispositivo è stato aggiunto ad Azure AD, esaminare la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** nel dispositivo Windows. La finestra di dialogo dovrebbe indicare che si è connessi alla directory di Azure AD.

![Accedi all'azienda o all'istituto di istruzione](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere l'[introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md).
- Per altre informazioni sulla gestione dei dispositivi nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md).
