---
title: Creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale
description: Informazioni su come creare una pagina di destinazione per l'offerta SaaS transazionale.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 737e2fc682e630775b763dd2f22f904d895a120f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921267"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>Creazione della pagina di destinazione per l'offerta SaaS transazionale nel Marketplace commerciale

Questo articolo illustra il processo di creazione di una pagina di destinazione per un'app SaaS transazionale che verrà venduta sul Marketplace commerciale Microsoft.

## <a name="overview"></a>Panoramica

È possibile considerare la pagina di destinazione come "lobby" per l'offerta di Software as a Service (SaaS). Una volta che il compratore sottoscrive un'offerta, il Marketplace commerciale li indirizza alla pagina di destinazione per attivare e configurare la sottoscrizione per l'applicazione SaaS. Questo è un passaggio di conferma dell'ordine che consente all'acquirente di vedere cosa hanno acquistato e confermare i dettagli dell'account. Utilizzando Azure Active Directory (Azure AD) e Microsoft Graph, sarà possibile abilitare Single Sign-On (SSO) per l'acquirente e ottenere informazioni importanti sull'acquirente che è possibile utilizzare per confermare e attivare la sottoscrizione, inclusi il nome, l'indirizzo di posta elettronica e l'organizzazione.

Poiché le informazioni necessarie per attivare la sottoscrizione sono limitate e fornite da Azure AD e Microsoft Graph, non è necessario richiedere informazioni che richiedono più del consenso di base. Se sono necessari i dettagli dell'utente che richiedono il consenso aggiuntivo per l'applicazione, è necessario richiedere queste informazioni dopo il completamento dell'attivazione della sottoscrizione. Questo consente l'attivazione di sottoscrizioni non unisenti per l'acquirente e riduce il rischio di abbandono.

La pagina di destinazione include in genere quanto segue:

- Presentare il nome dell'offerta e del piano acquistato, nonché le condizioni per la fatturazione.
- Presentare i dettagli dell'account dell'acquirente, inclusi il nome e il cognome, l'organizzazione e il messaggio di posta elettronica.
- Chiedere all'acquirente di confermare o sostituire i dettagli di un account diverso.
- Guida l'acquirente nei passaggi successivi dopo l'attivazione. Ad esempio, ricevere un messaggio di posta elettronica di benvenuto, gestire la sottoscrizione, ottenere supporto o leggere la documentazione.

> [!NOTE]
> L'acquirente verrà anche indirizzato alla pagina di destinazione durante la gestione della sottoscrizione dopo l'attivazione. Dopo aver attivato la sottoscrizione del compratore, è necessario usare SSO per consentire all'utente di eseguire l'accesso. È consigliabile indirizzare l'utente a un profilo account o a una pagina di configurazione.

Nelle sezioni seguenti viene illustrato il processo di creazione di una pagina di destinazione:

