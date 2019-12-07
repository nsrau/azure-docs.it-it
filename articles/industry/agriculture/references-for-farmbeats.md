---
title: Riferimenti per Azure FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8efc98ad2785a9052244556bddc60a5ba34bd3d8
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900385"
---
# <a name="references"></a>Riferimenti

Questo articolo descrive le API FarmBeats di Azure.

## <a name="rest-api"></a>API REST

Le API di Azure FarmBeats offrono alle aziende agricole un'interfaccia RESTful standardizzata con risposte basate su JSON che consentono di sfruttare le funzionalità di Azure FarmBeats, ad esempio:

- API per ottenere sensori, fotocamera, drone, meteo, satellite e dati di base curati.
- Normalizzazione e contestualizzazione dei dati tra i provider di dati comuni.
- Schematizzato le funzionalità di accesso ed esecuzione di query su tutti i dati inseriti.
- Generazione automatica di metadati su cui è possibile eseguire query in base alle funzionalità agronomiche. 
- Funzioni di aggregazione della sequenza temporale generate automaticamente per la compilazione rapida del modello.
- Motore Azure Data Factory integrato per creare facilmente pipeline di elaborazione dati personalizzate.

## <a name="application-development"></a>Sviluppo di applicazioni

Le API FarmBeats contengono la documentazione tecnica di spavalderia. Per informazioni su tutte le API e le relative richieste o risposte corrispondenti, vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger).

La tabella seguente riepiloga tutti gli oggetti e le risorse in FarmBeats datahub.

| Oggetti e risorse | Description
--- | ---|
Farm | Farm corrisponde a una posizione fisica di interesse all'interno del sistema FarmBeats. Ogni farm dispone di un nome della farm e di un ID Farm univoco. |
Dispositivo  | Il dispositivo corrisponde a un dispositivo fisico presente nella farm. Ogni dispositivo ha un ID dispositivo univoco. Viene in genere eseguito il provisioning di un dispositivo in una farm con un ID farm.
DeviceModel  | DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore e il tipo di dispositivo, ovvero gateway o nodo.
Sensore  | Il sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.
SensorModel  | SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore, che può essere analogico o digitale, e la misura del sensore, ad esempio la temperatura e la pressione dell'ambiente.
Telemetria  | La telemetria fornisce la possibilità di leggere i messaggi di telemetria per un determinato sensore e intervallo di tempo.
Processo  | Il processo corrisponde a qualsiasi flusso di lavoro delle attività eseguite nel sistema FarmBeats per ottenere un output desiderato. Ogni processo è associato a un ID processo e a un tipo di processo.
JobType  | Tipoprocesso corrisponde a tipi di processi diversi supportati dal sistema. Sono inclusi i tipi di processo definiti dall'utente e definiti dal sistema.
ExtendedType  | ExtendedType corrisponde all'elenco di tipi di sistema e definiti dall'utente nel sistema. ExtendedType consente di configurare un nuovo tipo di file di sensore, scena o scena nel sistema FarmBeats.
Partner  | Partner corrisponde al partner di integrazione di Sensor and Imagery per FarmBeats.
Scene  | La scena corrisponde a qualsiasi output generato nel contesto di una farm. Ogni scena ha un ID di scena, un'origine della scena, un tipo di scena e un ID farm associato. A ogni ID di scena possono essere associati più file di scena.
SceneFile |SceneFile corrisponde a tutti i file generati per una singola scena. A un singolo ID di scena possono essere associati più ID SceneFile.
Regola  |La regola corrisponde a una condizione per i dati correlati alla farm per attivare un avviso. Ogni regola si trova nel contesto dei dati di una farm.
Alert  | Alert corrisponde a una notifica, che viene generata quando viene soddisfatta una condizione della regola. Ogni avviso si trova nel contesto di una regola.
RoleDefinition  | RoleDefinition definisce le azioni consentite e non consentite per un ruolo.
RoleAssignment  |RoleAssignment corrisponde all'assegnazione di un ruolo a un utente o a un'entità servizio.

### <a name="data-format"></a>Formato dati

