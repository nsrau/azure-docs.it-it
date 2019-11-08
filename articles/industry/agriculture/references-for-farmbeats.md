---
title: Riferimenti per FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8e8e4524034f0a296045691309b065f8547bdaa0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797699"
---
# <a name="references"></a>Riferimenti

Di seguito sono riportate una raccolta di note e istruzioni che descrivono le API FarmBeats di Azure.

## <a name="rest-api"></a>API REST

Le API FarmBeats di Azure forniscono un'azienda agricola con un'interfaccia RESTful standardizzata con risposte basate su JSON, che ti aiuterà a sfruttare le funzionalità di Azure FarmBeats:

- API per ottenere sensori, fotocamera, drone, meteo, satellite e dati di base curati.
- Normalizzazione/contestualizzazione dei dati tra i provider di dati comuni.
- Schematizzato le funzionalità di accesso ed esecuzione di query su tutti i dati inseriti.
- Generazione automatica di metadati su cui è possibile eseguire query in base alle funzionalità agronomiche.  
- Funzioni di aggregazione della sequenza temporale generate automaticamente per la compilazione rapida del modello.
- Motore di Azure Data Factory integrato (ADF) per compilare facilmente pipeline di elaborazione dati personalizzate.

## <a name="application-development"></a>Sviluppo di applicazioni

