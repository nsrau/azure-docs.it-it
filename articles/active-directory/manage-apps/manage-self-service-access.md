---
title: Come configurare l'assegnazione di applicazioni self-service| Microsoft Docs
description: Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e09fd63ee6121ac9bf7f3c2be00f0ac22f752f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731699"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Come configurare un'assegnazione di applicazioni self-service

Prima che gli utenti possano individuare autonomamente le applicazioni dal pannello di accesso App personali, è necessario abilitare **l'accesso alle applicazioni self-service** a tutte le applicazioni a cui si desidera consentire agli utenti di individuare autonomamente e richiedere l'accesso. Questa funzionalità è disponibile per le applicazioni aggiunte dalla [raccolta Azure AD,](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)dal [proxy di applicazione di Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) o tramite il consenso dell'utente o dell'amministratore. [user or admin consent](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) 

Questa funzionalità è un modo efficace per consentire di risparmiare tempo e denaro al gruppo IT ed è altamente consigliata come parte di una distribuzione moderna di applicazioni con Azure Active Directory.

Questa funzionalità permette di:

-   Consentire agli utenti di individuare autonomamente le applicazioni dal pannello di [accesso App](https://myapps.microsoft.com/) personali senza disturbare il gruppo IT.

-   Aggiungere gli utenti a un gruppo pre-configurato in modo che sia possibile verificare chi ha richiesto l'accesso, rimuovere l'accesso e gestire i ruoli assegnati.

-   Facoltativamente, consentire al revisore aziendale di approvare l'accesso alle applicazioni in modo da rimuovere questa azione dalle operazioni del gruppo IT.

-   Facoltativamente, configurare un massimo di 10 persone che possono approvare l'accesso a questa applicazione.

-   Facoltativamente consentire al revisore aziendale di impostare le password usate dagli utenti per accedere all'applicazione, direttamente dal [Pannello di accesso dell'applicazione](https://myapps.microsoft.com/) del revisore aziendale.

-   Facoltativamente, assegnare automaticamente gli utenti dell'accesso self-service direttamente a un ruolo applicazione.

> [!NOTE]
> È necessaria una licenza di Azure Active Directory Premium (P1 o P2) per consentire agli utenti di richiedere l'aggiunta a un'app self-service e per consentire ai proprietari di approvare o rifiutare le richieste. Senza una licenza di Azure Active Directory Premium, gli utenti non possono aggiungere app self-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare autonomamente le applicazioni e, facoltativamente, consentire al gruppo di business di approvare l'accesso a tali applicazioni. Per le applicazioni con accesso singolo password, è anche possibile consentire al gruppo di business di gestire le credenziali assegnate a tali utenti dai propri pannelli di accesso App personali.

Per abilitare l'accesso self-service per un'applicazione, seguire questa procedura:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore globale.

2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **Applicazioni aziendali**.

3. Selezionare l'applicazione dall'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, utilizzare i controlli filtro per selezionare il tipo di applicazione, lo stato o la visibilità, quindi selezionare **Applica**.

4. Nel menu di spostamento a sinistra selezionare **Self-service**.

5. Per abilitare l'accesso self-service per questa applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.

6. Accanto **a Quale gruppo devono essere aggiunti gli utenti?**, fare clic su **Seleziona gruppo**. Scegliere un gruppo e quindi fare clic su **Seleziona**. Quando la richiesta di un utente viene approvata, verrà aggiunto a questo gruppo. Quando si visualizza l'appartenenza a questo gruppo, sarà possibile vedere a chi è stato concesso l'accesso all'applicazione tramite l'accesso self-service.
  
    > [!NOTE]
    > Questa impostazione non supporta i gruppi sincronizzati dall'ambiente locale.

7. **Facoltativo:** Per richiedere l'approvazione aziendale prima che agli utenti sia consentito l'accesso, impostare l'opzione Richiedi approvazione prima di **concedere l'accesso a questa applicazione?** su **Sì**.

8. **Facoltativo: per** le applicazioni che utilizzano solo l'accesso Single Sign-On con password, per consentire agli approvatori aziendali di specificare **Yes**le password inviate all'applicazione per gli utenti approvati, impostare Consenti agli approvatori di **impostare le password utente per l'applicazione?**

9. **Facoltativo:** Per specificare gli approvatori aziendali autorizzati ad approvare l'accesso all'applicazione, accanto a **Chi può approvare l'accesso all'applicazione?**, fare clic su **Seleziona responsabili approvazione**e quindi selezionare fino a 10 singoli approvatori aziendali. Quindi fare clic su **Seleziona**.

    >[!NOTE]
    >I gruppi non sono supportati. È possibile selezionare fino a 10 singoli approvatori aziendali. Se si specificano più approvatori, qualsiasi singolo approvatore può approvare una richiesta di accesso.

10. **Facoltativo:** per le **applicazioni che espongono ruoli**, per assegnare utenti approvati self-service a un ruolo, accanto al ruolo A a quale ruolo devono essere **assegnati gli utenti nell'applicazione?**, fare clic su **Seleziona ruolo**e quindi scegliere il ruolo a cui devono essere assegnati questi utenti. Quindi fare clic su **Seleziona**.

11. Per terminare, fare clic sul pulsante **Salva** nella parte superiore del riquadro.

Una volta completata la configurazione dell'applicazione in modalità self-service, gli utenti possono passare al pannello di [accesso App](https://myapps.microsoft.com/) personali e fare clic sul pulsante Aggiungi **app self-service** per trovare le app abilitate con accesso self-service. Gli approvatori aziendali visualizzano anche una notifica nel pannello di [accesso App personali.](https://myapps.microsoft.com/) È possibile abilitare un messaggio di posta elettronica per informare i responsabili dell'approvazione quando un utente richiede l'accesso a un'applicazione per cui è necessaria l'approvazione.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione di Azure Active Directory per la gestione self-service dei gruppi](../users-groups-roles/groups-self-service-management.md)
