---
title: Come configurare l'accesso Single Sign-on basato su password per le app Azure AD | Microsoft Docs
description: Come configurare l'accesso Single Sign-on (SSO) basato su password per le applicazioni aziendali Azure AD in Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d191abafbaad123ed47f8eaae6cdd4e48478da7a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422618"
---
# <a name="configure-password-single-sign-on"></a>Configurare l'accesso Single Sign-on basato su password

Quando si [aggiunge un'app della raccolta](add-gallery-app.md) o un' [app Web non della raccolta](add-non-gallery-app.md) alle applicazioni aziendali Azure ad, una delle opzioni Single Sign-on disponibili è [Single Sign-on basato su password](what-is-single-sign-on.md#password-based-sso). Questa opzione è disponibile per qualsiasi Web con una pagina di accesso HTML. L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. È utile anche per scenari in cui più utenti devono condividere un singolo account, ad esempio gli account di app di social media dell'organizzazione. 

SSO basato su password è un ottimo modo per iniziare a integrare rapidamente le applicazioni in Azure AD e consente di:

-   Abilitare l'**accesso Single Sign-On per gli utenti** archiviando e riproducendo in modo sicuro i nomi utente e password per l'applicazione integrata con Azure AD

-   **Supportare applicazioni che richiedono più campi di accesso** per applicazioni che richiedono non solo i campi di nome utente e password per accedere

-   **Personalizzare le etichette** dei campi di input di nome utente e password visualizzati nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando si immettono le credenziali

-   Consentire agli **utenti** di immettere i nomi utente e le password per tutti gli account di applicazioni esistenti digitati manualmente nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Consentire a un **membro del gruppo aziendale** di specificare i nome utente e le password assegnati a un utente tramite la funzionalità di [accesso all'applicazione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Consentire a un **amministratore** di specificare un nome utente e una password che verranno usati da singoli utenti o gruppi durante l'accesso all'applicazione tramite la funzionalità Aggiorna credenziali 

## <a name="before-you-begin"></a>Prima di iniziare

Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [aggiungere un'app della raccolta](add-gallery-app.md) o [aggiungere un'app non della raccolta](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Aprire l'app e selezionare password Single Sign-on

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

2. Passare a **Azure Active Directory** > **applicazioni aziendali**. Viene visualizzato un esempio casuale delle applicazioni del tenant di Azure AD. 

3. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**.

4. Immettere il nome dell'applicazione nella casella di ricerca e quindi selezionare l'applicazione dai risultati.

5. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

6. Selezionare **basata su password**.

7. Immettere l'URL della pagina di accesso basata sul Web dell'applicazione. Questa stringa deve essere la pagina che include il campo di input username.

   ![Accesso Single Sign-On basato su password](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selezionare **Salva**. Azure AD tenta di analizzare la pagina di accesso per un input di nome utente e un input di password. Se il tentativo ha esito positivo, l'operazione è completata. 
 
> [!NOTE]
> Il passaggio successivo consiste nell' [assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md). Dopo aver assegnato utenti e gruppi, è possibile fornire le credenziali da usare per conto di un utente durante l'accesso all'applicazione. Selezionare **utenti e gruppi**, selezionare la casella di controllo per la riga dell'utente o del gruppo, quindi fare clic su **Aggiorna credenziali**. Immettere quindi il nome utente e la password da utilizzare per conto dell'utente o del gruppo. In caso contrario, agli utenti verrà richiesto di immettere le credenziali all'avvio.
 

## <a name="manual-configuration"></a>Configurazione manuale

Se il tentativo di analisi del Azure AD ha esito negativo, è possibile configurare l'accesso manualmente.

1. **In\<nome applicazione > configurazione**selezionare **Configura \<nome applicazione > impostazioni di Single Sign-on password** per visualizzare la pagina **Configura accesso** . 

2. Selezionare **rileva manualmente i campi di accesso**. Verranno visualizzate altre istruzioni che descrivono il rilevamento manuale dei campi di accesso.

   ![Configurazione manuale di Single Sign-on basato su password](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selezionare **Acquisisci campi di accesso**. Viene visualizzata una pagina stato acquisizione in una nuova scheda, che mostra che l' **acquisizione dei metadati del messaggio è attualmente in corso**.

4. Se la casella di controllo **estensione pannello di accesso** è visualizzata in una nuova scheda, selezionare **Installa ora** per installare l'estensione del browser dell'estensione per l'accesso **sicuro alle app personali** . Per l'estensione del browser è necessario Microsoft Edge, Chrome o Firefox. Quindi, installare, avviare e abilitare l'estensione e aggiornare la pagina stato acquisizione.

   L'estensione del browser apre quindi un'altra scheda che Visualizza l'URL immesso.
5. Nella scheda con l'URL immesso scorrere il processo di accesso. Inserire i campi nome utente e password e provare ad accedere. Non è necessario specificare la password corretta.

   Viene richiesto di salvare i campi di accesso acquisiti.
6. Selezionare **OK**. L'estensione del browser aggiorna la pagina stato acquisizione con i **metadati del messaggio aggiornati per l'applicazione**. La scheda del browser si chiude.

7. Nella pagina Azure AD **Configura accesso** fare clic su OK. **è stato possibile accedere correttamente all'app**.

8. Selezionare **OK**.

Dopo l'acquisizione della pagina di accesso, è possibile assegnare utenti e gruppi ed è possibile configurare criteri di credenziale come [le normali applicazioni SSO con password](what-is-single-sign-on.md).

> [!NOTE]
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](configure-automatic-user-provisioning-portal.md)
