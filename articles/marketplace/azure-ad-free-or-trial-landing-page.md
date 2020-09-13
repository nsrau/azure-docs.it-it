---
title: Crea la pagina di destinazione per l'offerta SaaS gratuita o di valutazione nel Marketplace commerciale
description: Scopri come creare una pagina di destinazione per l'offerta SaaS gratuita o di valutazione.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/04/2020
ms.openlocfilehash: b01b482b967ba6db90aa80ba537457597fb91046
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488610"
---
# <a name="build-the-landing-page-for-your-free-or-trial-saas-offer-in-the-commercial-marketplace"></a>Crea la pagina di destinazione per l'offerta SaaS gratuita o di valutazione nel Marketplace commerciale

Questo articolo illustra il processo di creazione di una pagina di destinazione per un'app SaaS gratuita o di valutazione che verrà venduta sul Marketplace commerciale Microsoft.

## <a name="overview"></a>Panoramica

È possibile considerare la pagina di destinazione come "lobby" per l'offerta di Software as a Service (SaaS). Quando il cliente sceglie di ottenere l'app, il Marketplace commerciale li indirizza alla pagina di destinazione per attivare e configurare la sottoscrizione per l'applicazione SaaS. Quando si crea un'offerta di Software as a Service (SaaS), nel centro per i partner è possibile scegliere se vendere o meno [tramite Microsoft](plan-saas-offer.md#listing-options). Se si vuole solo elencare l'offerta nel Marketplace commerciale Microsoft e non vendere tramite Microsoft, è possibile specificare il modo in cui i potenziali clienti possono interagire con l'offerta. Quando si Abilita l'opzione **Get it Now (free)** o **Free Trial** list, è necessario specificare un URL della pagina di destinazione a cui l'utente può accedere per accedere alla sottoscrizione o alla versione di valutazione gratuita.

Lo scopo della pagina di destinazione è semplicemente ricevere l'utente per poter attivare la versione di valutazione gratuita o la sottoscrizione gratuita. Utilizzando Azure Active Directory (Azure AD) e Microsoft Graph, sarà possibile abilitare Single Sign-On (SSO) per l'utente e ottenere informazioni importanti sull'utente che è possibile utilizzare per attivare la versione di valutazione gratuita o la sottoscrizione gratuita, inclusi il nome, l'indirizzo di posta elettronica e l'organizzazione.

Poiché le informazioni necessarie per attivare la sottoscrizione sono limitate e fornite da Azure AD e Microsoft Graph, non è necessario richiedere informazioni che richiedono più del consenso di base. Se sono necessari i dettagli dell'utente che richiedono il consenso aggiuntivo per l'applicazione, è necessario richiedere queste informazioni dopo il completamento dell'attivazione della sottoscrizione. Questo consente l'attivazione di sottoscrizioni prive di attrito per l'utente e riduce il rischio di abbandono.

Nella pagina di destinazione sono in genere incluse le informazioni e le opzioni di elenco seguenti:

- Presentare il nome e i dettagli della versione di valutazione gratuita o della sottoscrizione gratuita. Ad esempio, specificare i limiti di utilizzo o la durata di una versione di valutazione.
- Presentare i dettagli dell'account dell'utente, ad esempio nome e cognome, organizzazione e indirizzo di posta elettronica.
- Chiedere all'utente di confermare o sostituire i dettagli di un account diverso.
- Guida l'utente nei passaggi successivi dopo l'attivazione. Ad esempio, ricevere un messaggio di posta elettronica di benvenuto, gestire la sottoscrizione, ottenere supporto o leggere la documentazione.

Nelle sezioni seguenti di questo articolo verrà illustrato il processo di creazione di una pagina di destinazione:

1. [Creare una registrazione dell'app Azure ad](#create-an-azure-ad-app-registration) per la pagina di destinazione.
2. [Usare un esempio di codice come punto di partenza](#use-a-code-sample-as-a-starting-point) per l'app.
3. [Leggere le informazioni dalle attestazioni codificate nel token ID](#read-information-from-claims-encoded-in-the-id-token), ricevute da Azure ad dopo l'accesso, che è stato inviato con la richiesta.
4. [Usare l'API Microsoft Graph](#use-the-microsoft-graph-api) per raccogliere informazioni aggiuntive, come richiesto.

## <a name="create-an-azure-ad-app-registration"></a>Creare una registrazione dell'app Azure AD

Il Marketplace commerciale è completamente integrato con Azure AD. Gli utenti arrivano al Marketplace autenticati con un [account Azure ad o un account Microsoft (MSA)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology). Dopo aver acquisito una sottoscrizione di valutazione gratuita o gratuita tramite l'offerta di solo elenco, l'utente passa dal Marketplace commerciale all'URL della pagina di destinazione per attivare e gestire la propria sottoscrizione all'applicazione SaaS. È necessario consentire all'utente di accedere all'applicazione con Azure AD SSO. L'URL della pagina di destinazione viene specificato nella pagina [configurazione tecnica](plan-saas-offer.md#technical-information) dell'offerta.

Il primo passaggio per usare l'identità è assicurarsi che la pagina di destinazione sia registrata come applicazione Azure AD. La registrazione dell'applicazione consente di usare Azure AD per autenticare gli utenti e richiedere l'accesso alle risorse utente. Può essere considerata la definizione dell'applicazione, che consente al servizio di ottenere informazioni su come rilasciare i token per l'app in base alle impostazioni dell'app.

### <a name="register-a-new-application-using-the-azure-portal"></a>Registrare una nuova applicazione mediante il portale di Azure

Per iniziare, seguire le istruzioni per la [registrazione di una nuova applicazione](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Per consentire agli utenti di altre aziende di visitare l'app, è necessario scegliere gli **account in qualsiasi directory organizzativa (qualsiasi Azure ad directory (multi-tenant) e gli account Microsoft personali (ad esempio Skype o Xbox)** quando viene chiesto a chi può usare l'applicazione.

Se si intende eseguire una query sull'API Microsoft Graph, [configurare la nuova applicazione per accedere alle API Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis). Quando si selezionano le autorizzazioni API per l'applicazione, il valore predefinito di **User. Read** è sufficiente per raccogliere le informazioni di base sull'utente per rendere il processo di onboarding semplice e automatico. Non richiedere alcuna autorizzazione dell'API con etichetta **richiede il consenso dell'amministratore**, poiché in questo modo tutti gli utenti non amministratori non possono visitare la pagina di destinazione.

Se è necessario disporre di autorizzazioni elevate come parte del processo di caricamento o provisioning, provare a usare la funzionalità di [consenso incrementale](https://aka.ms/incremental-consent) di Azure ad in modo che tutti gli utenti inviati dal Marketplace possano interagire inizialmente con la pagina di destinazione.

## <a name="use-a-code-sample-as-a-starting-point"></a>Usare un esempio di codice come punto di partenza

Microsoft ha fornito diverse app di esempio che implementano un sito Web semplice con Azure AD account di accesso abilitato. Dopo che l'applicazione è stata registrata in Azure AD, il pannello **avvio rapido** offre un elenco di tipi di applicazioni comuni e stack di sviluppo (Figura 1). Scegliere quella corrispondente all'ambiente e seguire le istruzioni per il download e l'installazione.

***Figura 1: pannello Guida introduttiva nella portale di Azure***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Viene illustrato il pannello avvio rapido nel portale di Azure.":::

Dopo aver scaricato il codice e configurato l'ambiente di sviluppo, modificare le impostazioni di configurazione nell'app in modo da riflettere l'ID applicazione, l'ID tenant e il segreto client registrati nella procedura precedente. Si noti che i passaggi esatti variano a seconda dell'esempio che si sta usando.

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>Leggere le informazioni dalle attestazioni codificate nel token ID

Come parte del flusso di [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) , Azure ad aggiunge un [token ID](https://docs.microsoft.com/azure/active-directory/develop/id-tokens) alla richiesta quando l'utente viene inviato alla pagina di destinazione. Questo token contiene più elementi di informazioni di base che potrebbero essere utili nel processo di attivazione, incluse le informazioni riportate in questa tabella.

| Valore | Descrizione |
| ------------ | ------------- |
| aud | Destinatari per questo token. In questo caso, deve corrispondere all'ID dell'applicazione ed essere convalidato. |
| preferred_username | Nome utente principale dell'utente visitato. Potrebbe trattarsi di un indirizzo di posta elettronica, un numero di telefono o un altro identificatore. |
| email | Indirizzo di posta elettronica dell'utente. Si noti che questo campo può essere vuoto. |
| name | Valore leggibile che identifica l'oggetto del token. In questo caso, sarà il nome dell'utente. |
| oid | Identificatore nel sistema di identità Microsoft che identifica in modo univoco l'utente tra le applicazioni. Microsoft Graph restituirà questo valore come proprietà ID per un determinato account utente. |
| tid | Identificatore che rappresenta il tenant Azure AD dall'utente. Nel caso di un'identità MSA, questo sarà sempre `9188040d-6c67-4c5b-b112-36a304b66dad` . Per altre informazioni, vedere la nota nella sezione successiva: usare Microsoft Graph API. |
| sub | Identificatore che identifica in modo univoco l'utente in questa applicazione specifica. |
|||

## <a name="use-the-microsoft-graph-api"></a>Usare l'API Microsoft Graph

Il token ID contiene informazioni di base per identificare l'utente, ma il processo di attivazione potrebbe richiedere dettagli aggiuntivi, ad esempio la società dell'utente, per completare il processo di onboarding. Usare l' [API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) per richiedere queste informazioni per evitare di imporre all'utente di immettere nuovamente i dettagli. Per impostazione predefinita, le autorizzazioni **utente standard. Read** includono le seguenti informazioni:

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

È possibile selezionare proprietà aggiuntive, ad esempio il nome della società dell'utente o la località dell'utente (paese), da includere nella richiesta. Per ulteriori informazioni, vedere [proprietà per il tipo di risorsa utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties).

La maggior parte delle app registrate con Azure AD concedere autorizzazioni delegate per la lettura delle informazioni dell'utente dal tenant Azure AD della propria azienda. Qualsiasi richiesta di Microsoft Graph per tali informazioni deve essere accompagnata da un token di accesso come autenticazione. I passaggi specifici per generare il token di accesso variano a seconda dello stack di tecnologia in uso, ma il codice di esempio conterrà un esempio. Per altre informazioni, vedere [ottenere l'accesso per conto di un utente](https://docs.microsoft.com/graph/auth-v2-user).

> [!NOTE]
> Gli account del tenant MSA (con ID tenant `9188040d-6c67-4c5b-b112-36a304b66dad` ) non restituiranno altre informazioni rispetto a quelle già raccolte con il token ID. È quindi possibile ignorare questa chiamata al API Graph per questi account.

## <a name="next-steps"></a>Passaggi successivi
- [Come creare un'offerta SaaS nel Marketplace commerciale](create-new-saas-offer.md)
