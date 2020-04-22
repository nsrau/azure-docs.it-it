---
title: Limitare l'app Azure AD a un set di utenti Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come limitare l'accesso alle app registrate in Azure AD a un set di utenti.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8bdc7e6e3795719128a8ecfb1e8bc97c1a9a08c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759035"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Procedura: Limitare l'app Azure AD a un set di utenti in un tenant di Azure ADHow to: Restrict your Azure AD app to a set of users in an Azure AD tenant

Per impostazione predefinita, le applicazioni registrate in un tenant di Azure Active Directory (Azure AD) sono disponibili per tutti gli utenti del tenant che eseguono correttamente l'autenticazione.

Analogamente, nel caso di un app [multi-tenant](howto-convert-app-to-be-multi-tenant.md), tutti gli utenti del tenant di Azure AD in cui è stato eseguito il provisioning dell'app potranno accedere all'applicazione dopo aver eseguito l'autenticazione nel rispettivo tenant.

Gli sviluppatori e gli amministratori di tenant devono spesso soddisfare l'esigenza di limitare un'app a un determinato set di utenti. Per soddisfare questo requisito, gli sviluppatori possono usare comuni criteri di autorizzazione come il controllo degli accessi in base al ruolo, ma questo approccio richiede una considerevole quantità di lavoro da parte dello sviluppatore.

Gli amministratori tenant e gli sviluppatori possono limitare un'app a un set specifico di utenti o gruppi di sicurezza nel tenant usando anche questa funzionalità predefinita di Azure AD.

## <a name="supported-app-configurations"></a>Configurazioni di app supportate

La possibilità di limitare un'app a un set specifico di utenti o gruppi di sicurezza in un tenant può essere adottata con i tipi di applicazione seguenti:

- Applicazioni configurate per l'accesso Single Sign-On federato con autenticazione basata su SAML
- Applicazioni Application Proxy che usano la preautenticazione di Azure Active Directory
- Applicazioni create direttamente sulla piattaforma applicativa Azure AD che usano l'autenticazione OAuth 2.0/OpenID Connect dopo che un utente o un amministratore ha fornito il consenso per tale applicazione.

     > [!NOTE]
     > Questa funzionalità è disponibile solo per app Web/API Web e applicazioni aziendali. Le app registrate come [native](quickstart-v1-integrate-apps-with-azure-ad.md) non possono essere limitate a un set di utenti o gruppi di sicurezza all'interno del tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>Aggiornare l'app per abilitare l'assegnazione degli utenti

Esistono due modi per creare un'applicazione con l'assegnazione utente abilitata. Uno richiede il ruolo **di amministratore globale,** il secondo non lo richiede.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Applicazioni aziendali (richiede il ruolo di amministratore globale)

1. Passare al portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**
1. Nella barra superiore selezionare l'account connesso. 
1. Nell'elenco **Directory** selezionare il tenant di Azure AD in cui verrà registrata l'app.
1. Nel menu di spostamento a sinistra selezionare **Azure Active Directory**. Se Azure Active Directory non è disponibile nel riquadro di spostamento, attenersi alla seguente procedura:

    1. Selezionare **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
    1. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare l'elemento Azure Active **Directory** dal risultato.

1. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali** all'interno del menu di spostamento a sinistra di **Azure Active Directory**.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

     Se l'applicazione desiderata non viene visualizzata, usare i filtri disponibili nella parte superiore dell'elenco **Tutte le applicazioni** per limitare l'elenco o scorrerlo verso il basso fino a trovare l'applicazione.

1. Selezionare dall'elenco l'applicazione che si vuole assegnare a un utente o un gruppo di sicurezza.
1. Nella pagina **Panoramica** dell'applicazione selezionare **Proprietà** dal menu di spostamento a sinistra dell'applicazione.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti nel tenant devono prima essere assegnati a questa applicazione o non saranno in grado di accedere all'applicazione.
1. Selezionare **Salva** per salvare questa modifica di configurazione.

### <a name="app-registration"></a>Registrazione delle app

1. Passare al [**portale di Azure**](https://portal.azure.com/).
1. Nella barra superiore selezionare l'account connesso. 
1. Nell'elenco **Directory** selezionare il tenant di Azure AD in cui verrà registrata l'app.
1. Nel menu di spostamento a sinistra selezionare **Azure Active Directory**.
1. Nel riquadro **Azure Active Directory** selezionare Registrazioni app dal menu di spostamento a sinistra di Azure Active Directory.In the Azure Active Directory pane, select **App Registrations** from the **Azure Active Directory** left-hand navigation menu.
1. Crea o seleziona l'app che vuoi gestire. Devi essere **proprietario** di questa registrazione dell'app.
1. Nella pagina **Panoramica** dell'applicazione seguire il collegamento **Applicazione gestita nella directory locale** sotto gli elementi essenziali nella parte superiore della pagina. Verrà visualizzato _l'applicazione Enterprise gestita_ della registrazione dell'app.
1. Nel pannello di spostamento a sinistra selezionare **Proprietà**.
1. Trovare l'impostazione **Assegnazione utenti obbligatoria** e impostarla su **Sì**. Quando questa opzione è impostata su **Sì**, gli utenti nel tenant devono prima essere assegnati a questa applicazione o non saranno in grado di accedere all'applicazione.
1. Selezionare **Salva** per salvare questa modifica di configurazione.

## <a name="assign-users-and-groups-to-the-app"></a>Assegnare utenti e gruppi all'app

Dopo aver configurato l'app per abilitare l'assegnazione degli utenti, è possibile ora assegnare all'app gli utenti e i gruppi desiderati.

1. Selezionare il riquadro **Utenti e gruppi** nel menu di spostamento a sinistra dell'applicazione.
1. Nella parte superiore dell'elenco **Utenti e gruppi** selezionare il pulsante **Aggiungi utente** per aprire il riquadro **Aggiungi assegnazione**.
1. Fare clic sul selettore **Utenti** nel riquadro **Aggiungi assegnazione**. 

     Verrà visualizzato un elenco di utenti e gruppi di sicurezza, oltre a una casella di testo per cercare e trovare un determinato utente o gruppo. Questa schermata consente di selezionare contemporaneamente più utenti e gruppi.

1. Dopo aver selezionato gli utenti e i gruppi, premere il pulsante **Seleziona** nella parte inferiore della pagina per passare alla parte successiva.
1. (Facoltativo) Se sono stati definiti ruoli dell'app nell'applicazione, è possibile usare l'opzione **Seleziona ruolo** per assegnare gli utenti e i gruppi selezionati a uno dei ruoli dell'applicazione. 
1. Premere il pulsante **Assegna** nella parte inferiore della pagina per completare le assegnazioni di utenti e gruppi all'app. 
1. Verificare che gli utenti e i gruppi aggiunti siano presenti nell'elenco **Utenti e gruppi** aggiornato.

## <a name="more-information"></a>Ulteriori informazioni

- [Procedura: aggiungere ruoli dell'app nell'applicazioneHow to: Add app roles in your application](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
- [Aggiungere l'autorizzazione usando le attestazioni di & ruoli dell'app a un'app Web di base ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM) (Utilizzo dei gruppi di sicurezza e dei ruoli applicazione nelle app - Video)
- [Azure Active Directory, now with Group Claims and Application Roles](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862) (Azure Active Directory, ora con attestazioni di gruppo e ruoli applicazione)
- [Manifesto dell'app Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)