---
title: Creare un'immagine Docker per Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo articolo descrive la procedura per creare un'immagine Docker per un servizio Web.
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
ms.openlocfilehash: 03e51ab298a08386f0094d6d0290aa1ec85d337f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Riferimento per l'API di account di Gestione modelli di Azure Machine Learning

Per informazioni sulla configurazione dell'ambiente di distribuzione, vedere [Model Management Account Setup](deployment-setup-configuration.md) (Impostazione dell'account di Gestione modelli).

L'API dell'account di Gestione modelli di Azure Machine Learning implementa le operazioni seguenti:

- Registrazione del modello
- Creazione del manifesto
- Creazione di un'immagine Docker
- Creazione di un servizio Web

È possibile usare questa immagine per creare un servizio Web in locale o in un cluster del servizio contenitore di Azure remoto o un altro ambiente supportato da Docker di propria scelta.

## <a name="prerequisites"></a>Prerequisiti
Verificare di avere completato la procedura di installazione disponibile nel documento [Install and create Quickstart](quickstart-installation.md) (Avvio rapido per installare e creare).

Prima di procedere, è necessario ottenere quanto segue:
1. Provisioning di un account di Gestione modelli
2. Creazione dell'ambiente per la distribuzione e la gestione dei modelli
3. Modello di Machine Learning

### <a name="azure-ad-token"></a>Token Azure AD
Quando si usa l'interfaccia della riga di comando di Azure, eseguire l'accesso tramite `az login`. L'interfaccia della riga di comando utilizza il token di Azure Active Directory (Azure AD) del file .azure. Se si vuole usare le API, sono disponibili le opzioni seguenti.

##### <a name="acquire-the-azure-ad-token-manually"></a>Acquisire manualmente il token di Azure AD
È possibile usare `az login` e ottenere la versione più recente del token dal file .azure che si trova nella home directory.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Acquisire il token Azure AD a livello di codice
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Dopo avere creato l'entità servizio, salvare l'output. È possibile a quel punto usarlo per ottenere il token da Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Il token viene inserito nell'intestazione di autorizzazione per le chiamate API.


## <a name="register-a-model"></a>Registrare un modello

Il passaggio della registrazione del modello registra il modello di Machine Learning con l'account di Gestione modelli di Azure che è stato creato. Questa registrazione consente di tenere traccia dei modelli e delle relative versioni che vengono assegnati al momento della registrazione. L'utente specifica il nome del modello. Se successivamente vengono registrati modelli con lo stesso nome, vengono generati una nuova versione e un nuovo ID.

### <a name="request"></a>Richiesta

| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrizione
Registra un modello.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| model | body | Payload utilizzato per registrare un modello. | Sì | [Modello](#model) |


### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | OK. La registrazione del modello è riuscita. | [Modello](#model) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Eseguire una query sull'elenco di modelli in un account
### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models 
 |
### <a name="description"></a>Descrizione
Esegue una query sull'elenco di modelli in un account. È possibile filtrare l'elenco dei risultati per tag e nome. Se non viene passato alcun filtro, la query elenca tutti i modelli presenti nell'account. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| name | query | Nome dell'oggetto. | No | string |
| tag | query | Tag del modello. | No | string |
| count | query | Numero di voci da visualizzare in una pagina. | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva. | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [PaginatedModelList](#paginatedmodellist) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Ottenere i dettagli del modello
### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{id}  
 |

### <a name="description"></a>Descrizione
Ottiene un modello in base all'ID.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID oggetto. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [Modello](#model) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrare un manifesto con il modello registrato e tutte le dipendenze

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrizione
Registrare un manifesto con il modello registrato e tutte le sue dipendenze.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| manifestRequest | body | Payload utilizzato per registrare un manifesto. | Sì | [Manifesto](#manifest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | La registrazione del manifesto è riuscita. | [Manifesto](#manifest) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Eseguire una query sull'elenco di manifesti in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests | 

### <a name="description"></a>Descrizione
Esegue una query sull'elenco di manifesti in un account. È possibile filtrare l'elenco dei risultati in base all'ID modello e al nome del manifesto. Se non viene passato alcun filtro, la query elenca tutti i manifesti presenti nell'account. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| modelId | query | ID modello. | No | string |
| manifestName | query | Nome del manifesto. | No | string |
| count | query | Numero di voci da visualizzare in una pagina. | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva. | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [PaginatedManifestList](#paginatedmanifestlist) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Ottenere i dettagli del manifesto

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{id} | 

### <a name="description"></a>Descrizione
Ottiene il manifesto in base all'ID.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID oggetto. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [Manifesto](#manifest) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Creare un'immagine

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrizione
Crea un'immagine come immagine Docker nel Registro contenitori di Azure.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| imageRequest | body | Payload utilizzato per creare un'immagine. | Sì | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di creazione dell'immagine. | Operation-Location |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Eseguire una query sull'elenco di immagini in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images | 

### <a name="description"></a>Descrizione
Esegue una query sull'elenco di immagini in un account. È possibile filtrare l'elenco dei risultati in base all'ID e al nome del manifesto. Se non viene passato alcun filtro, la query elenca tutte le immagini presenti nell'account. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| manifestId | query | ID manifesto. | No | string |
| manifestName | query | Nome del manifesto. | No | string |
| count | query | Numero di voci da visualizzare in una pagina. | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva. | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [PaginatedImageList](#paginatedimagelist) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Ottenere i dettagli dell'immagine

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{id} | 

### <a name="description"></a>Descrizione
Ottiene un'immagine in base all'ID.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID immagine. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [Immagine](#image) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Creare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrizione
Crea un servizio da un'immagine.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| serviceRequest | body | Payload utilizzato per creare un servizio. | Sì | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di creazione del servizio. | Operation-Location |
| 409 | Un servizio con il nome specificato esiste già. |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Eseguire una query sull'elenco di servizi in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services | 

### <a name="description"></a>Descrizione
Esegue una query sull'elenco di servizi in un account. È possibile filtrare l'elenco dei risultati in base al nome/ID modello, nome/ID manifesto, ID immagine, nome servizio o ID risorsa di calcolo di Machine Learning. Se non viene passato alcun filtro, la query elenca tutti i servizi presenti nell'account. L'elenco restituito è impaginato e il numero di voci in ogni pagina è un parametro facoltativo.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| serviceName | query | Nome del servizio. | No | string |
| modelId | query | Nome del modello. | No | string |
| modelName | query | ID modello. | No | string |
| manifestId | query | ID manifesto. | No | string |
| manifestName | query | Nome del manifesto. | No | string |
| imageId | query | ID immagine. | No | string |
| computeResourceId | query | ID risorsa di calcolo di Machine Learning. | No | string |
| count | query | Numero di voci da visualizzare in una pagina. | No | string |
| $skipToken | query | Token di continuazione per recuperare la pagina successiva. | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [PaginatedServiceList](#paginatedservicelist) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Ottenere i dettagli del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Ottiene un servizio in base all'ID.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID oggetto. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [ServiceResponse](#serviceresponse) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Aggiornare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| PUT |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Aggiorna un servizio esistente.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID oggetto. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| serviceUpdateRequest | body | Payload utilizzato per aggiornare un servizio esistente. | Sì |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Headers | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | URL di posizione operazione asincrona. Una chiamata GET visualizzerà lo stato dell'attività di aggiornamento del servizio. | Operation-Location |
| 404 | Il servizio con l'ID specificato non esiste. |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Eliminare un servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| DELETE |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id} | 

### <a name="description"></a>Descrizione
Elimina un servizio.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID oggetto. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. |  |
| 204 | Il servizio con l'ID specificato non esiste. |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Ottenere le chiavi del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrizione
Ottiene le chiavi del servizio.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID servizio. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [AuthKeys](#authkeys)
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Rigenerare le chiavi del servizio

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| POST |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{id}/keys | 

### <a name="description"></a>Descrizione
Rigenera le chiavi del servizio e le restituisce.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID servizio. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| regenerateKeyRequest | body | Payload utilizzato per aggiornare un servizio esistente. | Sì | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [AuthKeys](#authkeys)
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Eseguire una query sull'elenco di distribuzioni in un account

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments | 

### <a name="description"></a>Descrizione
Esegue una query sull'elenco di distribuzioni in un account. È possibile filtrare l'elenco dei risultati in base all'ID servizio, che restituirà solo le distribuzioni create per il servizio specifico. Se non viene passato alcun filtro, la query elenca tutte le distribuzioni presenti nell'account.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |
| serviceId | query | ID servizio. | No | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [DeploymentList](#deploymentlist) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Ottenere i dettagli della distribuzione

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/deployments/{id} | 

### <a name="description"></a>Descrizione
Ottiene la distribuzione in base all'ID.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID distribuzione. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [Distribuzione](#deployment) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Ottenere i dettagli dell'operazione

### <a name="request"></a>Richiesta
| Metodo | URI della richiesta |
|------------|------------|
| GET |  /api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/operations/{id} | 

### <a name="description"></a>Descrizione
Ottiene lo stato dell'operazione asincrona in base all'ID operazione.

### <a name="parameters"></a>Parametri
| Nome | Posizione | Descrizione | Obbligatorio | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | path | ID sottoscrizione di Azure. | Sì | string |
| resourceGroupName | path | Nome del gruppo di risorse in cui si trova l'account di Gestione modelli. | Sì | string |
| accountName | path | Nome dell'account di Gestione modelli. | Sì | string |
| id | path | ID operazione. | Sì | string |
| api-version | query | Versione dell'API del provider di risorse Microsoft.Machine.Learning da usare. | Sì | string |
| Authorization | intestazione | Token di autorizzazione. Deve essere qualcosa di simile a "Bearer XXXXXX". | Sì | string |

### <a name="responses"></a>Risposte
| Codice | Descrizione | Schema |
|--------------------|--------------------|--------------------|
| 200 | Completamento della procedura. | [OperationStatus](#asyncoperationstatus) |
| default | Risposta di errore che descrive il motivo per cui l'operazione non è riuscita. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definizioni

<a name="asset"></a>
### <a name="asset"></a>Asset
L'oggetto asset che sarà necessario durante la creazione dell'immagine Docker.


|Nome|Descrizione|Schema|
|---|---|---|
|**id**  <br>*facoltativo*|ID asset.|string|
|**mimeType**  <br>*facoltativo*|Tipo MIME del contenuto del modello. Per altre informazioni sul tipo MIME, vedere l'[elenco dei tipi di supporti IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**unpack**  <br>*facoltativo*|Indica se è necessario decomprimere il contenuto durante la creazione di un'immagine Docker.|boolean|
|**url**  <br>*facoltativo*|URL di posizione dell'asset.|string|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Lo stato dell'operazione asincrona.

*Tipo*: enum (NotStarted, Running, Cancelled, Succeeded, Failed)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Lo stato dell'operazione.


|Nome|Descrizione|Schema|
|---|---|---|
|**createdTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione dell'operazione asincrona (UTC).|string (date-time)|
|**endTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di fine dell'operazione asincrona (UTC).|string (date-time)|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facoltativo*|ID operazione asincrona.|string|
|**operationType**  <br>*facoltativo*|Tipo dell'operazione asincrona.|enum (immagine, servizio)|
|**resourceLocation**  <br>*facoltativo*|Risorsa creata/aggiornata dall'operazione asincrona.|string|
|**state**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Le chiavi di autenticazione per un servizio.


|Nome|Descrizione|Schema|
|---|---|---|
|**primaryKey**  <br>*facoltativo*|Chiave primaria.|string|
|**secondaryKey**  <br>*facoltativo*|Chiave secondaria.|string|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Impostazioni per il ridimensionamento automatico.


|Nome|Descrizione|Schema|
|---|---|---|
|**autoscaleEnabled**  <br>*facoltativo*|Abilitare o disabilitare il ridimensionamento automatico.|boolean|
|**maxReplicas**  <br>*facoltativo*|Numero massimo di repliche pod fino a cui scalare.  <br>**Valore minimo**: `1`|numero intero|
|**minReplicas**  <br>*facoltativo*|Numero minimo di repliche pod a cui scalare.  <br>**Valore minimo**: `0`|numero intero|
|**refreshPeriodInSeconds**  <br>*facoltativo*|Ora di aggiornamento per il trigger del ridimensionamento automatico.  <br>**Valore minimo**: `1`|numero intero|
|**targetUtilization**  <br>*facoltativo*|Percentuale di utilizzo che attiva il ridimensionamento automatico.  <br>**Valore minimo**: `0`  <br>**Valore massimo**: `100`|numero intero|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
La risorsa di calcolo di Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**id**  <br>*facoltativo*|ID risorsa.|string|
|**type**  <br>*facoltativo*|Tipo di risorsa.|enum (cluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Configurazione per riservare risorse per il contenitore nel cluster.


|Nome|Descrizione|Schema|
|---|---|---|
|**cpu**  <br>*facoltativo*|Specifica di prenotazione della CPU. Formato per Kubernetes: vedere [Meaning of CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu) (Significato di "CPU").|string|
|**memory**  <br>*facoltativo*|Specifica di prenotazione della memoria. Formato per Kubernetes: vedere [Meaning of memory](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory) (Significato di "memoria").|string|


<a name="deployment"></a>
### <a name="deployment"></a>Distribuzione
Un'istanza di una distribuzione di Azure Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione della distribuzione (UTC).|string (date-time)|
|**expiredAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di scadenza della distribuzione (UTC).|string (date-time)|
|**id**  <br>*facoltativo*|ID distribuzione.|string|
|**imageId**  <br>*facoltativo*|ID immagine associato con questa distribuzione.|string|
|**serviceName**  <br>*facoltativo*|Nome del servizio.|string|
|**Stato**  <br>*facoltativo*|Stato attuale della distribuzione.|string|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
Una matrice di oggetti distribuzione.

*Tipo*: matrice <[Deployment](#deployment)>


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Dettagli dell'errore del servizio Gestione modelli.


|Nome|Descrizione|Schema|
|---|---|---|
|**code**  <br>*obbligatorio*|Codice di errore.|string|
|**message**  <br>*obbligatorio*|Messaggio di errore.|string|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Un oggetto errore del servizio Gestione modelli.


|Nome|Descrizione|Schema|
|---|---|---|
|**code**  <br>*obbligatorio*|Codice di errore.|string|
|**details**  <br>*facoltativo*|Una matrice di oggetti dettagli errore.|Matrice <[ErrorDetail](#errordetail)>|
|**message**  <br>*obbligatorio*|Messaggio di errore.|string|
|**statusCode**  <br>*facoltativo*|Codice stato HTTP.|numero intero|


<a name="image"></a>
### <a name="image"></a>Image
L'immagine di Azure Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**computeResourceId**  <br>*facoltativo*|ID dell'ambiente creato nella risorsa di calcolo di Machine Learning.|string|
|**createdTime**  <br>*facoltativo*|Ora di creazione dell'immagine (UTC).|string (date-time)|
|**creationState**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|
|**description**  <br>*facoltativo*|Testo descrittivo dell'immagine.|string|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|
|**id**  <br>*facoltativo*|ID immagine.|string|
|**imageBuildLogUri**  <br>*facoltativo*|URI dei log caricati dalla build dell'immagine.|string|
|**imageLocation**  <br>*facoltativo*|Stringa di posizione del Registro contenitori di Azure per l'immagine creata.|string|
|**imageType**  <br>*facoltativo*||[ImageType](#imagetype)|
|**manifest**  <br>*facoltativo*||[Manifesto](#manifest)|
|**nome**  <br>*facoltativo*|Nome dell'immagine.|string|
|**version**  <br>*facoltativo*|Versione dell'immagine impostata dal servizio Gestione modelli.|numero intero|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>ImageRequest
Richiesta di creazione di un'immagine di Azure Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**computeResourceId**  <br>*obbligatorio*|ID dell'ambiente creato nella risorsa di calcolo di Machine Learning.|string|
|**description**  <br>*facoltativo*|Testo descrittivo dell'immagine.|string|
|**imageType**  <br>*obbligatorio*||[ImageType](#imagetype)|
|**manifestId**  <br>*obbligatorio*|ID del manifesto da cui verrà creata l'immagine.|string|
|**nome**  <br>*obbligatorio*|Nome dell'immagine.|string|


<a name="imagetype"></a>
### <a name="imagetype"></a>ImageType
Specifica il tipo dell'immagine.

*Tipo* : enum (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifesto
Il manifesto di Azure Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**assets**  <br>*obbligatorio*|Elenco di asset.|Matrice <[Asset](#asset)>|
|**createdTime**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione del manifesto (UTC).|string (date-time)|
|**description**  <br>*facoltativo*|Testo descrittivo del manifesto.|string|
|**driverProgram**  <br>*obbligatorio*|Programma driver del manifesto.|string|
|**id**  <br>*facoltativo*|ID manifesto.|string|
|**modelIds**  <br>*facoltativo*|Elenco degli ID modello dei modelli registrati. La richiesta non riuscirà se uno qualsiasi dei modelli inclusi non è registrato.|Matrice <string>|
|**modelType**  <br>*facoltativo*|Specifica che i modelli sono già registrati con il servizio Gestione modelli.|enum (Registered)|
|**nome**  <br>*obbligatorio*|Nome del manifesto.|string|
|**targetRuntime**  <br>*obbligatorio*||[TargetRuntime](#targetruntime)|
|**version**  <br>*facoltativo*  <br>*sola lettura*|La versione del manifesto assegnata dal servizio Gestione modelli.|numero intero|
|**webserviceType**  <br>*facoltativo*|Specifica il tipo desiderato di servizio Web che verrà creato dal manifesto.|enum (Realtime)|


<a name="model"></a>
### <a name="model"></a>Modello
Un'istanza di un modello di Azure Machine Learning.


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*  <br>*sola lettura*|Ora di creazione del modello (UTC).|string (date-time)|
|**description**  <br>*facoltativo*|Testo descrittivo del modello.|string|
|**id**  <br>*facoltativo*  <br>*sola lettura*|ID modello.|string|
|**mimeType**  <br>*obbligatorio*|Tipo MIME del contenuto del modello. Per altre informazioni sul tipo MIME, vedere l'[elenco dei tipi di supporti IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|string|
|**nome**  <br>*obbligatorio*|Nome del modello.|string|
|**tag**  <br>*facoltativo*|Elenco di tag di modello.|Matrice <string>|
|**unpack**  <br>*facoltativo*|Indica se è necessario decomprimere il modello durante la creazione di un'immagine Docker.|boolean|
|**url**  <br>*obbligatorio*|URL del modello. In genere viene inserito un URL di firma di accesso condiviso qui.|string|
|**version**  <br>*facoltativo*  <br>*sola lettura*|Versione del modello assegnata dal servizio Gestione modelli.|numero intero|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Informazioni sulla raccolta di dati del modello.


|Nome|Descrizione|Schema|
|---|---|---|
|**eventHubEnabled**  <br>*facoltativo*|Abilitare un hub di eventi per un servizio.|boolean|
|**storageEnabled**  <br>*facoltativo*|Abilitare la risorsa di archiviazione per un servizio.|boolean|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
Un elenco impaginato di immagini.


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco.|string|
|**value**  <br>*facoltativo*|Matrice di oggetti modello.|Matrice <[Image](#image)>|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
Un elenco impaginato di manifesti.


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco.|string|
|**value**  <br>*facoltativo*|Matrice di oggetti manifesto.|Matrice <[Manifest](#manifest)>|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
Un elenco impaginato di modelli.


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco.|string|
|**value**  <br>*facoltativo*|Matrice di oggetti modello.|Matrice <[Model](#model)>|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
Un elenco impaginato di servizi.


|Nome|Descrizione|Schema|
|---|---|---|
|**nextLink**  <br>*facoltativo*|Collegamento di continuità (URI assoluto) alla pagina successiva dell'elenco.|string|
|**value**  <br>*facoltativo*|Matrice di oggetti servizio.|Matrice <[ServiceResponse](#serviceresponse)>|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
Una richiesta di creazione di un servizio.


|Nome|Descrizione|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio.|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obbligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*obbligatorio*|Immagine per creare il servizio.|string|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee.  <br>**Valore minimo**: `1`|numero intero|
|**nome**  <br>*obbligatorio*|Nome del servizio.|string|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un dato momento. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
Una richiesta di rigenerazione della chiave per un servizio.


|Nome|Descrizione|Schema|
|---|---|---|
|**keyType**  <br>*facoltativo*|Specifica la chiave da rigenerare.|enum (Primary, Secondary)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Lo stato dettagliato del servizio.


|Nome|Descrizione|Schema|
|---|---|---|
|**createdAt**  <br>*facoltativo*|Ora di creazione del servizio (UTC).|string (date-time)|
|**ID**  <br>*facoltativo*|ID servizio.|string|
|**image**  <br>*facoltativo*||[Immagine](#image)|
|**manifest**  <br>*facoltativo*||[Manifesto](#manifest)|
|**models**  <br>*facoltativo*|Elenco di modelli.|Matrice <[Model](#model)>|
|**nome**  <br>*facoltativo*|Nome del servizio.|string|
|**scoringUri**  <br>*facoltativo*|URI per la valutazione del servizio.|string|
|**state**  <br>*facoltativo*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*facoltativo*|Ora dell'ultimo aggiornamento (UTC).|string (date-time)|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio.|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*obbligatorio*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee.  <br>**Valore minimo**: `1`|numero intero|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un dato momento. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|
|**error**  <br>*facoltativo*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
Una richiesta di aggiornamento di un servizio.


|Nome|Descrizione|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*facoltativo*|Abilitare Application Insights per un servizio.|boolean|
|**autoScaler**  <br>*facoltativo*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*facoltativo*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*facoltativo*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*facoltativo*|Immagine per creare il servizio.|string|
|**maxConcurrentRequestsPerContainer**  <br>*facoltativo*|Numero massimo di richieste simultanee.  <br>**Valore minimo**: `1`|numero intero|
|**numReplicas**  <br>*facoltativo*|Numero di repliche pod in esecuzione in un dato momento. Non è possibile specificare se Autoscaler è abilitato.  <br>**Valore minimo**: `0`|numero intero|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Il tipo di runtime di destinazione.


|Nome|Descrizione|Schema|
|---|---|---|
|**properties**  <br>*obbligatorio*||Mappa <stringa, stringa>|
|**runtimeType**  <br>*obbligatorio*|Specifica il runtime.|enum (SparkPython, Python)|

