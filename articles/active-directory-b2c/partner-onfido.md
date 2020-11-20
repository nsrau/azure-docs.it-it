---
title: Esercitazione per la configurazione di Azure Active Directory B2C con onfido
titleSuffix: Azure AD B2C
description: Informazioni su come integrare Azure AD B2C autenticazione con onfido per la verifica dell'ID documento e della biometria facciale
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 48fc8533ee1fd206e69e16d4c03e4b4acf047135
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953688"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Esercitazione per la configurazione di onfido con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite informazioni aggiuntive su come integrare Azure AD B2C con [onfido](https://onfido.com/). Onfido è un ID documento e un'app di verifica biometria facciale. Consente alle aziende di soddisfare *i* requisiti dei clienti e delle identità in tempo reale. Onfido usa una sofisticata verifica dell'identità basata sull'intelligenza artificiale, che verifica prima di tutto un ID foto, quindi la associa alla biometria facciale. Questa soluzione associa un'identità digitale alla propria persona reale e fornisce un'esperienza di onboarding sicura riducendo al tempo stesso la frode.

In questo esempio viene connesso il servizio onfido nel flusso di iscrizione o di accesso per eseguire la verifica dell'identità. Decisioni informate sul prodotto e il servizio a cui l'utente può accedere viene eseguito in base ai risultati di onfido.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Un tenant Azure ad B2C](./tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

- Un account di [prova](https://onfido.com/signup/)onfido.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione onfido include i componenti seguenti:

- Tenant di Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente in base ai criteri personalizzati definiti nel tenant. È noto anche come provider di identità. Ospita l'app client onfido, che raccoglie i documenti utente e li trasmette al servizio API onfido.

- Client onfido: un'utilità di raccolta documenti client JavaScript configurabile distribuita in altre pagine Web. Raccoglie i documenti ed esegue controlli preliminari come la dimensione e la qualità del documento.

- API REST intermedia: fornisce gli endpoint per il tenant di Azure AD B2C per comunicare con il servizio API onfido, gestendo l'elaborazione dei dati e rispettando i requisiti di sicurezza di entrambi.

- Servizio API onfido: servizio back-end fornito da onfido, che consente di salvare e verificare i documenti forniti dall'utente.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![screenshot per onfido-Architecture-diagram](media/partner-onfido/onfido-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. L'utente effettua l'iscrizione per creare un nuovo account e immette le informazioni nella pagina. Azure AD B2C raccoglie gli attributi utente. L'app client onfido ospitata in Azure AD B2C esegue verifiche preliminari per le informazioni utente.
| 2. | Azure AD B2C chiama l'API del livello intermedio e passa gli attributi utente.
| 3. | L'API del livello intermedio raccoglie gli attributi utente e li trasforma in un formato utilizzabile dall'API onfido. Quindi, lo invia a onfido.
| 4. | Onfido utilizza le informazioni e le elabora per convalidare l'identificazione dell'utente. Restituisce quindi il risultato all'API del livello intermedio.
| 5. | L'API del livello intermedio elabora le informazioni e restituisce le informazioni rilevanti nel formato JSON corretto per Azure AD B2C.
| 6. | Azure AD B2C riceve informazioni dall'API del livello intermedio. Se viene visualizzata una risposta di errore, viene visualizzato un messaggio di errore all'utente. Se viene visualizzata una risposta con esito positivo, l'utente viene autenticato e scritto nella directory.

## <a name="onboard-with-onfido"></a>Onboarding con onfido

1. Per creare un account onfido, contattare [onfido](https://onfido.com/signup/).

2. Una volta creato un account, creare una [chiave API](https://documentation.onfido.com/). Le chiavi attive sono fatturabili, tuttavia, è possibile usare le [chiavi sandbox per il test](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) della soluzione. Le chiavi sandbox producono la stessa struttura dei risultati delle chiavi attive, ma i risultati sono sempre predeterminati. I documenti non vengono elaborati o salvati.

>[!NOTE]
> La chiave sarà necessaria in un secondo momento.

Per altre informazioni su onfido, vedere la [documentazione dell'API onfido](https://documentation.onfido.com) e l' [hub per sviluppatori onfido](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Configurare Azure AD B2C con onfido

### <a name="part-1---deploy-the-api"></a>Parte 1: distribuire l'API

- Distribuire il [codice API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) fornito a un servizio di Azure. Il codice può essere pubblicato da Visual Studio, seguendo queste [istruzioni](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).
- Configurare CORS, aggiungere l' **origine consentita** come https:///{your_tenant_name}. b2clogin. com

>[!NOTE]
>Per configurare Azure AD con le impostazioni necessarie, è necessario l'URL del servizio distribuito.

#### <a name="adding-sensitive-configuration-settings"></a>Aggiunta di impostazioni di configurazione riservate

Le impostazioni dell'applicazione possono essere configurate nel [servizio app in Azure](../app-service/configure-common.md#configure-app-settings). Il servizio app consente di configurare in modo sicuro le impostazioni senza archiviarle in un repository. Per l'API REST sono necessarie le impostazioni seguenti:

| Nome dell'impostazione dell'applicazione | Source (Sorgente) | Note |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Account onfido |

### <a name="part-2---deploy-the-ui"></a>Parte 2: distribuire l'interfaccia utente

#### <a name="configure-your-storage-location"></a>Configurare il percorso di archiviazione

1. Configurare un [contenitore di archiviazione BLOB nell'account di archiviazione](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Archiviare i file dell'interfaccia utente dalla [cartella dell'interfaccia utente](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) al contenitore BLOB.

3. Consentire l'accesso CORS al contenitore di archiviazione creato seguendo queste istruzioni:

   a. Passare a **Settings**  > **allowed Origin**, immettere `https://{your_tenant_name}.b2clogin.com` . Sostituire-tenant-Name con il nome del tenant Azure AD B2C. Ad esempio, https://fabrikam.b2clogin.com . Usare tutte le lettere minuscole quando si immette il nome del tenant.

   b. Per i **metodi consentiti**, selezionare `GET` e `PUT` .

   c. Selezionare **Salva**.

#### <a name="update-ui-files"></a>Aggiornare i file dell'interfaccia utente

1. Nei file dell'interfaccia utente passare alla cartella [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Aprire ogni file HTML.

3. Trovare e sostituire {your-UI-BLOB-container-URL} con l'URL in cui si trovano le cartelle dell'interfaccia utente **ocean_blue**, **dist** e **assets**

4. Trovare e sostituire {your-Intermediate-API-URL} con l'URL del servizio app per le API intermedio.

#### <a name="upload-your-files"></a>Caricare i file

1. Archiviare i file dell'interfaccia utente dalla cartella dell'interfaccia utente al contenitore BLOB.

2. Usare [Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) per gestire i file e le autorizzazioni di accesso.

### <a name="part-3---configure-azure-ad-b2c"></a>Parte 3: configurare Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Sostituire i valori di configurazione

Nei [criteri personalizzati](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)forniti, trovare i segnaposto seguenti e sostituire con i valori corrispondenti dell'istanza.

| Segnaposto | Replace with value | Esempio  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Nome breve del tenant |  "tenantinuso" da yourtenant.onmicrosoft.com |
| {your_tenantID} | TenantID del tenant di Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID app dell'app IdentityExperienceFramework configurata nel tenant di Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID app dell'app ProxyIdentityExperienceFramework configurata nel tenant di Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID app dell'applicazione di archiviazione del tenant                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID oggetto dell'applicazione di archiviazione del tenant                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_app_insights_instrumentation_key} | Chiave di strumentazione dell'istanza di Application Insights *| 01234567-89ab-cdef-0123-456789ABCDEF|
|{your_ui_file_base_url}| URL della posizione in cui si trovano le cartelle dell'interfaccia utente **ocean_blue**, **dist** e **assets** | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL del servizio app configurato                                             | `https://yourapp.azurewebsites.net`          |

* App Insights può trovarsi in un tenant diverso. Questo passaggio è facoltativo. Se non è necessario, rimuovere i elementi technicalprofile e OrchestrationSteps corrispondenti.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4: configurare i criteri di Azure AD B2C

Fare riferimento a questo [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) per istruzioni su come configurare il tenant di Azure ad B2C e configurare i criteri.

>[!NOTE]
> Come procedura consigliata, è consigliabile che i clienti aggiungano una notifica di consenso nella pagina della raccolta di attributi. Notificare agli utenti che le informazioni verranno inviate a servizi di terze parti per la verifica dell'identità.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare **Framework esperienza di identità**.

2. Selezionare il **SignUpSignIn** creato in precedenza.

3. Selezionare **Esegui flusso utente** e selezionare le impostazioni:

   a. **Applicazione**: selezionare l'app registrata (l'esempio è JWT)

   b. **URL di risposta**: selezionare l' **URL di reindirizzamento**

   c. Selezionare **Esegui il flusso utente**.

4. Esaminare il flusso di iscrizione e creare un account

5. Il servizio onfido verrà chiamato durante il flusso, dopo la creazione dell'attributo utente. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)