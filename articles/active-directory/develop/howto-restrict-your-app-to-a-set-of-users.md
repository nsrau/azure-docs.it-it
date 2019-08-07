---
title: Come limitare un'app registrata in Azure Active Directory a un set di utenti
description: Informazioni su come limitare l'accesso alle app registrate in Azure AD a un set di utenti.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51c49633e68fdc5f9afd4bf0205adaa625940ff
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812965"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Procedura: Limitare l'app a un set di utenti

Per impostazione predefinita, le applicazioni registrate in un tenant di Azure Active Directory (Azure AD) sono disponibili per tutti gli utenti del tenant che eseguono correttamente l'autenticazione.

Analogamente, nel caso di un app [multi-tenant](howto-convert-app-to-be-multi-tenant.md), tutti gli utenti del tenant di Azure AD in cui è stato eseguito il provisioning dell'app potranno accedere all'applicazione dopo aver eseguito l'autenticazione nel rispettivo tenant.

Gli sviluppatori e gli amministratori di tenant devono spesso soddisfare l'esigenza di limitare un'app a un determinato set di utenti. Per soddisfare questo requisito, gli sviluppatori possono usare comuni criteri di autorizzazione come il controllo degli accessi in base al ruolo, ma questo approccio richiede una considerevole quantità di lavoro da parte dello sviluppatore.

Azure AD consente agli amministratori e agli sviluppatori di tenant di limitare un'app a un set specifico di utenti o gruppi di sicurezza all'interno del tenant.

## <a name="supported-app-configurations"></a>Configurazioni di app supportate

La possibilità di limitare un'app a un set specifico di utenti o gruppi di sicurezza in un tenant può essere adottata con i tipi di applicazione seguenti:

- Applicazioni configurate per l'accesso Single Sign-On federato con autenticazione basata su SAML
- Applicazioni Application Proxy che usano la preautenticazione di Azure Active Directory
- Applicazioni create direttamente sulla piattaforma applicativa Azure AD che usano l'autenticazione OAuth 2.0/OpenID Connect dopo che un utente o un amministratore ha fornito il consenso per tale applicazione.

     > [!NOTE]
     > Questa funzionalità è disponibile solo per app Web/API Web e applicazioni aziendali. Le app registrate come [native](quickstart-v1-integrate-apps-with-azure-ad.md) non possono essere limitate a un set di utenti o gruppi di sicurezza all'interno del tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>Aggiornare l'app per abilitare l'assegnazione degli utenti

Esistono due modi per creare un'applicazione con l'assegnazione utente abilitata. Uno richiede il ruolo di **amministratore globale** , il secondo no.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Applicazioni aziendali (richiede il ruolo amministratore globale)

1. Passare alla [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale**.
1. Nella barra superiore selezionare l'account connesso. 
1. Nell'elenco **Directory** selezionare il tenant di Azure AD in cui verrà registrata l'app.
1. Nel menu di spostamento a sinistra selezionare **Azure Active Directory**. Se Azure Active Directory non è disponibile nel riquadro di spostamento, attenersi alla seguente procedura:

    1. Selezionare **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
    1. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare l'elemento **Azure Active Directory** dal risultato.

1. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali** all'interno del menu di spostamento a sinistra di **Azure Active Directory**.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

     Se l'applicazione desiderata non viene visualizzata, usare i filtri disponibili nella parte superiore dell'elenco **Tutte le applicazioni** per limitare l'elenco o scorrerlo verso il basso fino a trovare l'applicazione.

1. Selezionare dall'elenco l'applicazione che si vuole assegnare a un utente o un gruppo di sicurezza.
1. Nella pagina **Panoramica** dell'applicazione selezionare **Proprietà** nel menu di navigazione a sinistra dell'applicazione.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti devono prima essere assegnati a questa applicazione prima di potervi accedere.
1. Selezionare **Salva** per salvare questa modifica di configurazione.

### <a name="app-registration"></a>Registrazione delle app

1. Passare alla [**portale di Azure**](https://portal.azure.com/).
1. Nella barra superiore selezionare l'account connesso. 
1. Nell'elenco **Directory** selezionare il tenant di Azure AD in cui verrà registrata l'app.
1. Nel menu di spostamento a sinistra selezionare **Azure Active Directory**.
1. Nel riquadro **Azure Active Directory** selezionare registrazioni per l' **app** dal menu di spostamento a sinistra **Azure Active Directory** .
1. Creare o selezionare l'app che si vuole gestire. È necessario essere **proprietario** di questa registrazione dell'app.
1. Nella pagina **Panoramica** dell'applicazione, seguire il collegamento **applicazione gestita in directory locale** sotto gli elementi di base nella parte superiore della pagina. Questa operazione consente di passare all' _applicazione aziendale gestita_ della registrazione dell'app.
1. Nel pannello di navigazione a sinistra selezionare **Proprietà**.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti devono prima essere assegnati a questa applicazione prima di potervi accedere.
1. Selezionare **Salva** per salvare questa modifica di configurazione.

## <a name="assign-users-and-groups-to-the-app"></a>Assegnare utenti e gruppi all'app

Dopo aver configurato l'app per abilitare l'assegnazione degli utenti, è possibile ora assegnare all'app gli utenti e i gruppi desiderati.

1. Selezionare il riquadro **Utenti e gruppi** nel menu di spostamento a sinistra dell'applicazione.
1. Nella parte superiore dell'elenco **Utenti e gruppi** selezionare il pulsante **Aggiungi utente** per aprire il riquadro **Aggiungi assegnazione**.
1. Fare clic sul selettore **Utenti** nel riquadro **Aggiungi assegnazione**. 

     Verrà visualizzato un elenco di utenti e gruppi di sicurezza, oltre a una casella di testo per cercare e trovare un determinato utente o gruppo. Questa schermata consente di selezionare contemporaneamente più utenti e gruppi.

1. Dopo aver selezionato gli utenti e i gruppi, premere il pulsante **Seleziona** nella parte inferiore della pagina per passare alla parte successiva.
1. Premere il pulsante **Assegna** nella parte inferiore della pagina per completare le assegnazioni di utenti e gruppi all'app. 
1. Verificare che gli utenti e i gruppi aggiunti siano presenti nell'elenco **Utenti e gruppi** aggiornato.

