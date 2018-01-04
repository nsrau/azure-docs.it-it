---
title: Aggiungere un nuovo dispositivo Windows 10 con Azure AD in fase di completamento dell'installazione | Microsoft Docs
description: Questo argomento spiega in che modo gli utenti possono configurare un'aggiunta ad Azure AD in fase di completamento dell'installazione.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: d02994a4fba64605c79edae3f68db36dc8935b28
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Aggiungere un nuovo dispositivo Windows 10 con Azure AD in fase di completamento dell'installazione

Tramite la gestione dei dispositivi in Azure Active Directory (Azure AD) è possibile garantire che gli utenti accedano alle risorse da dispositivi che soddisfano gli standard di sicurezza e conformità. Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md).

Con Windows 10, è possibile aggiungere un nuovo dispositivo ad Azure AD in fase di completamento dell'installazione.  
In questo modo, è possibile distribuire dispositivi già pronti ai dipendenti o agli studenti.

Se nel dispositivo è installato Windows 10 Professional o Windows 10 Enterprise, per impostazione predefinita viene avviato il processo di installazione dei dispositivi di proprietà dell'azienda.

Nell'*esperienza di Configurazione guidata* di Windows, l'aggiunta di un dominio Active Directory (AD) locale non è supportata. Se si prevede di aggiungere un computer a un dominio AD, durante l'installazione è necessario selezionare il collegamento **Configurare Windows con un account locale**. Sarà quindi possibile aggiungere il dominio dalle impostazioni del computer.
 


## <a name="before-you-begin"></a>Prima di iniziare

Per aggiungere un dispositivo Windows 10, il servizio di registrazione dispositivo deve essere configurato per consentire la registrazione dei dispositivi. Oltre ad avere l'autorizzazione per l'aggiunta di dispositivi nel tenant di Azure AD, è necessario che i dispositivi registrati siano meno del numero massimo configurato. Per altri dettagli, vedere [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings).

## <a name="joining-a-device"></a>Aggiunta di un dispositivo

**Per aggiungere un dispositivo Windows 10 ad Azure AD in fase di completamento dell'installazione:**


1. Quando si accende il nuovo dispositivo e viene avviato il processo di installazione, viene visualizzato il messaggio **Preparazione** . Seguire le istruzioni per configurare il dispositivo.

2. Iniziare personalizzando il paese e la lingua. Quindi accettare le Condizioni di licenza software Microsoft.
 
    ![Personalizzare il paese](./media/device-management-azuread-joined-devices-frx/01.png)

3. Selezionare la rete che si desidera usare per la connessione a Internet.

4. Fare clic su **Questo dispositivo appartiene all'organizzazione**. 

    ![Schermata A chi appartiene questo PC?](./media/device-management-azuread-joined-devices-frx/02.png)

5. Immettere le credenziali fornite dall'organizzazione e quindi fare clic su **Accedi**.

    ![Schermata di accesso](./media/device-management-azuread-joined-devices-frx/03.png)

6. Il dispositivo individua un tenant corrispondente in Azure AD. Se si è in un dominio federato, si verrà reindirizzati al server del servizio token di sicurezza locale, ad esempio Active Directory Federation Services (AD FS).

7. Se si è in un dominio non federato, immettere le credenziali direttamente nella pagina ospitata da Azure AD. 

8. Viene richiesto di effettuare l'autenticazione a più fattori. 
 
9. Azure AD verifica se è necessaria la registrazione per la gestione di dispositivi mobili.

10. Windows registra il dispositivo nella directory dell'organizzazione in Azure AD e per la gestione di dispositivi mobili, se appropriato.

11. In caso di:
    - Un utente gestito, Windows visualizza il desktop tramite il processo di accesso automatico.

    - Un utente federato, viene visualizzata la schermata di accesso di Windows dove immettere le credenziali.

## <a name="verification"></a>Verifica

Per verificare se un dispositivo è stato aggiunto ad Azure AD, esaminare la finestra di dialogo **Accedi all'azienda o all'istituto di istruzione** nel dispositivo Windows. La finestra di dialogo dovrebbe indicare che si è connessi alla directory di Azure AD.

![Accedi all'azienda o all'istituto di istruzione](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere [Introduzione alla gestione dei dispositivi in Azure Active Directory](device-management-introduction.md).

- Per altre informazioni sulla gestione di dispositivi nel portale di Azure AD, vedere [Gestione dei dispositivi tramite il portale di Azure](device-management-azure-portal.md).
