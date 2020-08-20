---
title: Come configurare Single Sign-On basate su password per le app Azure AD
description: Come configurare l'accesso Single Sign-on basato Single Sign-On su password per le applicazioni Azure AD in Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: be6a8a58f1d66df9d0fe557584c4731e42ae9c59
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640549"
---
# <a name="configure-password-based-single-sign-on"></a>Configurare Single Sign-On basate su password

Nella [serie di guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni si è appreso come usare Azure ad come provider di identità (IDP) per un'applicazione. Nella Guida introduttiva viene configurato l'accesso Single Sign-on basato su SAML. Un'altra opzione è Single Sign-on basato su password. Questo articolo illustra in modo più dettagliato l'opzione SSO basato su password. 

Questa opzione è disponibile per qualsiasi sito Web con una pagina di accesso HTML. SSO basato su password è anche noto come insieme di credenziali delle password. SSO basato su password consente di gestire l'accesso e le password degli utenti alle applicazioni Web che non supportano la Federazione delle identità. È anche utile quando più utenti devono condividere un singolo account, ad esempio gli account di app di social media dell'organizzazione.

SSO basato su password è un ottimo modo per iniziare a integrare rapidamente le applicazioni in Azure AD e consente di:

- Abilitare Single Sign-On per gli utenti archiviando e riproducendo in modo sicuro nomi utente e password

- Supportare applicazioni che richiedono più campi di accesso per applicazioni che richiedono non solo i campi di nome utente e password per accedere

- Personalizzare le etichette dei campi nome utente e password visualizzati dagli utenti nelle [app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando immettono le credenziali

- Consentire agli utenti di fornire i propri nomi utente e password per tutti gli account dell'applicazione esistenti digitati manualmente.

- Consentire a un membro del gruppo aziendale di specificare i nome utente e le password assegnati a un utente tramite la funzionalità di [accesso all'applicazione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Consentire a un amministratore di specificare un nome utente e una password da usare per singoli utenti o gruppi quando accedono all'applicazione con la funzionalità Aggiorna credenziali 

## <a name="before-you-begin"></a>Prima di iniziare

L'uso di Azure AD come provider di identità (IdP) e la configurazione di Single Sign-On (SSO) può essere semplice o complesso a seconda dell'applicazione usata. Alcune applicazioni possono essere configurate con poche azioni. Altri richiedono una configurazione approfondita. Per iniziare rapidamente, esaminare la serie di [guide introduttive](view-applications-portal.md) sulla gestione delle applicazioni. Se l'applicazione che si sta aggiungendo è semplice, probabilmente non è necessario leggere questo articolo. Se l'applicazione che si sta aggiungendo richiede una configurazione personalizzata ed è necessario usare l'accesso SSO basato su password, questo articolo è adatto all'utente.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui l'opzione **Single Sign-on** non verrà spostata per un'applicazione nelle **applicazioni aziendali**. 
>
> Se l'applicazione è stata registrata usando **registrazioni app** , la funzionalità Single Sign-on è configurata in modo da usare OAuth OIDC per impostazione predefinita. In questo caso, l'opzione **Single Sign-on** non verrà visualizzata nella finestra di navigazione in **applicazioni aziendali**. Quando si usa **registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per ulteriori informazioni sul file manifesto, vedere [Azure Active Directory manifesto dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Per altre informazioni sugli standard SSO, vedere [autenticazione e autorizzazione con la piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Gli altri scenari in cui l' **accesso Single Sign-on** non sarà presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non ha le autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire l' **accesso Single Sign-on** , ma non sarà possibile salvarlo. Per ulteriori informazioni sui ruoli amministrativi Azure AD, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Configurazione di base

Nella [serie di avvio rapido](view-applications-portal.md)si è appreso come aggiungere un'app al tenant, consentendo Azure ad sa che viene usato come provider di identità per l'app. Alcune app sono già preconfigurate e vengono visualizzate nella raccolta di Azure AD. Altre app non sono presenti nella raccolta ed è necessario creare un'app generica e configurarla manualmente. A seconda dell'app, l'opzione SSO basata su password potrebbe non essere disponibile. Se l'elenco di opzioni basato su password non è visualizzato nella pagina Single Sign-On per l'app, non è disponibile.

La pagina di configurazione per SSO basato su password è semplice. Include solo l'URL della pagina di accesso utilizzata dall'app. Questa stringa deve essere la pagina che include il campo di input username.

Dopo aver immesso l'URL, selezionare **Salva**. Azure AD analizza il codice HTML della pagina di accesso per i campi di input di nome utente e password. Se il tentativo ha esito positivo, l'operazione è completata.
 
Il passaggio successivo consiste nell' [assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md). Dopo aver assegnato utenti e gruppi, è possibile fornire le credenziali da usare per un utente durante l'accesso all'applicazione. Selezionare **utenti e gruppi**, selezionare la casella di controllo per la riga dell'utente o del gruppo, quindi selezionare **Aggiorna credenziali**. Infine, immettere il nome utente e la password da usare per l'utente o il gruppo. In caso contrario, agli utenti verrà richiesto di immettere le credenziali all'avvio.
 

## <a name="manual-configuration"></a>Configurazione manuale

Se il tentativo di analisi del Azure AD ha esito negativo, è possibile configurare l'accesso manualmente.

1. In ** \<application name> configurazione**selezionare **Configura \<application name> le impostazioni di Single Sign-on basato su password** per visualizzare la pagina **Configura accesso** . 

2. Selezionare **rileva manualmente i campi di accesso**. Verranno visualizzate altre istruzioni che descrivono il rilevamento manuale dei campi di accesso.

   ![Configurazione manuale delle Single Sign-On basate su password](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selezionare **Acquisisci campi di accesso**. Viene visualizzata una pagina stato acquisizione in una nuova scheda, che mostra che l' **acquisizione dei metadati del messaggio è attualmente in corso**.

4. Se la casella **estensione app personali richiesta** viene visualizzata in una nuova scheda, selezionare **Installa ora** per installare l'estensione del browser dell'estensione per l' **accesso sicuro alle app personali** . Per l'estensione del browser è necessario Microsoft Edge, Chrome o Firefox. Quindi, installare, avviare e abilitare l'estensione e aggiornare la pagina stato acquisizione.

   L'estensione del browser apre quindi un'altra scheda che Visualizza l'URL immesso.
5. Nella scheda con l'URL immesso scorrere il processo di accesso. Inserire i campi nome utente e password e provare ad accedere. Non è necessario specificare la password corretta.

   Viene richiesto di salvare i campi di accesso acquisiti.
6. Selezionare **OK**. L'estensione del browser aggiorna la pagina stato acquisizione con i **metadati del messaggio aggiornati per l'applicazione**. La scheda del browser si chiude.

7. Nella pagina Azure AD **Configura accesso** fare clic su OK. **è stato possibile accedere correttamente all'app**.

8. Selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