JSON è un formato di dati comune indipendente dal linguaggio che fornisce una semplice rappresentazione testuale di strutture di dati arbitrarie. Per ulteriori informazioni, vedere il [sito Web JSON](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Le richieste HTTP all'API REST sono protette con Azure Active Directory (Azure AD).
Per effettuare una richiesta autenticata alle API REST, il codice client richiede l'autenticazione con credenziali valide prima di poter chiamare l'API. L'autenticazione è coordinata tra i vari attori per Azure AD. Fornisce al client un token di accesso come prova dell'autenticazione. Il token viene quindi inviato nell'intestazione di autorizzazione HTTP delle richieste dell'API REST. Per ulteriori informazioni sull'autenticazione Azure AD, vedere [Azure Active Directory](https://portal.azure.com) per gli sviluppatori.

Il token di accesso deve essere inviato nelle successive richieste API, nella sezione header, come segue:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Intestazioni di richiesta HTTP

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API ad Azure FarmBeats datahub.


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type  | Formato della richiesta (Content-Type: Application/<format>). Per le API datahub di Azure FarmBeats, il formato è JSON. Content-Type: application/json
Authorization  | Specifica il token di accesso necessario per effettuare una chiamata API. Autorizzazione: Bearer < Access-token >
Accept | Formato della risposta. Per le API datahub di Azure FarmBeats, il formato è JSON. Accept: Application/JSON

### <a name="api-requests"></a>Richieste API

Per eseguire una richiesta API REST, combinare il metodo HTTP (GET, POST, PUT o DELETE), l'URL del servizio API, l'URI di una risorsa per eseguire query, inviare dati a, aggiornare o eliminare e quindi aggiungere una o più intestazioni di richiesta HTTP.

L'URL del servizio API è l'URL di datahub, ad esempio, https://\<yourdatahub-website-name >. azurewebsites. NET.
Facoltativamente, è possibile includere i parametri di query sulle chiamate GET da filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

Per ottenere l'elenco dei dispositivi, viene usata la richiesta di esempio seguente:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Per la maggior parte delle chiamate GET, POST e PUT è necessario un corpo della richiesta JSON.

La richiesta di esempio seguente crea un dispositivo. Questa richiesta ha input JSON con il corpo della richiesta.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametri della query

Per le chiamate REST GET è possibile filtrare, limitare le dimensioni e ordinare i dati in una risposta API includendo uno o più parametri di query nell'URI della richiesta. Per i parametri di query, vedere la documentazione dell'API e le singole chiamate GET.
Ad esempio, quando si esegue una query sull'elenco di dispositivi (GET Call on/device), è possibile specificare i parametri di query seguenti: 

![Elenco di dispositivi](./media/for-references/query-parameters-device-1.png)

### <a name="error-handling"></a>Gestione degli errori

Le API datahub di Azure FarmBeats restituiscono gli errori HTTP standard. I codici di errore più comuni sono i seguenti:

 |Codice di errore             | Description |
 |---                    | --- |
 |200                    | Success |
 |201                    | Creazione (post) riuscita |
 |400                    | Richiesta non valida. Si è verificato un errore nella richiesta. |
 |401                    | Non autorizzato. Il chiamante dell'API non è autorizzato ad accedere alla risorsa. |
 |404                    | Risorsa non trovata |
 |5XX                    | Errore interno del server. I codici di errore che iniziano con 5XX indicano che si è verificato un errore nel server. Per ulteriori informazioni, vedere i log del server e la sezione seguente. |


Oltre agli errori HTTP standard, le API datahub di Azure FarmBeats restituiscono anche errori interni nel formato seguente:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

In questo esempio, quando è stata creata una farm, il campo obbligatorio "Name" non è stato specificato nel payload di input. Il messaggio di errore risultante sarà:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Aggiungere utenti o registrazioni di app a Azure Active Directory

È possibile accedere alle API di Azure FarmBeats da un utente o da una registrazione dell'app in Azure Active Directory. Per creare una registrazione dell'app in Azure Active Directory, seguire questa procedura.

1. Passare alla [portale di Azure](https://portal.azure.com)e selezionare **Azure Active Directory** > **registrazioni app** > **nuova registrazione**. In alternativa, è possibile usare un account esistente.
2. Per un nuovo account, procedere come segue:

    - Immettere un nome.
    - Selezionare gli **account solo in questa directory organizzativa (tenant singolo)** .
    - Usare i valori predefiniti negli altri campi.
    - Selezionare **Registra**.

3. Nel riquadro della **Panoramica** della registrazione dell'app nuovo ed esistente eseguire le operazioni seguenti:

    - Acquisire l' **ID client** e l' **ID tenant**.
    - Passare a **certificati e segreti** per generare un nuovo segreto client e acquisire il **segreto client**.
    - Tornare a **Panoramica**e selezionare il collegamento accanto a **Gestisci applicazione nella directory locale**.
    - Passare a **Proprietà** per acquisire l' **ID oggetto**.

4. Passare a [datahub spavalderia](https://<yourdatahub>.azurewebsites.net/swagger/index.html) ed eseguire le operazioni seguenti:
    - Passare all' **API RoleAssignment**.
    - Eseguire un POST per creare un oggetto **RoleAssignment** per l' **ID oggetto** appena creato. 

  > [!NOTE]
  > Per ulteriori informazioni su come aggiungere utenti e Active Directory la registrazione, vedere [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Dopo aver completato i passaggi precedenti, la registrazione dell'app (client) può chiamare le API di Azure FarmBeats usando un token di accesso tramite l'autenticazione della connessione. 

Usare il token di accesso per inviarlo nelle richieste API successive nella sezione dell'intestazione come:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
