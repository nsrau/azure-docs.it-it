---
title: Riferimenti
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479516"
---
# <a name="references"></a>Riferimenti

In questo articolo vengono descritte le API di Azure FarmBeats.This article describes the Azure FarmBeats APIs.

## <a name="rest-api"></a>API REST

Le API FarmBeats di Azure offrono alle aziende agricole un'interfaccia RESTful standardizzata con risposte basate su JSON che consentono di sfruttare le funzionalità di Azure FarmBeats, ad esempio:The Azure FarmBeats APIs provide agricultural businesses with a standardized RESTful interface with JSON-based responses to help you take advantage of Azure FarmBeats capabilities, such as:

- API per ottenere sensori, fotocamera, droni, meteo, satelliti e dati di terra curati.
- Normalizzazione e contestualizzazione dei dati tra provider di dati comuni.
- Funzionalità di accesso e query schematizzate su tutti i dati ingeriti.
- Generazione automatica di metadati su cui è possibile eseguire query, in base a funzionalità agronomiche.
- Aggregati della sequenza temporale generati automaticamente per la creazione rapida di modelli.
- Motore di Azure Data Factory integrato per creare facilmente pipeline di elaborazione dati personalizzate.

## <a name="application-development"></a>Sviluppo di applicazioni

Le API FarmBeats contengono la documentazione tecnica di Swagger. Per informazioni su tutte le API e sulle richieste o risposte corrispondenti, vedere [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

Nella tabella seguente vengono riepilogati tutti gli oggetti e le risorse in FarmBeats Datahub:

| Oggetti e risorse | Descrizione
--- | ---|
Farm | Farm corrisponde a una posizione fisica di interesse all'interno del sistema FarmBeats. Ogni farm ha un nome di farm e un ID farm univoco. |
Dispositivo  | Il dispositivo corrisponde a un dispositivo fisico presente nella farm. Ogni dispositivo ha un ID dispositivo univoco. Viene in genere eseguito il provisioning di un dispositivo in una farm con un ID farm.
DeviceModel  | DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero gateway o nodo.
Sensore  | Sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere collegato a un dispositivo con un ID dispositivo.
SensorEModello  | SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, analogico o digitale, e la misurazione del sensore, ad esempio la temperatura ambiente e la pressione.
Telemetria  | Telemetria offre la possibilità di leggere i messaggi di telemetria per un sensore e un intervallo di tempo specifici.
Processo  | Processo corrisponde a qualsiasi flusso di lavoro di attività eseguite nel sistema FarmBeats per ottenere l'output desiderato. Ogni processo è associato a un ID processo e a un tipo di processo.
JobType  | JobType corrisponde a diversi tipi di lavoro supportati dal sistema. Sono inclusi i tipi di processo definiti dal sistema e definiti dall'utente.
ExtendedType  | ExtendedType corrisponde all'elenco dei tipi definiti dall'utente e dal sistema nel sistema. ExtendedType consente di configurare un nuovo sensore, scene o tipo di file di scena nel sistema FarmBeats.
Partner  | Partner corrisponde al partner di integrazione di sensori e immagini per FarmBeats.
Scene  | La scena corrisponde a qualsiasi output generato nel contesto di una farm. A ogni scena sono associati un ID scena, un'origine della scena, un tipo di scena e un ID farm. A ogni ID scena possono essere associati più file di scena.
SceneFile |SceneFile corrisponde a tutti i file generati per una singola scena. A un singolo ID scena possono essere associati più ID SceneFile.
Regola  |La regola corrisponde a una condizione per i dati correlati alla farm per attivare un avviso. Ogni regola si trova nel contesto dei dati di una farm.
Avviso  | L'avviso corrisponde a una notifica, che viene generata quando viene soddisfatta una condizione della regola. Ogni avviso si trova nel contesto di una regola.
RoleDefinition  | RoleDefinition definisce le azioni consentite e non consentite per un ruolo.
RoleAssignment  |RoleAssignment corrisponde all'assegnazione di un ruolo a un utente o a un'entità servizio.

### <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che fornisce una rappresentazione testuale semplice di strutture di dati arbitrari. Per ulteriori informazioni, vedere il [sito Web JSON](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Le richieste HTTP all'API REST sono protette con Azure Active Directory (Azure AD).
Per effettuare una richiesta autenticata alle API REST, il codice client richiede l'autenticazione con credenziali valide prima di poter chiamare l'API. L'autenticazione è coordinata tra i vari attori da Azure AD. Fornisce al client un token di accesso come prova dell'autenticazione. Il token viene quindi inviato nell'intestazione di autorizzazione HTTP delle richieste API REST. Per altre informazioni sull'autenticazione di Azure AD, vedere [Azure Active Directory](https://portal.azure.com) per sviluppatori.

Il token di accesso deve essere inviato nelle richieste API successive, nella sezione dell'intestazione, come:The access token must be sent in subsequent API requests, in the header section, as:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Intestazioni di richiesta HTTP

Ecco le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API a Azure FarmBeats Datahub.Here are the most common request headers that you must specify when you make an API call to Azure FarmBeats Datahub.


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type  | Il formato della richiesta (Content-Type: application/<format>). Per le API Datahub di Azure FarmBeats, il formato è JSON. Content-Type: application/json
Autorizzazione  | Specifica il token di accesso necessario per effettuare una chiamata API. Autorizzazione:> T-token di connessione <BearerAuthorization: Bearer Access-Token>
Accept | Formato della risposta. Per le API Datahub di Azure FarmBeats, il formato è JSON. Accetta: applicazione/json

### <a name="api-requests"></a>Richieste API

Per effettuare una richiesta di API REST, combinare il metodo HTTP (GET, POST, PUT o DELETE), l'URL del servizio API, l'URI di una risorsa per eseguire una query, inviare dati a, aggiornare o eliminare e quindi aggiungere una o più intestazioni di richiesta HTTP.

L'URL del servizio API è l'URL\<di Datahub, ad esempio, https:// nome-sitoWeb-hub>.azurewebsites.net.The URL to the API service is your Datahub URL, for example, for youdatahub-website-name>.azurewebsites.net.

Facoltativamente, è possibile includere parametri di query nelle chiamate GET per filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente viene utilizzata per ottenere l'elenco dei dispositivi:The following sample request is used to get the list of devices:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

La maggior parte delle chiamate GET, POST e PUT richiede un corpo della richiesta JSON.

La richiesta di esempio seguente crea un dispositivo. Questa richiesta ha un JSON di input con il corpo della richiesta.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametri di query

Per le chiamate REST GET, è possibile filtrare, limitare le dimensioni e ordinare i dati in una risposta API includendo uno o più parametri di query nell'URI della richiesta. Per i parametri di query, vedere la documentazione dell'API e le singole chiamate GET.
Ad esempio, quando si esegue una query sull'elenco di dispositivi (chiamata GET su /Device), è possibile specificare i seguenti parametri di query:

![Elenco dei dispositivi](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Gestione degli errori

Le API Datahub di Azure FarmBeats restituiscono gli errori HTTP standard. I codici di errore più comuni sono i seguenti:

 |Codice di errore             | Descrizione |
 |---                    | --- |
 |200                    | Operazione completata |
 |201                    | Creazione (post) riuscita |
 |400                    | Richiesta non valida. Si è verificato un errore nella richiesta. |
 |401                    | Non autorizzato. Il chiamante dell'API non è autorizzato ad accedere alla risorsa. |
 |404                    | Risorsa non trovata |
 |5XX                    | Errore interno del server. I codici di errore che iniziano con 5XX indicano che si è verificato un errore nel server. Fare riferimento ai registri del server e alla sezione seguente per ulteriori dettagli. |


Oltre agli errori HTTP standard, anche le API Datahub di Azure FarmBeats restituiscono errori interni nel formato seguente:In addition to the standard HTTP errors, Azure FarmBeats Datahub APIs also return internal errors in the following format:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

In questo esempio, quando è stata creata una farm, il campo obbligatorio "Name" non è stato specificato nel payload di input. Il messaggio di errore risultante sarebbe:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Aggiungere utenti o registrazioni di app ad Azure Active DirectoryAdd users or app registrations to Azure Active Directory

Azure FarmBeats APIs can be accessed by a user or an app registration in Azure Active Directory. Per creare una registrazione dell'app in Azure Active Directory, attenersi alla seguente procedura:

1. Passare al [portale](https://portal.azure.com)di Azure e selezionare**Registrazioni** > app **di Azure Active Directory** > **Nuova registrazione**. In alternativa, è possibile utilizzare un account esistente.
2. Per un nuovo account, eseguire le operazioni seguenti:

    - Immettere un nome.
    - Selezionare **Solo account in questa directory dell'organizzazione (Tenant singolo)**.
    - Utilizzare i valori predefiniti negli altri campi.
    - Selezionare **Registra**.

3. Nel riquadro Panoramica registrazione app nuova ed esistente eseguire le operazioni seguenti:On the new and existing app registration **Pane,** do the following:

    - Acquisire **l'ID client** e **l'ID tenant**.
    - Passare a **Certificati e segreti** per generare un nuovo segreto client e acquisire il **segreto client**.
    - Tornare a **Panoramica**e selezionare il collegamento accanto a **Gestisci applicazione nella directory locale**.
    - Passare a **Proprietà** per acquisire l'ID **oggetto**.

4. Vai al tuo [Datahub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) ed esegui le operazioni seguenti:
    - Passare **all'API RoleAssignment**.
    - Eseguire un POST per creare un oggetto **RoleAssignment** per **l'ID oggetto** appena creato.

  > [!NOTE]
  > Per altre informazioni su come aggiungere utenti e sulla registrazione di Active Directory, vedere [Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Dopo aver completato i passaggi precedenti, la registrazione dell'app (client) può chiamare le API FarmBeats di Azure usando un token di accesso tramite l'autenticazione al portatore.

Usare il token di accesso per inviarlo nelle richieste API successive nella sezione dell'intestazione come:Use the access token to send it in subsequent API requests in the header section as:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
