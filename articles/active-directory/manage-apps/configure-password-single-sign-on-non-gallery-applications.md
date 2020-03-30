---
title: Come configurare l'accesso Single Sign-On con password per le app di Azure AD. Documenti Microsoft
description: Come configurare l'accesso Single Sign-On (SSO) con password per le applicazioni aziendali di Azure AD nella piattaforma di identità Microsoft (Azure AD)How to configure password single sign-on (SSO) to your Azure AD enterprise applications in Microsoft identity platform (Azure AD)
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
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063527"
---
# <a name="configure-password-single-sign-on"></a>Configurare l'accesso Single Sign-On tramite password

Quando si [aggiunge un'app per](add-gallery-app.md) la raccolta o [un'app Web non di raccolta](add-non-gallery-app.md) alle applicazioni aziendali di Azure AD, una delle opzioni Single Sign-On disponibili è Single [Sign-On basata](what-is-single-sign-on.md#password-based-sso)su password. Questa opzione è disponibile per qualsiasi Web con una pagina di accesso HTML. L'SSO basato su password, definito anche insieme di credenziali delle password, consente di gestire l'accesso degli utenti e le password per le applicazioni Web che non supportano la federazione delle identità. È utile anche per gli scenari in cui più utenti devono condividere un singolo account, ad esempio per gli account dell'app di social media dell'organizzazione. 

SSO basato su password è un ottimo modo per iniziare a integrare rapidamente le applicazioni in Azure AD e consente di:Password-based SSO is a great way to get integrateing applications into Azure AD quickly, and allows you to:

-   Abilitare l'**accesso Single Sign-On per gli utenti** archiviando e riproducendo in modo sicuro i nomi utente e password per l'applicazione integrata con Azure AD

-   **Supportare applicazioni che richiedono più campi di accesso** per applicazioni che richiedono non solo i campi di nome utente e password per accedere

-   **Personalizzare le etichette** dei campi di input di nome utente e password visualizzati nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando si immettono le credenziali

-   Consentire agli **utenti** di immettere i nomi utente e le password per tutti gli account di applicazioni esistenti digitati manualmente nel [pannello di accesso all'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Consentire a un **membro del gruppo aziendale** di specificare i nome utente e le password assegnati a un utente tramite la funzionalità di [accesso all'applicazione self-service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Consentire a un **amministratore** di specificare un nome utente e una password da utilizzare per singoli utenti o gruppi durante l'accesso all'applicazione utilizzando la funzionalità Aggiorna credenziali 

## <a name="before-you-begin"></a>Prima di iniziare

Se l'applicazione non è stata aggiunta al tenant di Azure AD, vedere [Aggiungere un'app della raccolta](add-gallery-app.md) oppure [Aggiungere un'app non della raccolta](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Aprire l'app e selezionare Single Sign-On password

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell’applicazione cloud o amministratore dell’applicazione per il proprio tenant di Azure AD.

2. Passare ad **Applicazioni Azure Active Directory** > **Enterprise**. Viene visualizzato un esempio casuale delle applicazioni del tenant di Azure AD. 

3. Nel menu **Tipo di applicazione** selezionare **Tutte le applicazioni** e quindi **Applica**.

4. Immettere il nome dell'applicazione nella casella di ricerca, quindi selezionarla nei risultati.

5. Nella sezione **Gestisci** selezionare **Single Sign-On**. 

6. Selezionare **Basato su password**.

7. Immettere l'URL della pagina di accesso basata sul Web dell'applicazione. Questa stringa deve essere la pagina che include il campo di input nome utente.

   ![Single Sign-On basato su password](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Selezionare **Salva**. Azure AD tenta di analizzare la pagina di accesso per un input del nome utente e un input password. Se il tentativo ha esito positivo, il percorso è finito. 
 
> [!NOTE]
> Il passaggio successivo consiste [nell'assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md). Dopo aver assegnato utenti e gruppi, è possibile fornire le credenziali da utilizzare per conto di un utente quando accede all'applicazione. Selezionare **Utenti e gruppi**, selezionare la casella di controllo per la riga dell'utente o del gruppo e quindi fare clic su Aggiorna **credenziali**. Quindi, immettere il nome utente e la password da utilizzare per conto dell'utente o del gruppo. In caso contrario, agli utenti verrà richiesto di immettere le credenziali stesse all'avvio.
 

## <a name="manual-configuration"></a>Configurazione manuale

Se il tentativo di analisi di Azure AD non riesce, è possibile configurare l'accesso manualmente.

1. In ** \<Nome applicazione> Configurazione**selezionare Configura ** \<nome applicazione> Impostazioni Single Sign-On password** per visualizzare la pagina Configura **accesso.** 

2. Selezionare **Rileva manualmente i campi di accesso**. Vengono visualizzate ulteriori istruzioni che descrivono il rilevamento manuale dei campi di accesso.

   ![Configurazione manuale dell'accesso Single Sign-On basato su password](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selezionare **Acquisisci campi di accesso**. Una pagina di stato di acquisizione si apre in una nuova scheda, che mostra che l'acquisizione dei metadati del messaggio **è attualmente in corso.**

4. Se la casella Estensione pannello di **accesso richiesta** viene visualizzata in una nuova scheda, selezionare Installa **ora** per installare l'estensione del browser My Apps **Secure Sign-in Extension.** (L'estensione del browser richiede Microsoft Edge, Chrome o Firefox.) Quindi installare, avviare e abilitare l'estensione e aggiornare la pagina di stato dell'acquisizione.

   L'estensione del browser apre quindi un'altra scheda che visualizza l'URL immesso.
5. Nella scheda con l'URL immesso passare attraverso il processo di accesso. Compilare i campi nome utente e password e provare ad accedere. Non è necessario fornire la password corretta.

   Un prompt chiede di salvare i campi di accesso acquisiti.
6. Selezionare **OK**. L'estensione del browser aggiorna la pagina di stato dell'acquisizione con il messaggio **I metadati sono stati aggiornati per l'applicazione.** La scheda del browser si chiude.

7. Nella pagina **di accesso Configurazione** di Azure AD selezionare **OK, è stato possibile accedere all'app correttamente.**

8. Selezionare **OK**.

Dopo l'acquisizione della pagina di accesso, è possibile assegnare utenti e gruppi ed è possibile impostare criteri delle credenziali proprio come [le normali applicazioni SSO](what-is-single-sign-on.md)con password.

> [!NOTE]
> È possibile caricare un logo icona dell'applicazione usando il pulsante **Carica Logo** nella scheda **Configura** dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](methods-for-assigning-users-and-groups.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
