---
title: Informazioni sui concetti relativi al repository dei modelli di Azure Microsoft Docs
description: Per gli sviluppatori di soluzioni o i professionisti IT, è possibile ottenere informazioni sui concetti di base del repository di modelli di Azure.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715073"
---
# <a name="azure-iot-model-repository"></a>Repository del modello di Azure IoT

Il repository del modello di Azure IoT consente ai generatori di dispositivi di gestire e condividere i modelli di dispositivi Plug and Play IoT. I modelli di dispositivo sono documenti JSON LD definiti con il [linguaggio di modellazione digitale gemello (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). I modelli archiviati nel servizio repository di modelli possono essere condivisi con gli sviluppatori di soluzioni privatamente tramite il controllo di accesso o pubblicamente senza che sia necessaria alcuna autenticazione per l'integrazione e lo sviluppo della soluzione Plug and Play cloud.

> [!NOTE]
> I generatori di dispositivi possono scegliere di implementare i modelli di dispositivi Plug and Play direttamente in un dispositivo, usare i moduli o in un modulo di IoT Edge.

È possibile accedere al repository del modello usando:

- Portale del [repository del modello di Azure](https://aka.ms/iotmodelrepo)
- [API REST del repository di modelli di Azure](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Comandi del repository del modello dell'interfaccia della riga di comando Azure](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Modelli pubblici

I modelli di dispositivi gemelli digitali pubblici archiviati nel repository dei modelli sono disponibili per tutti gli utenti che possono utilizzare e integrare nella propria applicazione senza alcuna autenticazione. Inoltre, i modelli pubblici consentono a un eco-sistema aperto per i generatori di dispositivi e gli sviluppatori di soluzioni di condividere e riutilizzare i propri Plug and Play i modelli di dispositivo.

Per istruzioni su come pubblicare un modello nel repository del modello per renderlo pubblico, vedere la sezione [pubblicare un modello](#publish-a-model) in **modelli aziendali** .

Per visualizzare un modello pubblico tramite il portale del repository di modelli:

1. Passare al [portale del repository del modello di Azure](https://aka.ms/iotmodelrepo).

1. Selezionare **Visualizza modelli pubblici**.

    ![Visualizzare i modelli pubblici](./media/concepts-model-repository/public-models.png)

Per visualizzare un modello pubblico a livello di codice usando l'API REST, vedere ottenere la documentazione dell'API REST del [modello](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) .

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Per visualizzare un modello pubblico usando l'interfaccia della riga di comando, vedere l'interfaccia della riga di comando di Azure [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

## <a name="company-models"></a>Modelli aziendali

Il repository del modello aziendale è un tenant nel repository del modello di Azure per la propria organizzazione per la creazione e la gestione di modelli di dispositivi gemelli digitali creati dagli utenti all'interno dell'azienda o dell'organizzazione. I modelli aziendali sono disponibili solo per gli utenti autenticati della società o dell'organizzazione. Un amministratore tenant del repository di modelli può assegnare autorizzazioni e controllare l'accesso di altri utenti della società o dell'organizzazione ai modelli nel repository del modello aziendale.

### <a name="set-up-your-company-model-repository"></a>Configurare il repository del modello aziendale

Usare l' *account aziendale o dell'Istituto di istruzione Azure Active Directory (Azure ad)* per accedere al repository del modello. Se l'organizzazione dispone già di un tenant di Azure AD, è possibile usare gli account utente e le entità servizio di questo Azure AD tenant.

Per informazioni su come configurare un tenant di Azure AD e su come creare un utente o un'entità servizio in un tenant Azure AD, vedere la sezione [informazioni aggiuntive](#additional-information) .

- Se si è il primo utente dell'organizzazione ad accedere al repository del modello o ad accedere al portale, viene concesso il ruolo **amministratore tenant** . Questo ruolo consente di assegnare i ruoli ad altri utenti nel tenant del repository dell'organizzazione.

- È possibile assegnare altri ruoli a un **amministratore tenant** , ad esempio **leggere modelli** o **creare modelli**.

### <a name="understand-access-management"></a>Informazioni sulla gestione degli accessi

Nella tabella seguente sono riepilogate le funzionalità supportate nel repository del modello aziendale e le relative autorizzazioni associate:

| Funzionalità  | Autorizzazione| Descrizione|
|-------------|-----------|------------|
|Leggi modelli|Leggi modelli|Per impostazione predefinita, tutti gli utenti del tenant aziendale possono visualizzare i modelli aziendali. Inoltre, l'utente può visualizzare il modello o i modelli privati condivisi da altre società.|
|Gestire l'accesso|Gestire l'accesso|Gestire l'assegnazione di ruolo utente (aggiunta o rimozione) per altri utenti nell'organizzazione.|
|Creare modelli|Creare modelli|Creare modelli nel repository del modello aziendale.|
|Pubblicare modelli|Pubblicare modelli|Pubblicare i modelli per renderli pubblici per chiunque possa visualizzare il modello.|

Nella tabella seguente sono riepilogati i ruoli supportati e le rispettive funzionalità nell'archivio dei modelli che possono essere utilizzati per la gestione degli accessi.

|Ruolo|Funzionalità|
|----|----------|
|TenantAdministrator|Gestisci accesso, leggi modelli|
|Autore|Creare modelli, leggere modelli|
|Publisher|Pubblicare modelli, leggere modelli|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Passaggio di un token di sicurezza quando si accede ai modelli aziendali con un'API REST

Quando si chiamano le API REST per gestire i modelli aziendali privati o condivisi, è necessario fornire un token di autorizzazione per l'utente o l'entità servizio nel formato JWT. Vedere la sezione [informazioni aggiuntive per informazioni](#additional-information) su come ottenere un token JWT per un utente o un'entità servizio.

Il token JWT deve essere passato nell'intestazione HTTP Authorization nell'API quando la destinazione è costituita da modelli aziendali o modelli condivisi. Il token JWT non è necessario per i modelli pubblici destinati a.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Visualizzazione di modelli aziendali o condivisi

È necessario essere un membro del ruolo *lettore* del tenant del repository oppure il modello deve essere condiviso con l'utente per la lettura di un modello. È possibile visualizzare un elenco di modelli non pubblicati che sono stati condivisi con l'utente e un elenco di modelli pubblicati che sono stati condivisi con l'utente. Per impostazione predefinita, gli utenti possono leggere i modelli aziendali, i modelli che sono stati condivisi con loro da altre società e tutti i modelli pubblici.

Per visualizzare una società o un modello condiviso usando il portale:

1. Accedere al portale del [repository del modello di Azure](https://aka.ms/iotmodelrepo).

1. Espandi **modelli aziendali** -non **pubblicato** nel riquadro sinistro

    ![Visualizzare i modelli aziendali](./media/concepts-model-repository/view-company-models.png)

1. Espandi **modelli condivisi-non pubblicato** nel riquadro sinistro

    ![Visualizza modelli condivisi](./media/concepts-model-repository/view-shared-models.png)

Per visualizzare una società o un modello condiviso usando l'API REST, vedere la documentazione sull'API REST del [modello](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) . Vedere [passaggio di un token di sicurezza quando si accede ai modelli aziendali con un'API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) per informazioni sul passaggio di un'intestazione di autorizzazione JWT nella richiesta HTTP.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Per visualizzare un modello aziendale o un modello condiviso usando l'interfaccia della riga di comando, vedere l'interfaccia della riga di comando di Azure [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) .

### <a name="manage-roles"></a>Gestire i ruoli

L'amministratore tenant può assegnare ruoli agli utenti nel tenant del repository, in modo da poter creare modelli privati per la società o organizzazione, pubblicare modelli o gestire ruoli per altri utenti.

Per aggiungere un utente a un ruolo tenant del repository di modelli tramite il portale:

1. Accedere al portale del [repository del modello di Azure](https://aka.ms/iotmodelrepo).

1. Selezionare **gestione accessi** nel riquadro sinistro, quindi selezionare **+ Aggiungi**. Nel riquadro **Aggiungi autorizzazione** Digitare l'indirizzo di lavoro dell'utente che si desidera aggiungere al ruolo:

    ![Aggiungi indirizzo di lavoro](./media/concepts-model-repository/add-user.png)

1. Scegliere il ruolo a cui si vuole aggiungere l'utente dall'elenco a discesa **Role** . Quindi selezionare **Salva**:

    ![Scegli ruolo](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Caricare un modello

Per caricare un modello nel repository del modello aziendale, è necessario essere un membro del ruolo di **autore** del tenant del repository.

Questi modelli non sono pubblicati e sono accessibili solo agli utenti all'interno dell'organizzazione, per impostazione predefinita. È inoltre possibile condividere uno o più modelli non pubblicati con utenti esterni.

I modelli caricati non sono modificabili.

Gli ID modello per questi modelli devono essere univoci a livello globale in tutti i tenant del repository per tutti i modelli caricati.

Per caricare un modello usando il portale:

1. Accedere al portale del [repository del modello di Azure](https://aka.ms/iotmodelrepo).

1. Espandere **modelli aziendali** nel riquadro sinistro e selezionare **Crea modello**. Quindi selezionare **Import JSON**.

    ![Crea modello](./media/concepts-model-repository/create-model.png)

1. Selezionare il file che si desidera caricare. Se il modello viene convalidato correttamente dal portale, selezionare **Salva**.

Per caricare un modello usando l'API REST, vedere [creare un'](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API del modello. Vedere [passaggio di un token di sicurezza quando si accede ai modelli aziendali con un'API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) per informazioni sul passaggio di un'intestazione di autorizzazione JWT nella richiesta HTTP.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

Per caricare un modello usando l'interfaccia della riga di comando, vedere l'interfaccia della riga di comando di Azure [creare un modello](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) .

### <a name="publish-a-model"></a>Pubblicare un modello

Per pubblicare un modello, devono essere soddisfatti i requisiti seguenti:

1. Per pubblicare un modello, è necessario che l'organizzazione faccia parte di [Microsoft Partner Network](https://docs.microsoft.com/partner-center/). Per creare un account del Centro per i partner, vedere [Creare un account per il Centro per i partner](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). In seguito all'approvazione dell'account, è possibile pubblicare i modelli. Per altre informazioni, vedere [Domande frequenti sul Centro per i partner](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. L'utente deve essere un membro del ruolo *server di pubblicazione* del tenant del repository.

I modelli creati e pubblicati dagli utenti all'interno dell'organizzazione sono visibili come *modelli pubblicati*. Questi modelli sono pubblici e possono essere trovati da chiunque nei **modelli pubblici**.

Per pubblicare un modello usando il portale:

1. Accedere al portale del [repository del modello di Azure](https://aka.ms/iotmodelrepo).

2. Espandere **modelli aziendali** nel riquadro sinistro e selezionare il modello che si desidera pubblicare. Quindi selezionare **pubblica**.

    ![Pubblicare un modello](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Se si riceve una notifica in cui si informa che non si dispone di un ID Microsoft partner (MPN), seguire la procedura di registrazione nella notifica. Per ulteriori informazioni, vedere i requisiti all'inizio di questa sezione.

Per pubblicare un modello con l'API REST, vedere la documentazione sull'API REST di [pubblicazione di un modello](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) . Fornire il parametro della stringa `update-metadata=true` di query per pubblicare un modello usando l'API REST. Vedere [passaggio di un token di sicurezza quando si accede ai modelli aziendali con un'API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) per informazioni sul passaggio di un'intestazione di autorizzazione JWT nella richiesta HTTP.

Per pubblicare un modello usando l'interfaccia della riga di comando, vedere l'interfaccia della riga di comando di Azure [pubblicare un modello](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) .

> [!NOTE]
> Prima di eseguire i test di certificazione, è necessario pubblicare i modelli nel repository del modello. Per altre informazioni, vedere [come certificare i dispositivi Plug and Play](howto-certify-device.md).

### <a name="share-a-model"></a>Condividere un modello

È possibile condividere modelli aziendali creati con utenti di organizzazioni esterne. In questo modo, è possibile consentire ai collaboratori di visualizzare e sviluppare soluzioni con i modelli aziendali privati.

Ad esempio, un produttore di dispositivi potrebbe voler mantenere i modelli privati per l'azienda o l'organizzazione. Potrebbero avere clienti che richiedono che le funzionalità del dispositivo rimangano riservate.

La condivisione di modelli tra aziende o organizzazioni consente un accesso sicuro a modelli non pubblici.

Per condividere un modello aziendale usando il portale:

- Se si è l'autore di un modello, i pulsanti **Condividi** e **condiviso con** sono attivi quando si Visualizza il modello nella sezione **modelli aziendali** .

    ![Condividi modello](./media/concepts-model-repository/share-model.png)

- Per condividere il modello con un utente esterno, selezionare **Condividi**. Nel riquadro **Condividi modello** immettere l'indirizzo di posta elettronica dell'utente esterno e selezionare **Salva**.

- Per visualizzare gli utenti con cui è stato condiviso il modello, selezionare **condiviso con**.

- Per interrompere la condivisione del modello con un utente specifico, selezionare l'utente nell'elenco di utenti nel riquadro **condiviso con** . Quindi selezionare **Rimuovi** e confermare la scelta quando richiesto.

    ![Interrompere la condivisione](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Informazioni aggiuntive

Gli argomenti seguenti possono risultare utili quando si lavora con Azure AD:

- Per creare un nuovo tenant Azure AD, vedere [creare un nuovo tenant nel Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). La maggior parte delle organizzazioni avrà già Azure AD tenant.

- Per aggiungere utenti o utenti Guest a un tenant di Azure AD, vedere [aggiungere o eliminare utenti usando Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Per aggiungere un'entità servizio a un tenant di Azure AD, vedere [come usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Per informazioni su come ottenere un token JWT da Azure AD da usare quando si chiamano le API REST, vedere [acquisizione di un token da Azure ad per l'autorizzazione delle richieste da un'applicazione client](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nel rivedere l' [architettura plug and Play](concepts-architecture.md).
