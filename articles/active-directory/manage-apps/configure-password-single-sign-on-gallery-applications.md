---
title: Come configurare un accesso Single Sign-On basato su password per un'applicazione della raccolta di Azure AD | Microsoft Docs
description: Come configurare un'applicazione per la Single Sign-On sicura basata su password quando è già elencata nella raccolta di applicazioni Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146878"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Configurare Single Sign-On password per un'applicazione Azure AD Gallery

Quando si aggiunge un'applicazione dalla [raccolta di applicazioni Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), è possibile scegliere come si vuole che gli utenti possano accedere a tale applicazione. È possibile configurare questa opzione in qualsiasi momento selezionando **Single Sign-on** in un'applicazione aziendale nel [portale di Azure](https://portal.azure.com/).

Una delle opzioni di Single Sign-On (SSO) disponibili è [Single Sign-on basata su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Questo è un ottimo modo per iniziare a integrare rapidamente le applicazioni in Azure AD. Consente le operazioni seguenti:

-   Archivia e riproduce in modo sicuro nomi utente e password per l'applicazione integrata con Azure AD

-   Fornisce supporto per le applicazioni che richiedono più campi di accesso oltre ai campi nome utente e password

-   Consente di personalizzare le etichette dei campi nome utente e password visualizzati dagli utenti nel pannello di [accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando immettono le credenziali

-   Consente agli utenti di fornire i propri nomi utente e password per tutti gli account dell'applicazione esistenti immessi manualmente nel [Pannello di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Consente a un membro del gruppo aziendale di utilizzare la funzionalità di [accesso alle applicazioni self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) per specificare i nomi utente e le password assegnati a un utente

-   Consente a un amministratore di specificare i nomi utente e le password assegnati a un utente usando la funzionalità Aggiorna credenziali quando [assegnano un utente a un'applicazione](#assign-a-user-to-an-application-directly)

-   Consente a un amministratore di usare la funzionalità di aggiornamento delle credenziali per specificare il nome utente o la password condivisa per un gruppo di persone quando [assegnano un gruppo a un'applicazione](#assign-an-application-to-a-group-directly)

La sezione seguente descrive come abilitare la [Single Sign-on basata su password](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) per un'applicazione già presente nella [raccolta di applicazioni Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>Panoramica dei passaggi necessari
Per configurare un'applicazione dalla raccolta di Azure AD, è necessario:

-   [Aggiungere un'applicazione dalla raccolta di Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configurare l'applicazione per un accesso Single Sign-On basato su password](#configure-the-application-for-password-single-sign-on)

-   Assegnare l'applicazione a un utente o a un gruppo:

    -   [Assegnare un utente direttamente a un'applicazione](#assign-a-user-to-an-application-directly)

    -   [Assegnare un'applicazione direttamente a un gruppo ](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Aggiungere un'applicazione dalla raccolta di Azure AD

Per aggiungere un'applicazione dalla raccolta di Azure AD, seguire questa procedura:

1.  Aprire il [portale di Azure](https://portal.azure.com)e accedere come **amministratore globale** o coamministratore.

2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu a sinistra.

3.  Immettere **Azure Active Directory** nella casella di ricerca e quindi selezionare l'elemento **Azure Active Directory** .

4.  Selezionare **applicazioni aziendali** dal menu Azure ad a sinistra.

5.  Selezionare il pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **applicazioni aziendali** .

6.  Nella casella **immettere un nome** nella sezione **Aggiungi dalla raccolta** , immettere il nome dell'applicazione.

7.  Selezionare l'applicazione che si desidera configurare per Single Sign-On.

8.  Prima di aggiungere l'applicazione, è possibile modificarne il nome nella casella **nome** .

9.  Selezionare **Aggiungi** per aggiungere l'applicazione.

Dopo un breve periodo di tempo, sarà possibile visualizzare il riquadro di configurazione dell'applicazione.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurare l'applicazione per un accesso Single Sign-On basato su password

Per configurare l'accesso Single Sign-On per un'applicazione, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale** o coamministratore.

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu a sinistra.

3. Immettere **Azure Active Directory** nella casella di ricerca e quindi selezionare l'elemento **Azure Active Directory** .

4. Selezionare **applicazioni aziendali** dal menu Azure Active Directory a sinistra.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** all'inizio dell'elenco di tutte le **applicazioni**e impostare l'opzione **Mostra** su **tutte le applicazioni**.

6. Selezionare l'applicazione che si desidera configurare per Single Sign-On.

7. Al termine del caricamento dell'applicazione, selezionare **Single Sign-on** dal menu dell'applicazione a sinistra.

8. Selezionare la modalità **accesso basato su password** .

9. [Assegnare gli utenti all'applicazione](#assign-a-user-to-an-application-directly).

10. È anche possibile fornire le credenziali per conto degli utenti selezionando la riga di un utente, selezionando **Aggiorna credenziali**e immettendo il nome utente e la password. In caso contrario, agli utenti viene richiesto di immettere le credenziali all'avvio dell'applicazione.

## <a name="assign-a-user-to-an-application-directly"></a>Assegnare un utente direttamente a un'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale**.

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu a sinistra.

3. Immettere **Azure Active Directory** nella casella di ricerca e quindi selezionare l'elemento **Azure Active Directory** .

4. Selezionare **applicazioni aziendali** dal menu Azure Active Directory a sinistra.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** all'inizio dell'elenco di tutte le **applicazioni**e impostare l'opzione **Mostra** su **tutte le applicazioni**.

6. Selezionare l'applicazione a cui si desidera assegnare un utente.

7. Al termine del caricamento dell'applicazione, selezionare **utenti e gruppi** dal menu dell'applicazione a sinistra.

8. Selezionare il pulsante **Aggiungi** nella parte superiore dell'elenco **utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione** .

9. Selezionare **utenti e gruppi** nel riquadro **Aggiungi assegnazione** .

10. Immettere il nome completo o l'indirizzo di posta elettronica dell'utente nella casella di ricerca **Cerca per nome o indirizzo di posta elettronica** .

11. Passare il puntatore del mouse sull'utente nell'elenco, quindi selezionare la casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungerli all'elenco **selezionato** .

12. Facoltativo: Se si desidera aggiungere più utenti, immettere un altro nome completo o indirizzo di posta elettronica nella casella **Cerca per nome o indirizzo di posta elettronica** e selezionare la casella di controllo relativa all'utente per aggiungerli all'elenco **selezionato** .

13. Al termine della selezione degli utenti, usare il pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. Facoltativo: Usare il comando **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per selezionare un ruolo da assegnare agli utenti selezionati.

15. Selezionare **assegna** per assegnare l'applicazione agli utenti selezionati.

## <a name="assign-an-application-to-a-group-directly"></a>Assegnare un'applicazione direttamente a un gruppo

Per assegnare uno o più gruppi direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come **amministratore globale**.

2. Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu a sinistra.

3. Immettere **Azure Active Directory** nella casella di ricerca e quindi selezionare l'elemento **Azure Active Directory** .

4. Selezionare **applicazioni aziendali** dal menu Azure ad a sinistra.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   Se non viene visualizzata l'applicazione desiderata, usare il controllo **filtro** nella parte superiore dell' **elenco di tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni**.

6. Selezionare l'applicazione a cui si desidera assegnare un utente.

7. Al termine del caricamento dell'applicazione, selezionare **utenti e gruppi** dal menu dell'applicazione a sinistra.

8. Selezionare il pulsante **Aggiungi** nella parte superiore dell'elenco **utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione** .

9. Selezionare **utenti e gruppi** nel riquadro **Aggiungi assegnazione** .

10. Immettere il nome completo del gruppo che si vuole assegnare nella casella di ricerca **Cerca per nome o indirizzo di posta elettronica** .

11. Passare il puntatore del mouse sul **gruppo** nell'elenco, quindi selezionare la casella di controllo accanto alla foto o al logo del profilo del gruppo per aggiungere il gruppo all'elenco **selezionato** .

12. Facoltativo: Se si desidera aggiungere più di un gruppo, immettere un altro nome di gruppo completo nella casella **di ricerca Cerca per nome o indirizzo di posta elettronica** , quindi selezionare la casella di controllo corrispondente per aggiungere questo gruppo all'elenco **selezionato** .

13. Al termine della selezione dei gruppi, usare il pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. Facoltativo: Usare il comando **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per selezionare un ruolo da assegnare ai gruppi selezionati.

15. Selezionare **assegna** per assegnare l'applicazione ai gruppi selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati dovrebbero essere in grado di avviare queste applicazioni dal pannello di accesso.

## <a name="next-steps"></a>Passaggi successivi
[Fornire Single Sign-on alle app tramite il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md).
