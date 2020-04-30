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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731699"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Come configurare un'assegnazione di applicazioni self-service

Prima che gli utenti possano individuare autonomamente le applicazioni dal pannello di accesso app personali, è necessario abilitare **l'accesso alle applicazioni self-service** alle applicazioni per le quali si desidera consentire agli utenti di individuare autonomamente e richiedere l'accesso. Questa funzionalità è disponibile per le applicazioni che sono state aggiunte dalla [raccolta di Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), [Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) o sono state aggiunte tramite il [consenso dell'utente o dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Questa funzionalità è un modo efficace per consentire di risparmiare tempo e denaro al gruppo IT ed è altamente consigliata come parte di una distribuzione moderna di applicazioni con Azure Active Directory.

Questa funzionalità permette di:

-   Consentire agli utenti di individuare autonomamente le applicazioni dal [Pannello di accesso app personali](https://myapps.microsoft.com/) senza preoccuparsi del gruppo it.

-   Aggiungere gli utenti a un gruppo pre-configurato in modo che sia possibile verificare chi ha richiesto l'accesso, rimuovere l'accesso e gestire i ruoli assegnati.

-   Facoltativamente, consentire al revisore aziendale di approvare l'accesso alle applicazioni in modo da rimuovere questa azione dalle operazioni del gruppo IT.

-   Facoltativamente, configurare un massimo di 10 persone che possono approvare l'accesso a questa applicazione.

-   Facoltativamente consentire al revisore aziendale di impostare le password usate dagli utenti per accedere all'applicazione, direttamente dal [Pannello di accesso dell'applicazione](https://myapps.microsoft.com/) del revisore aziendale.

-   Facoltativamente, assegnare automaticamente gli utenti dell'accesso self-service direttamente a un ruolo applicazione.

> [!NOTE]
> È necessaria una licenza Azure Active Directory Premium (P1 o P2) per consentire agli utenti di richiedere l'aggiunta a un'app self-service e ai proprietari di approvare o rifiutare le richieste. Senza una licenza di Azure Active Directory Premium, gli utenti non possono aggiungere app self-service.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un ottimo modo per consentire agli utenti di individuare autonomamente le applicazioni e, facoltativamente, consentire al gruppo aziendale di approvare l'accesso a tali applicazioni. Per le applicazioni con accesso Single Sign-on basato su password, è anche possibile consentire al gruppo aziendale di gestire le credenziali assegnate a tali utenti dai propri pannelli di accesso alle app personali.

Per abilitare l'accesso self-service per un'applicazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Selezionare **Azure Active Directory**. Nel menu di spostamento a sinistra selezionare **applicazioni aziendali**.

3. Selezionare l'applicazione dall'elenco. Se l'applicazione non viene visualizzata, iniziare a digitarne il nome nella casella di ricerca. In alternativa, usare i controlli filtro per selezionare il tipo di applicazione, lo stato o la visibilità, quindi selezionare **applica**.

4. Nel menu di spostamento a sinistra selezionare **self-service**.

5. Per abilitare l'accesso self-service per questa applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.

6. Accanto al **gruppo a cui devono essere aggiunti gli utenti assegnati?**, fare clic su **Seleziona gruppo**. Scegliere un gruppo, quindi fare clic su **Seleziona**. Quando la richiesta di un utente viene approvata, verrà aggiunta a questo gruppo. Quando si visualizza l'appartenenza a questo gruppo, sarà possibile vedere a chi è stato concesso l'accesso all'applicazione tramite l'accesso self-service.
  
    > [!NOTE]
    > Questa impostazione non supporta i gruppi sincronizzati dall'ambiente locale.

7. **Facoltativo:** Per richiedere l'approvazione aziendale prima che agli utenti venga consentito l'accesso, impostare **Richiedi approvazione prima di concedere l'accesso a questa applicazione?** selezionare **Sì**.

8. **Facoltativo: per le applicazioni che usano solo l'accesso Single Sign-on basato su password,** per consentire ai responsabili approvazione aziendali di specificare le password inviate a questa applicazione per gli utenti approvati, impostare l'opzione **Consenti ai responsabili approvazione di impostare le password dell'utente per questa applicazione?** Selezionare **Sì**.

9. **Facoltativo:** Per specificare i responsabili approvazione aziendali a cui è consentito approvare l'accesso a questa applicazione, accanto a **chi è autorizzato ad approvare l'accesso a questa applicazione**, fare clic su **Seleziona responsabili approvazione**, quindi selezionare fino a 10 responsabili approvazione aziendali singoli. Quindi fare clic su **Seleziona**.

    >[!NOTE]
    >I gruppi non sono supportati. È possibile selezionare fino a 10 responsabili approvazione aziendali singoli. Se si specificano più responsabili approvazione, i singoli responsabili approvazione possono approvare una richiesta di accesso.

10. **Facoltativo:** **per le applicazioni che espongono ruoli**, per assegnare utenti approvati in modo self-service a un ruolo, accanto a **a quale ruolo devono essere assegnati gli utenti in questa applicazione?**, fare clic su **Seleziona ruolo**, quindi scegliere il ruolo a cui questi utenti devono essere assegnati. Quindi fare clic su **Seleziona**.

11. Per terminare, fare clic sul pulsante **Salva** nella parte superiore del riquadro.

Dopo aver completato la configurazione dell'applicazione self-service, gli utenti possono passare al [Pannello di accesso app personali](https://myapps.microsoft.com/) e fare clic sul pulsante **Aggiungi app self-service** per trovare le app abilitate con l'accesso self-service. Gli approvatori aziendali visualizzano anche una notifica nel [Pannello di accesso app personali](https://myapps.microsoft.com/). È possibile abilitare un messaggio di posta elettronica per informare i responsabili dell'approvazione quando un utente richiede l'accesso a un'applicazione per cui è necessaria l'approvazione.

## <a name="next-steps"></a>Passaggi successivi
[Configurazione di Azure Active Directory per la gestione self-service dei gruppi](../users-groups-roles/groups-self-service-management.md)