Le API contengono la documentazione tecnica di spavalderia. Vedere [spavalderia](https://aka.ms/FarmBeatsDatahubSwagger) per informazioni su tutte le API e sulle richieste/risposte corrispondenti.

Questo è un riepilogo di tutti gli oggetti e le risorse nell'hub dati FarmBeats:

Farm | Farm corrisponde a una posizione fisica di interesse all'interno del sistema FarmBeats. Ogni farm dispone di un nome della farm e di un ID Farm univoco.
--- | ---|
Dispositivo  | Il dispositivo corrisponde a un dispositivo fisico presente nella farm. Ogni dispositivo ha un ID dispositivo univoco. Il provisioning del dispositivo viene in genere eseguito in una farm con un ID farm.
DeviceModel  | DeviceModel corrisponde ai metadati del dispositivo, ad esempio il produttore, il tipo del dispositivo gateway o node.
Sensore  | Il sensore corrisponde a un sensore fisico che registra i valori. Un sensore è in genere connesso a un dispositivo con un ID dispositivo.
SensorModel  | SensorModel corrisponde ai metadati del sensore, ad esempio il produttore, il tipo di sensore analogico o digitale, misura sensore come temperatura ambiente, pressione e così via.
Telemetria  | La telemetria fornisce la possibilità di leggere i messaggi di telemetria per un determinato sensore e intervallo di tempo.
Processo  | Il processo corrisponde a qualsiasi flusso di lavoro delle attività, che vengono eseguite nel sistema FarmBeats per ottenere un output desiderato. Ogni processo è associato a un ID processo e a un tipo di processo.
Tipoprocesso  | Tipoprocesso corrisponde a tipi di processi diversi supportati dal sistema. Sono inclusi i tipi di processo definiti dall'utente di sistema &.
ExtendedType  | ExtendedType corrisponde all'elenco dei tipi di sistema & definiti dall'utente nel sistema. Questo consente di configurare un nuovo tipo di sensore o scena o Scenefile nel sistema FarmBeats.
Partner  | Partner corrisponde al partner di integrazione Sensor/Imagery per FarmBeats
Scena  | La scena corrisponde a qualsiasi output generato nel contesto di una farm. Ogni scena ha un ID di scena, un'origine della scena, un tipo di scena e un ID Farm associati. A ogni ID di scena possono essere associati più file di scena.
SceneFile |SceneFile corrisponde a tutti i file, che vengono generati per una singola scena. A un singolo ID di scena possono essere associati più ID SceneFile.
Regola  |La regola corrisponde a una condizione per i dati correlati alla farm per attivare un avviso. Ogni regola sarà nel contesto dei dati di una farm.
Avviso  | Alert corrisponde a una notifica, che viene generata quando viene soddisfatta una condizione della regola. Ogni avviso si troverà nel contesto di una regola.
RoleDefinition  | RoleDefinition definisce le azioni consentite e non consentite per un ruolo.
RoleAssignment  |RoleAssignment corrisponde all'assegnazione di un ruolo a un utente o a un'entità servizio.

**Formato dati**

JSON (JavaScript Object Notation) è un formato di dati comune indipendente dal linguaggio che fornisce una semplice rappresentazione testuale di strutture di dati arbitrarie. Per ulteriori informazioni, vedere json.org.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Le richieste HTTP all'API REST sono protette con Azure Active Directory (Azure AD).
Per effettuare una richiesta autenticata alle API REST, il codice client richiede l'autenticazione con credenziali valide prima di poter chiamare l'API. L'autenticazione è coordinata tra i vari attori per Azure AD e fornisce al client un token di accesso come prova dell'autenticazione. Il token viene quindi inviato nell'intestazione di autorizzazione HTTP delle richieste dell'API REST. Per ulteriori informazioni sull'autenticazione Azure AD, vedere [Azure Active Directory](https://portal.azure.com) per gli sviluppatori.

Il token di accesso deve essere inviato nelle successive richieste API nella sezione di intestazione come:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**Intestazioni di richiesta HTTP**

Di seguito sono riportate le intestazioni di richiesta più comuni che è necessario specificare quando si effettua una chiamata API ad Azure FarmBeats Data Hub:


**Intestazione** | **Descrizione ed esempio**
--- | ---
Content-Type  | Il formato della richiesta (Content-Type: Application/<format>) per il formato API dell'hub dati di Azure FarmBeats è JSON. Content-Type: Application/JSON
Autorizzazione  | Specifica il token di accesso necessario per effettuare una chiamata API. **Autorizzazione: Bearer < Access-token >**
Accept | Formato della risposta. Per le API di Azure FarmBeats Data Hub il formato è JSON **Accept: Application/JSON**

**Richieste API**

Per eseguire una richiesta API REST, combinare il metodo HTTP (GET, POST, PUT o DELETE), l'URL del servizio API, l'URI di una risorsa per eseguire query, inviare dati a, aggiornare o eliminare e una o più intestazioni di richiesta HTTP.

L'URL del servizio API è l'URL dell'hub dati https://\<yourdatahub-website-name >. azurewebsites. NET, facoltativamente, è possibile includere i parametri di query sulle chiamate GET da filtrare, limitare le dimensioni e ordinare i dati nelle risposte.

La richiesta di esempio seguente consente di ottenere l'elenco dei dispositivi:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Per la maggior parte delle chiamate GET, POST e PUT è necessario un corpo della richiesta JSON.

La richiesta di esempio seguente consiste nel creare un dispositivo (con un JSON di input con il corpo della richiesta).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Parametri di query**

Per le chiamate REST **Get** è possibile filtrare, limitare le dimensioni e ordinare i dati in una risposta API includendo uno o più parametri di query nell'URI della richiesta. Per i parametri di query, fare riferimento alla documentazione dell'API e vedere le singole chiamate GET.
Ad esempio, quando si esegue una query sull'elenco di dispositivi (GET Call on/device), è possibile specificare i parametri di query seguenti:  

![Beat Farm progetto](./media/for-references/query-parameters-device-1.png)

**Gestione degli errori**

Le API di Azure FarmBeats Data Hub restituiscono gli errori HTTP standard. I codici di errore più comuni sono i seguenti:


 |Codice di errore             | Descrizione |
 |---                    | --- |
 |200                    | Success |
 |201                    | Creazione (post) riuscita |
 |400                    | Richiesta non valida. Si è verificato un errore nella richiesta |
 |401                    | Non autorizzato. Il chiamante dell'API non è autorizzato ad accedere alla risorsa |
 |404                    | Risorsa non trovata |
 |5XX                    | Errore interno del server. I codici di errore che iniziano con 5XX indicano che si è verificato un errore nel server. Per ulteriori informazioni, vedere i log del server e la sezione seguente. |


Oltre agli errori HTTP standard, le API dell'hub dati di Azure FarmBeats restituiscono anche errori interni nel formato seguente:

```
{
  "message": "<More information on the error>",
  "status": "<error code>”,
  "code": "<InternalErrorCode>",
  "moreInfo": "<Details of the error>"
}
```

Esempio: quando si crea una farm, nel payload di input non è stato specificato un campo obbligatorio "Name". Il messaggio di errore risultante sarà:

```
{
  "message": "Model validation failed",
  "status": 400,
  "code": "ModelValidationFailed",
  "moreInfo": "[\"The Name field is required.\"]"
}
```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Aggiunta di utenti o registrazioni di app a Azure Active Directory

 È possibile accedere alle API di Azure FarmBeats da un utente o da una registrazione dell'app nel Azure Active Directory. Per creare una registrazione dell'app nel Azure Active Directory, seguire questa procedura:  

1. Passare a [portale di Azure](https://portal.azure.com) **Azure Active Directory, registrazioni app**> **nuova registrazione**. In alternativa, è possibile usare un account esistente.
2. Per un nuovo account, assicurarsi di completare le operazioni seguenti:

    - Immettere un nome
    - Seleziona gli **account solo in questa directory organizzativa (tenant singolo)**
    - Valori predefiniti negli altri campi
    - Seleziona **Registro**

3. Dalla registrazione di app nuova/esistente, **Panoramica**, completare le operazioni seguenti

    - Acquisire l' **ID client** e l' **ID tenant**.
    - Passare a **certificati e segreti** per generare un nuovo segreto client e acquisire il **segreto client**.
    - Tornare a panoramica e fare clic sul collegamento accanto a **Gestisci applicazione nella directory locale**
    - Passare a **Proprietà** per acquisire l' **ID oggetto**

4. Passare a [spavalderia dell'hub dati](https://<yourdatahub>.azurewebsites.net/swagger/index.html) e seguire questa procedura:
    - Passa all' **API RoleAssignment**
    - Eseguire un **post** per creare un RoleAssignment per l' **ID oggetto** appena creato. : Il codice JSON di input è:

  > [!NOTE]
  > Per ulteriori informazioni sull'aggiunta di utenti e la registrazione AD, vedere [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

Dopo aver completato i passaggi precedenti, la registrazione dell'app (client) può chiamare le API di Azure FarmBeats usando un token di accesso tramite l'autenticazione della connessione.  

Usare il token di accesso per inviarlo nelle richieste API successive nella sezione dell'intestazione come:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