1. [Creare una registrazione dell'app Azure ad](#create-an-azure-ad-app-registration) per la pagina di destinazione.
2. [Usare un esempio di codice come punto di partenza](#use-a-code-sample-as-a-starting-point) per l'app.
3. [Risolvere il token di identificazione di acquisto del Marketplace](#resolve-the-marketplace-purchase-identification-token) aggiunto all'URL dal Marketplace commerciale.
4. [Leggere le informazioni dalle attestazioni codificate nel token ID](#read-information-from-claims-encoded-in-the-id-token), che è stato ricevuto da Azure ad dopo l'accesso, inviato con la richiesta.
5. [Usare l'API Microsoft Graph](#use-the-microsoft-graph-api) per raccogliere informazioni aggiuntive, come richiesto.
6. [Usare due app Azure ad per migliorare la sicurezza nell'](#use-two-azure-ad-apps-to-improve-security-in-production)ambiente di produzione.

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure AD

Il Marketplace commerciale è completamente integrato con Azure AD. Gli acquirenti arrivano al Marketplace autenticati con un [account Azure ad o un account Microsoft (MSA)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology). Dopo l'acquisto, l'acquirente passa dal Marketplace commerciale all'URL della pagina di destinazione per attivare e gestire la propria sottoscrizione dell'applicazione SaaS. È necessario consentire al buyer di accedere all'applicazione con Azure AD SSO. L'URL della pagina di destinazione viene specificato nella pagina [configurazione tecnica](partner-center-portal/offer-creation-checklist.md#technical-configuration-page) dell'offerta.

Il primo passaggio per usare l'identità è assicurarsi che la pagina di destinazione sia registrata come applicazione Azure AD. La registrazione dell'applicazione consente di usare Azure AD per autenticare gli utenti e richiedere l'accesso alle risorse utente. Può essere considerata la definizione dell'applicazione, che consente al servizio di ottenere informazioni su come rilasciare i token per l'app in base alle impostazioni dell'app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrare una nuova applicazione mediante il portale di Azure

Per iniziare, seguire le istruzioni per la [registrazione di una nuova applicazione](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Per consentire agli utenti di altre aziende di visitare l'app, è necessario scegliere una delle opzioni multi-tenant quando viene richiesto chi può usare l'applicazione.

Se si intende eseguire una query sull'API Microsoft Graph, [configurare la nuova applicazione per accedere alle API Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis). Quando si selezionano le autorizzazioni API per l'applicazione, il valore predefinito di **User. Read** è sufficiente per raccogliere informazioni di base sull'acquirente per rendere il processo di onboarding uniforme e automatico. Non richiedere alcuna autorizzazione dell'API con etichetta **richiede il consenso dell'amministratore**, poiché in questo modo tutti gli utenti non amministratori non possono visitare la pagina di destinazione.

Se è necessario disporre di autorizzazioni elevate come parte del processo di caricamento o provisioning, provare a usare la funzionalità di [consenso incrementale](https://aka.ms/incremental-consent) di Azure ad in modo che tutti gli acquirenti inviati dal Marketplace possano interagire inizialmente con la pagina di destinazione.

## <a name="use-a-code-sample-as-a-starting-point"></a>Usare un esempio di codice come punto di partenza

Sono state fornite diverse app di esempio che implementano un sito Web semplice con Azure AD account di accesso abilitato. Dopo che l'applicazione è stata registrata in Azure AD, il pannello **avvio rapido** offre un elenco di tipi di applicazioni comuni e stack di sviluppo, come illustrato nella figura 1. Scegliere quella corrispondente all'ambiente e seguire le istruzioni per il download e l'installazione.

***Figura 1: pannello Guida introduttiva nella portale di Azure***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Viene illustrato il pannello avvio rapido nel portale di Azure.":::

Dopo aver scaricato il codice e configurato l'ambiente di sviluppo, modificare le impostazioni di configurazione nell'app in modo da riflettere l'ID applicazione, l'ID tenant e il segreto client registrati nella procedura precedente. Si noti che i passaggi esatti variano a seconda dell'esempio che si sta usando.

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>Usare due app Azure AD per migliorare la sicurezza nell'ambiente di produzione

Questo articolo presenta una versione semplificata dell'architettura per l'implementazione di una pagina di destinazione per l'offerta SaaS del Marketplace commerciale. Quando si esegue la pagina in produzione, è consigliabile migliorare la sicurezza comunicando con le API di evasione SaaS solo tramite un'altra applicazione protetta. Questa operazione richiede la creazione di due nuove applicazioni:

- In primo luogo, l'applicazione della pagina di destinazione multi-tenant descritta fino a questo punto, eccetto senza la funzionalità per contattare le API di evasione SaaS. Questa funzionalità verrà scaricata in un'altra applicazione, come descritto di seguito.
- In secondo luogo, un'applicazione per il proprietario delle comunicazioni con le API di evasione SaaS. Questa applicazione deve essere a singolo tenant, solo per l'uso da parte dell'organizzazione ed è possibile stabilire un elenco di controllo di accesso per limitare l'accesso alle API solo da questa app.

In questo modo la soluzione è in grado di funzionare in scenari che rispettano il principio [di separazione dei problemi](https://docs.microsoft.com/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns) . Ad esempio, la pagina di destinazione usa la prima app Azure AD registrata per accedere all'utente. Dopo che l'utente ha eseguito l'accesso, la pagina di destinazione usa la seconda Azure AD per richiedere un token di accesso per chiamare l'API di evasione SaaS e chiamare l'operazione di risoluzione.

## <a name="resolve-the-marketplace-purchase-identification-token"></a>Risolvere il token di identificazione dell'acquisto del Marketplace

Quando l'acquirente viene inviato alla pagina di destinazione, viene aggiunto un token al parametro URL. Questo token è diverso dal token rilasciato da Azure AD e dal token di accesso usato per l'autenticazione da servizio a servizio e viene usato come input per la chiamata di risoluzione delle [API di evasione Saas](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) per ottenere i dettagli della sottoscrizione. Come per tutte le chiamate alle API di evasione SaaS, la richiesta da servizio a servizio verrà autenticata con un token di accesso basato sull'utente Azure AD ID applicazione dell'app per l'autenticazione da servizio a servizio.

> [!NOTE]
> Nella maggior parte dei casi, è preferibile effettuare questa chiamata da una seconda applicazione a tenant singolo. Vedere [usare due app Azure ad per migliorare la sicurezza in produzione](#use-two-azure-ad-apps-to-improve-security-in-production) più avanti in questo articolo.

### <a name="request-an-access-token"></a>Richiedere un token di accesso

Per autenticare l'applicazione con le API di evasione SaaS, è necessario un token di accesso, che può essere generato chiamando il Azure AD endpoint OAuth. Vedere [come ottenere il token di autorizzazione dell'editore](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

### <a name="call-the-resolve-endpoint"></a>Chiamare l'endpoint di risoluzione

Le API di evasione SaaS implementano l'endpoint di [risoluzione](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) che può essere chiamato per confermare la validità del token del Marketplace e per restituire informazioni sulla sottoscrizione.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Leggere le informazioni dalle attestazioni codificate nel token ID

Come parte del flusso di [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) , Azure ad aggiunge un [token ID](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) alla richiesta quando l'acquirente viene inviato alla pagina di destinazione. Questo token contiene più elementi di informazioni di base che potrebbero essere utili nel processo di attivazione, incluse le informazioni riportate in questa tabella.

| Valore | Descrizione |
| ------------ | ------------- |
| aud | Destinatari per questo token. In questo caso, deve corrispondere all'ID dell'applicazione ed essere convalidato. |
| preferred_username | Nome utente principale dell'utente visitato. Potrebbe trattarsi di un indirizzo di posta elettronica, un numero di telefono o un altro identificatore. |
| email | Indirizzo di posta elettronica dell'utente. Si noti che questo campo può essere vuoto. |
| name | Valore leggibile che identifica l'oggetto del token. In questo caso, sarà il nome dell'acquirente. |
| oid | Identificatore nel sistema di identità Microsoft che identifica in modo univoco l'utente tra le applicazioni. Microsoft Graph restituirà questo valore come proprietà ID per un determinato account utente. |
| tid | Identificatore che rappresenta il tenant di Azure AD dal quale il compratore è. Nel caso di un'identità MSA, questo sarà sempre ``9188040d-6c67-4c5b-b112-36a304b66dad`` . Per altre informazioni, vedere la nota nella sezione successiva: usare l'API Microsoft Graph. |
| sub | Identificatore che identifica in modo univoco l'utente in questa applicazione specifica. |
|||

## <a name="use-the-microsoft-graph-api"></a>Usare l'API Microsoft Graph

Il token ID contiene informazioni di base per identificare l'acquirente, ma il processo di attivazione potrebbe richiedere dettagli aggiuntivi, ad esempio la società dell'acquirente, per completare il processo di onboarding. Usare l' [API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) per richiedere queste informazioni per evitare di imporre all'utente di immettere nuovamente i dettagli. Per impostazione predefinita, le autorizzazioni **utente standard. Read** includono le informazioni seguenti.

| Valore | Descrizione |
| ------------ | ------------- |
| displayName | Nome visualizzato nella rubrica per l'utente. |
| givenName | Nome dell'utente. |
| jobTitle | Titolo del processo dell'utente. |
| mail | Indirizzo SMTP dell'utente. |
| mobilePhone | Numero di telefono cellulare primario per l'utente. |
| preferredLanguage | Codice ISO 639-1 per la lingua preferita dell'utente. |
| surname | Cognome dell'utente. |
|||

È possibile selezionare proprietà aggiuntive, ad esempio il nome della società dell'utente o la località dell'utente (paese), da includere nella richiesta. Per altri dettagli, vedere [proprietà per il tipo di risorsa utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) .

La maggior parte delle app registrate con Azure AD concedere autorizzazioni delegate per la lettura delle informazioni dell'utente dal tenant Azure AD della propria azienda. Qualsiasi richiesta di Microsoft Graph per tali informazioni deve essere accompagnata da un token di accesso per l'autenticazione. I passaggi specifici per generare il token di accesso variano a seconda dello stack di tecnologia in uso, ma il codice di esempio conterrà un esempio. Per altre informazioni, vedere [ottenere l'accesso per conto di un utente](https://docs.microsoft.com/graph/auth-v2-user).

> [!NOTE]
> Gli account del tenant MSA (con ID tenant ``9188040d-6c67-4c5b-b112-36a304b66dad`` ) non restituiranno altre informazioni rispetto a quelle già raccolte con il token ID. È quindi possibile ignorare questa chiamata al API Graph per questi account.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'offerta SaaS nel Marketplace commerciale](./partner-center-portal/create-new-saas-offer.md)
