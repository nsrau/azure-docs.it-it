---
title: Creare un'immagine Docker per Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento descrive la procedura per creare un'immagine Docker per un servizio Web.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 4c4391cecaf10428b5d4cacf3b39e6a08d417053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Riferimento per l'API di account di Gestione modelli di Azure Machine Learning

Per informazioni sulla configurazione dell'ambiente di distribuzione, vedere [Model Management Account Setup](model-management-configuration.md) (Impostazione dell'account di Gestione modelli).

L'API di Gestione modelli di Azure Machine Learning implementa le operazioni seguenti:

- Registrazione del modello
- Creazione del manifesto
- Creazione di un'immagine Docker
- Creazione di un servizio Web

È possibile usare questa immagine per creare un servizio Web in locale o in un cluster ACS remoto o un altro ambiente supportato da Docker di propria scelta.

## <a name="prerequisites"></a>Prerequisiti
Verificare di avere completato la procedura di installazione disponibile nel documento [Install and create Quickstart](quickstart-installation.md) (Avvio rapido per installare e creare).

Prima di procedere, è necessario ottenere quanto segue:
1. Provisioning di un account di Gestione modelli
2. Creazione dell'ambiente per la distribuzione e la gestione dei modelli
3. Modello di Machine Learning

### <a name="aad-token"></a>Token AAD
Quando si usa l'interfaccia CLI, accedere con `az login`. L'interfaccia CLI usa il token AAD dal file con estensione azure. Se si desidera usare le API, è possibile:

##### <a name="acquiring-the-aad-token-manually"></a>Acquisire il token AAD manualmente
È possibile eseguire il comando `az login` e ottenere la versione più recente del token dal file con estensione azure che si trova nella home directory.

##### <a name="acquiring-the-aad-token-programmatically"></a>Acquisire il token AAD a livello di codice
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Dopo avere creato l'entità servizio, salvare l'output. È possibile a quel punto usarlo per ottenere il token da AAD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Il token viene inserito nell'intestazione di autorizzazione per le chiamate API. Vedere di seguito per altri dettagli.


## <a name="register-model"></a>Registrare un modello

Il passaggio della registrazione del modello registra il modello di Machine Learning con l'account di Gestione modelli di Azure creato. Questa registrazione consente di tenere traccia dei modelli e delle relative versioni che vengono assegnati al momento della registrazione. L'utente specifica il nome del modello. Se successivamente vengono registrati modelli con lo stesso nome, vengono generati una nuova versione e un nuovo ID.

### <a name="request"></a>Richiesta

| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrizione
Registrare un modello

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| model | body | Payload usato per registrare un modello | Sì | [Modello](#model) |


### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | OK. Registrazione del modello riuscita | [Modello](#model) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Eseguire una query sull'elenco di modelli in un account
### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrizione
Eseguire una query sull'elenco di modelli in un account. L'elenco dei risultati può essere filtrato in base al tag e al nome. Se non viene passato alcun filtro, la query elenca tutti i modelli presenti nell'account specificato. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| name | query | Nome dell'oggetto | No | string |
| tag | query | Tag del modello | No | string |
| count | query | Numero di voci da visualizzare in una pagina | No | string |
| $skipToken | Token di continuazione per recuperare la pagina successiva | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Success | [PaginatedModelList](#paginatedmodellist) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Ottenere i dettagli del modello
### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Descrizione
Ottenere il modello in base all'ID

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'oggetto | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Success | [Modello](#model) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrare un manifesto con il modello registrato e tutte le dipendenze

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrizione
Registrare un manifesto con il modello registrato e tutte le dipendenze

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| manifestRequest | body | Payload usato per registrare un manifesto | Sì | [Manifesto](#manifest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | La registrazione del manifesto ha avuto esito positivo | [Manifesto](#manifest) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Eseguire una query sull'elenco di manifesti in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrizione
Eseguire una query sull'elenco di manifesti in un account. L'elenco dei risultati può essere filtrato in base all'ID del modello e al nome del manifesto. Se non viene passato alcun filtro, la query elenca tutti i manifesti presenti nell'account specificato. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| modelId | query | ID modello | No | string |
| manifestName | query | Nome del manifesto | No | string |
| count | query | Numero di voci da visualizzare in una pagina | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Ottenere i dettagli del manifesto

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Descrizione
Ottiene il manifesto in base all'ID

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'oggetto | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [Manifesto](#manifest) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Creare un'immagine

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrizione
Creare un'immagine come immagine Docker nel record di controllo di accesso

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| imageRequest | body | Payload usato per creare un'immagine | Sì | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di creazione dell'immagine. | Operation-Location |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Eseguire una query sull'elenco di immagini in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrizione
Eseguire una query sull'elenco di immagini in un account. L'elenco dei risultati può essere filtrato in base all'ID e al nome del manifesto. Se non viene passato alcun filtro, la query elenca tutte le immagini presenti nell'account specificato. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| manifestId | query | ID del manifesto | No | string |
| manifestName | query | Nome del manifesto | No | string |
| count | query | Numero di voci da visualizzare in una pagina | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [PaginatedImageList](#paginatedimagelist) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Ottenere i dettagli dell'immagine

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Descrizione
Ottiene l'immagine in base all'ID

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'immagine | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [Immagine](#image) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Creare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrizione
Creare un servizio da un'immagine

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| serviceRequest | body | Payload usato per creare un servizio | Sì | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di creazione del servizio. | Operation-Location |
| 409 | Un servizio con il nome specificato esiste già. |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Eseguire una query sull'elenco di servizi in un account.

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrizione
Eseguire una query sull'elenco di servizi in un account. L'elenco dei risultati può essere filtrato in base a nome/ID modello, nome/ID manifesto, ID immagine, nome servizio o ID risorsa di calcolo di Machine Learning. Se non viene passato alcun filtro, la query elenca tutti i servizi presenti nell'account. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| serviceName | query | Nome del servizio | No | string |
| modelId | query | Nome del modello | No | string |
| modelName | query | ID modello | No | string |
| manifestId | query | ID del manifesto | No | string |
| manifestName | query | Nome del manifesto | No | string |
| imageId | query | ID dell'immagine | No | string |
| computeResourceId | query | ID della risorsa di calcolo di Machine Learning | No | string |
| count | query | Numero di voci da visualizzare in una pagina | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [PaginatedServiceList](#paginatedservicelist) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Ottenere i dettagli del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Ottiene il servizio in base all'ID

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'oggetto | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [ServiceResponse](#serviceresponse) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Aggiornare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Aggiornare un servizio esistente

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'oggetto | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| serviceUpdateRequest | body | Payload che viene usato per aggiornare un servizio esistente | Sì |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di aggiornamento del servizio. | Operation-Location |
| 404 | Il servizio con il nome specificato non esiste. |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Eliminare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Elimina un servizio

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID dell'oggetto | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo |  |
| 204 | Il servizio con il nome specificato non esiste. |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Ottenere le chiavi del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrizione
Ottiene le chiavi del servizio

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID del servizio | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [AuthKeys](#authkeys)
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Rigenerare le chiavi del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrizione
Rigenera le chiavi del servizio e le restituisce

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID del servizio | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| regenerateKeyRequest | body | Payload che viene usato per aggiornare un servizio esistente | Sì | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [AuthKeys](#authkeys)
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Eseguire una query sull'elenco di distribuzioni in un account.

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Descrizione
Eseguire una query sull'elenco di distribuzioni in un account. L'elenco dei risultati può essere filtrato in base all'ID di servizio, che restituirà solo le distribuzioni create per il servizio specifico. Se non viene passato alcun filtro, la query elenca tutte le distribuzioni presenti nell'account specificato.

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |
| serviceId | query | ID del servizio | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [DeploymentList](#deploymentlist) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Ottenere i dettagli della distribuzione

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Descrizione
Ottiene la distribuzione in base all'ID

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID della distribuzione | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [Distribuzione](#deployment) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Ottenere i dettagli dell'operazione

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Descrizione
Ottenere lo stato dell'operazione asincrona in base all'ID operazione

### <a name="parameters"></a>parameters
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli | Sì | string |
| id | path | ID operazione | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione, deve essere simile a "Bearer XXXXXX" | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | esito positivo | [OperationStatus](#asyncoperationstatus) |
| default | Risposta con errore che descrive il motivo per cui l'operazione non è riuscita | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definizioni

<a name="asset"></a>
### <a name="asset"></a>Asset
Oggetto asset che sarà necessario durante la creazione dell'immagine Docker


|Nome|Descrizione|Schema|
|---|---|---|
|**id**  <br>*facoltativo*|ID asset|string|
|**mimeType**  <br>*facoltativo*|Tipo MIME del contenuto del modello. Per altre informazioni sul tipo MIME, visitare la pagina https://www.iana.org/assignments/media-types/media-types.xhtml|string|
|**unpack**  <br>*facoltativo*|Indica se è necessario decomprimere il contenuto durante la creazione di un'immagine Docker.|boolean|
|**url**  <br>*facoltativo*|URL di posizione dell'asset|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Stato dell'operazione asincrona

*Tipo* : enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Stato dell'operazione


|Nome|Descrizione|Schema|
|---|---|---|
|**createdTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione dell'operazione asincrona (UTC)|string (date-time)|
|**endTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di fine dell'operazione asincrona (UTC)|string (date-time)|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facoltativo*|ID dell'operazione asincrona|string|
|**operationType**  <br>*facoltativo*|Tipo dell'operazione asincrona|enum (immagine, servizio)|
|**resourceLocation**  <br>*facoltativo*|Risorsa creata/aggiornata dall'operazione asincrona|string|
|**state**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Chiavi di autenticazione per un servizio


|Nome|Descrizione|Schema|
|---|---|---|
|**primaryKey**  <br>*facoltativo*|Chiave primaria|string|
|**secondaryKey**  <br>*facoltativo*|Chiave secondaria|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Impostazioni per il ridimensionamento automatico


|Nome|Descrizione|Schema|
|---|---|---|
|**autoscaleEnabled**  <br>*facoltativo*|Abilitare o disabilitare il ridimensionamento automatico|boolean|
|**maxReplicas**  <br>*facoltativo*|Numero massimo di repliche pod fino a cui scalare.  <br>**Valore minimo**: `1`|numero intero|
|**minReplicas**  <br>*facoltativo*|Numero minimo di repliche pod a cui scalare.  <br>**Valore minimo**: `0`|numero intero|
|**refreshPeriodInSeconds**  <br>*facoltativo*|Ora di aggiornamento per il trigger del ridimensionamento automatico.  <br>**Valore minimo**: `1`|numero intero|
|**targetUtilization**  <br>*facoltativo*|Percentuale di utilizzo a cui viene attivato il ridimensionamento automatico.  <br>**Valore minimo**: `0`  <br>**Valore massimo**: `100`|numero intero|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Risorsa di calcolo di Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**id**  <br>*facoltativo*|ID risorsa|string|
|**type**  <br>*facoltativo*|Tipo di risorsa|enum (cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configurazione per riservare risorse per il contenitore nel cluster


|Nome|Descrizione|Schema|
|---|---|---|
|**cpu**  <br>*facoltativo*|Specifica di prenotazione della CPU. Formato per Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu|string|
|**memory**  <br>*facoltativo*|Specifica di prenotazione della memoria. Formato per Kubernetes: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory|string|


<a name="deployment"></a>
### <a name="deployment"></a>Distribuzione
Istanza di una distribuzione di Azure Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione della distribuzione (UTC)|string (date-time)|
|**expiredAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di scadenza della distribuzione (UTC)|string (date-time)|
|**id**  <br>*facoltativo*|ID della distribuzione|string|
|**imageId**  <br>*facoltativo*|ID dell'immagine associata a questa distribuzione|string|
|**serviceName**  <br>*facoltativo*|Nome del servizio|string|
|**Stato**  <br>*facoltativo*|Stato attuale della distribuzione|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Matrice di oggetti di distribuzione.

*Type* : < [Deployment](#deployment) > array


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Dettagli dell'errore del servizio Gestione modelli.


|Nome|Descrizione|Schema|
|---|---|---|
|**code**  <br>*obbligatorio*|codice di errore|string|
|**message**  <br>*obbligatorio*|messaggio di errore|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Oggetto errore del servizio Gestione modelli.


|Nome|Descrizione|Schema|
|---|---|---|
|**code**  <br>*obbligatorio*|codice di errore|string|
|**details**  <br>*facoltativo*|Matrice di oggetti dettagli errore.|< [ErrorDetail](#errordetail) > array|
|**message**  <br>*obbligatorio*|Messaggio di errore|string|
|**statusCode**  <br>*facoltativo*|Stato codice HTTP|numero intero|


<a name="image"></a>
### <a name="image"></a>Image
Immagine di Azure Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**computeResourceId**  <br>*facoltativo*|ID dell'ambiente creato nel calcolo di Machine Learning|string|
|**createdTime**  <br>*facoltativo*|Ora di creazione dell'immagine (UTC)|string (date-time)|
|**creationState**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*facoltativo*|Testo descrittivo dell'immagine|string|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facoltativo*|ID dell'immagine|string|
|**imageBuildLogUri**  <br>*facoltativo*|URI dei log caricati dalla build dell'immagine|string|
|**imageLocation**  <br>*facoltativo*|Stringa di posizione del Registro contenitori di Azure per l'immagine creata|string|
|**imageType**  <br>*facoltativo*||[ImageType](#imagetype)|
|**manifest**  <br>*facoltativo*||[Manifesto](#manifest)|
|**nome**  <br>*facoltativo*|Nome dell'immagine|string|
|**version**  <br>*facoltativo*|Versione dell'immagine impostata dal servizio Gestione modelli|numero intero|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Richiesta di creazione di un'immagine di Azure Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**computeResourceId**  <br>*obbligatorio*|ID dell'ambiente creato nel calcolo di Machine Learning|string|
|**description**  <br>*facoltativo*|Testo descrittivo dell'immagine|string|
|**imageType**  <br>*obbligatorio*||[ImageType](#imagetype)|
|**manifestId**  <br>*obbligatorio*|ID del manifesto da cui verrà creata l'immagine|string|
|**nome**  <br>*obbligatorio*|Nome dell'immagine|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Specifica il tipo dell'immagine

*Type* : enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifesto
Manifesto di Azure Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**assets**  <br>*obbligatorio*|Elenco di asset|< [Asset](#asset) > array|
|**createdTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione del manifesto (UTC)|string (date-time)|
|**description**  <br>*facoltativo*|Testo descrittivo del manifesto|string|
|**driverProgram**  <br>*obbligatorio*|Programma driver del manifesto|string|
|**id**  <br>*facoltativo*|ID del manifesto|string|
|**modelIds**  <br>*facoltativo*|Elenco di ID modello dei modelli registrati. La richiesta avrà esito negativo se uno qualsiasi dei modelli inclusi non è registrato.|< string > array|
|**modelType**  <br>*facoltativo*|Specifica che i modelli sono già registrati con il servizio Gestione modelli|enum (Registered)|
|**nome**  <br>*obbligatorio*|Nome del manifesto|string|
|**targetRuntime**  <br>*obbligatorio*||[TargetRuntime](#targetruntime)|
|**version**  <br>*facoltativo*  <br>*sola lettura*|Versione del manifesto assegnata dal servizio Gestione modelli|numero intero|
|**webserviceType**  <br>*facoltativo*|Specifica il tipo desiderato di servizio Web che verrà creato dal manifesto|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Modello
Istanza di un modello di Azure Machine Learning


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione del modello (UTC)|string (date-time)|
|**description**  <br>*facoltativo*|Testo descrittivo del modello|string|
|**id**  <br>*facoltativo*  <br>*sola lettura*|ID modello|string|
|**mimeType**  <br>*obbligatorio*|Tipo MIME del contenuto del modello. Per altre informazioni sul tipo MIME, visitare la pagina https://www.iana.org/assignments/media-types/media-types.xhtml|string|
|**nome**  <br>*obbligatorio*|Nome del modello|string|
|**tag**  <br>*facoltativo*|Elenco di tag di modello|< string > array|
|**unpack**  <br>*facoltativo*|Indica se è necessario decomprimere il modello durante la creazione di un'immagine Docker.|boolean|
|**url**  <br>*obbligatorio*|URL del modello. In genere qui viene inserito un URL SAS.|string|
|**version**  <br>*facoltativo*  <br>*sola lettura*|Versione del modello assegnata dal servizio Gestione modelli|numero intero|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informazioni sulla raccolta di dati del modello


|Nome|Descrizione|Schema|
|---|---|---|
|**eventHubEnabled**  <br>*facoltativo*|Abilitare l'Hub di eventi per un servizio|boolean|
|**storageEnabled**  <br>*facoltativo*|Abilitare la risorsa di archiviazione per un servizio|boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Elenco impaginato di immagini


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco|string|
|**value**  <br>*facoltativo*|Matrice di oggetti del modello|< [Image](#image) > array|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Elenco impaginato di manifesti


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco|string|
|**value**  <br>*facoltativo*|Matrice di oggetti manifesto|< [Manifest](#manifest) > array|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Elenco impaginato di modelli


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco|string|
|**value**  <br>*facoltativo*|Matrice di oggetti modello|< [Model](#model) > array|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Elenco impaginato di servizi


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco|string|
|**value**  <br>*facoltativo*|Matrice di oggetti servizio|< [ServiceResponse](#serviceresponse) > array|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Richiesta di creazione di un servizio


|Nome|Descrizione|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obbligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obbligatorio*|Immagine per creare il servizio|string|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee  <br>**Valore minimo**: `1`|numero intero|
|**nome**  <br>*obbligatorio*|Nome del servizio|string|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un momento specifico. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Richiesta di rigenerazione della chiave per un servizio


|Nome|Descrizione|Schema|
|---|---|---|
|**keyType**  <br>*facoltativo*|Specifica la chiave da rigenerare.|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Stato dettagliato del servizio


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*|Ora di creazione del servizio (UTC)|string (date-time)|
|**id**  <br>*facoltativo*|ID del servizio|string|
|**image**  <br>*facoltativo*||[Immagine](#image)|
|**manifest**  <br>*facoltativo*||[Manifesto](#manifest)|
|**models**  <br>*facoltativo*|Elenco di modelli|< [Model](#model) > array|
|**nome**  <br>*facoltativo*|Nome del servizio|string|
|**scoringUri**  <br>*facoltativo*|Uri per la valutazione del servizio|string|
|**state**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*facoltativo*|Ora dell'ultimo aggiornamento (UTC)|string (date-time)|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obbligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee  <br>**Valore minimo**: `1`|numero intero|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un momento specifico. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Richiesta di aggiornamento di un servizio


|Nome|Descrizione|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*facoltativo*|Immagine per creare il servizio|string|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee  <br>**Valore minimo**: `1`|numero intero|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un momento specifico. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Tipo di runtime di destinazione


|Nome|Descrizione|Schema|
|---|---|---|
|**properties**  <br>*obbligatorio*||< string, string > map|
|**runtimeType**  <br>*obbligatorio*|Specifica il runtime|enum (SparkPython, Python)|

