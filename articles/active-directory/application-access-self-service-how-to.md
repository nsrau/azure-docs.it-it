---
title: Come configurare l'assegnazione di applicazioni self-service| Microsoft Docs
description: Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: af0ec17d319f0a2911e1b50a5ed45dd59dafa752
ms.contentlocale: it-it
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-configure-self-service-application-assignment"></a>Come configurare un'assegnazione di applicazioni self-service

Prima che gli utenti possano da soli individuare le applicazioni dal pannello di accesso, è necessario abilitare l'**accesso alle applicazioni self-service** per tutte le applicazioni per cui si vuole consentire l'individuazione e l'accesso da parte degli utenti.

Questa funzionalità è un modo efficace per consentire di risparmiare tempo e denaro al gruppo IT ed è altamente consigliata come parte di una distribuzione moderna di applicazioni con Azure Active Directory.

Questa funzionalità permette di:

-   Consentire agli utenti individuare da soli le applicazioni dal [pannello di accesso dell'applicazione](https://myapps.microsoft.com/) senza dover contattare il gruppo IT.

-   Aggiungere gli utenti a un gruppo pre-configurato in modo che sia possibile verificare chi ha richiesto l'accesso, rimuovere l'accesso e gestire i ruoli assegnati.

-   Facoltativamente, consentire al revisore aziendale di approvare l'accesso alle applicazioni in modo da rimuovere questa azione dalle operazioni del gruppo IT.

-   Facoltativamente, configurare fino a 10 persone che possono approvare l'accesso a questa applicazione.

-   Facoltativamente, consentire al revisore di business di impostare le password usate dagli utenti per accedere all'applicazione, direttamente dal [pannello di accesso dell'applicazione](https://myapps.microsoft.com/) del revisore aziendale.

-   Facoltativamente, assegnare automaticamente gli utenti dell'accesso self-service direttamente a un ruolo applicazione.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Abilitare l'accesso alle applicazioni self-service per consentire agli utenti di trovare le proprie applicazioni

L'accesso alle applicazioni self-service è un modo efficace per consentire agli utenti di individuare da soli le applicazioni, e, facoltativamente, al gruppo aziendale di approvare l'accesso a tali applicazioni. È possibile consentire al gruppo aziendale di gestire le credenziali assegnate agli utenti per il diritto di accesso alle applicazioni Single Sign-On basato su password dal pannello di accesso.

Per abilitare l'accesso self-service a un'applicazione, seguire i passaggi seguenti:

1.  Aprire il [**Portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.

2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Altri servizi** nella parte inferiore del menu di navigazione principale a sinistra.

3.  Digitare **"Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4.  Fare clic su **Applicazioni aziendali** nel menu di navigazione a sinistra di Azure Active Directory.

5.  Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

  * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'**elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6.  Selezionare l'applicazione di cui si vuole abilitare l'accesso self-service dall'elenco.

7.  Dopo il caricamento dell'applicazione, fare clic su **Self-service** nel menu di navigazione a sinistra dell'applicazione.

8.  Per abilitare l'accesso alle applicazioni self-service per tale applicazione, impostare l'opzione **Consentire agli utenti di richiedere l'accesso a questa applicazione?** su **Sì**.

9.  Successivamente, per selezionare il gruppo al quale devono essere aggiunti gli utenti che richiedono l'accesso a questa applicazione, fare clic sul selettore accanto all'etichetta **Gruppo a cui devono essere aggiunti gli utenti assegnati** e selezionare un gruppo.

10. **Facoltativo:** se si vuole richiedere un'approvazione aziendale prima che venga consentito l'accesso agli utenti, impostare l'opzione **Richiedere l'approvazione prima di concedere l'accesso a questa applicazione?** su **Sì**.

11. **Facoltativo: per applicazioni che usano solo un punto di accesso singolo basato su password,** se si vuole consentire ai revisori aziendali di specificare le password inviate a questa applicazione per gli utenti approvati, impostare l'opzione **Consentire ai responsabili approvazione di impostare le password utente per questa applicazione?** su **Sì**.

12. **Facoltativo:** per specificare i revisori aziendali che possono approvare l'accesso a questa applicazione, fare clic sul selettore accanto all'etichetta **Utenti autorizzati ad approvare l'accesso a questa applicazione** per selezionare un massimo di 10 revisori aziendali individuali.

   >[!NOTE]
   >I gruppi non sono supportati.
   >
   >

13. **Facoltativo:** **per le applicazioni che espongono ruoli**, se si vuole assegnare un ruolo agli utenti approvati self-service, fare clic sul selettore accanto all'opzione **A quale ruolo è necessario assegnare gli utenti in questa applicazione?** per selezionare il ruolo a cui devono essere assegnati questi utenti.

14. Per terminare, fare clic sul pulsante **Salva** nella parte superiore del pannello.

Dopo aver completato la configurazione dell'applicazione self-service, gli utenti possono accedere al [pannello di accesso dell'applicazione](https://myapps.microsoft.com/) e fare clic sul pulsante **+Aggiungi** per trovare le app per cui è stato abilitato l'accesso self-service. I revisori aziendali ricevono una notifica nel [pannello di accesso dell'applicazione](https://myapps.microsoft.com/). È possibile abilitare un messaggio di posta elettronica che informa i revisori quando un utente ha richiesto l'accesso a un'applicazione che richiede l'approvazione. 

Tali approvazioni supportano solo flussi di lavoro di approvazione individuali, vale a dire che se si specificano più revisori, qualsiasi revisore potrebbe approvare l'accesso all'applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Impostazione di Azure Active Directory per la gestione self-service dei gruppi](active-directory-accessmanagement-self-service-group-management.md)

