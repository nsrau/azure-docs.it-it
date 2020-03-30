---
title: Creare, gestire e proteggere le chiavi API di amministrazione e queryCreate, manage, and secure admin and query api-keys
titleSuffix: Azure Cognitive Search
description: Una chiave api controlla l'accesso all'endpoint del servizio. Le chiavi amministratore concedono l'accesso in scrittura. Le chiavi di query possono essere create per l'accesso in sola lettura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794379"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Creare e gestire chiavi API per un servizio Ricerca cognitiva di AzureCreate and manage api-keys for an Azure Cognitive Search service

Tutte le richieste indirizzate a un servizio di ricerca necessitano di una chiave API di sola lettura generata specificamente per il proprio servizio. Questa chiave API è l'unico meccanismo di autenticazione dell'accesso all'endpoint di servizio di ricerca e deve essere inclusa in ogni richiesta. In [soluzioni REST](search-get-started-postman.md), la chiave api viene in genere specificata in un'intestazione della richiesta. In [soluzioni .NET](search-howto-dotnet-sdk.md#core-scenarios), una chiave è spesso specificata come un'impostazione di configurazione e quindi passata come [Credenziali](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (chiave di amministrazione) o [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (chiave di query) nel [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Le chiavi vengono create con il servizio di ricerca durante il provisioning del servizio. È possibile visualizzare e ottenere valori di chiave nel [portale di Azure](https://portal.azure.com).

![Pagina del portale, sezione Impostazioni, Chiavi](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Definizione di una chiave API

Una chiave API è una stringa composta da lettere e numeri generati casualmente. Tramite [autorizzazioni basate sui ruoli](search-security-rbac.md) è possibile eliminare o leggere le chiavi, ma non è possibile sostituire una chiave con una password definita dall'utente o usare Active Directory come metodologia di autenticazione principale per l'accesso alle operazioni di ricerca. 

Per accedere al servizio di ricerca vengono usati due tipi di chiavi: amministratore (di lettura-scrittura) e query (di sola lettura).

|Chiave|Descrizione|Limiti|  
|---------|-----------------|------------|  
|Amministrativi|Concede diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati.<br /><br /> Due chiavi amministratore, chiamate chiave *primaria* e *secondaria* nel portale, vengono generate quando il servizio viene creato e possono essere generate di nuovo singolarmente su richiesta. Avere due chiavi consente di eseguire il rollover di una chiave mentre si usa la seconda per l'accesso continuo al servizio.<br /><br /> Le chiavi amministratore vengono specificate solo nelle intestazioni delle richieste HTTP. Non è possibile inserire un elemento api-key amministratore in un URL.|Un massimo di 2 per servizio|  
|Query|Concede l'accesso in sola lettura agli indici e ai documenti e viene in genere distribuita alle applicazioni client che inviano richieste di ricerca.<br /><br /> Le chiavi di query vengono create su richiesta. È possibile crearle manualmente nel portale o a livello di codice tramite l'[API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Le chiavi di query possono essere specificate nell'intestazione di una richiesta HTTP per un'operazione di ricerca o suggerimento. In alternativa, è possibile passare una chiave di query come parametro in un URL. A seconda di come l'applicazione client formula la richiesta, può risultare più semplice passare la chiave come parametro di query:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 per servizio|  

 Non esiste alcuna distinzione visiva tra una chiave amministratore o una chiave di query. Entrambe le chiavi sono stringhe composte da 32 caratteri alfanumerici generati in modo casuale. Se si è persa traccia del tipo di chiave specificato nell'applicazione, è possibile [controllare i valori delle chiavi nel portale](https://portal.azure.com) o usare l'[API REST](https://docs.microsoft.com/rest/api/searchmanagement/) per restituire il valore e il tipo di chiave.  

> [!NOTE]  
>  Passare dati sensibili, ad esempio un elemento `api-key`, nell'URI della richiesta è considerato una procedura di sicurezza non ottimale. Per questo motivo, Ricerca cognitiva di `api-key` Azure accetta solo una chiave di query come stringa di query ed è consigliabile evitare di farlo a meno che il contenuto dell'indice non sia disponibile pubblicamente. Come regola generale, è consigliabile passare l'elemento `api-key` come intestazione della richiesta.  

## <a name="find-existing-keys"></a>Trovare le chiavi esistenti

È possibile ottenere le chiavi di accesso nel portale o tramite l'[API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/). Per altre informazioni, vedere [Manage admin and query api-keys](search-security-api-keys.md) (Gestione di chiavi API query e amministratore).

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Elencare i [servizi di ricerca](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio e nella pagina Panoramica fare clic su **Impostazioni** >**impostazioni** per visualizzare le chiavi di amministratore e di query.

   ![Pagina del portale, sezione Impostazioni, Chiavi](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Creare chiavi di query

Le chiavi di query vengono utilizzate per l'accesso in sola lettura ai documenti all'interno di un indice per le operazioni destinate a una raccolta di documenti. Le query di ricerca, filtro e suggerimento sono operazioni che accettano una chiave di query. Qualsiasi operazione di sola lettura che restituisce dati di sistema o definizioni di oggetti, ad esempio una definizione di indice o lo stato dell'indicizzatore, richiede una chiave di amministrazione.

Limitare l'accesso e le operazioni nelle app client è essenziale per proteggere le risorse di ricerca nel servizio. Usare sempre una chiave di query anziché una chiave di amministrazione per qualsiasi query proveniente da un'app client.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Elencare i [servizi di ricerca](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio e nella pagina Panoramica fare clic su **Impostazioni** >**chiavi**.
4. Fare clic su **Gestisci chiavi di query**.
5. Usare la chiave di query già generata per il servizio o creare fino a 50 nuove chiavi di query. La chiave di query predefinita non è denominata, ma è possibile denominare chiavi di query aggiuntive per la gestibilità.

   ![Creare o utilizzare una chiave di query](media/search-security-overview/create-query-key.png) 

> [!Note]
> Un esempio di codice che mostra l'utilizzo della chiave di query è disponibile in [Query di un indice di Ricerca cognitiva](search-query-dotnet.md)di Azure in C .

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Riscrivere una chiave amministratore

Vengono create due chiavi di amministrazione per ogni servizio in modo che sia possibile ruotare una chiave primaria usando la chiave secondaria per la continuità aziendale.

1. Nella pagina**Chiavi** **impostazioni** >copiare la chiave secondaria.
2. Per tutte le applicazioni, aggiornare le impostazioni relative alle chiavi API per usare la chiave secondaria.
3. Riscrivere la chiave primaria.
4. Aggiornare tutte le applicazioni affinché usino la nuova chiave primaria.

Se si rigenerano inavvertitamente entrambe le chiavi contemporaneamente, tutte le richieste client che utilizzano tali chiavi avranno esito negativo con HTTP 403 non consentito. Tuttavia, il contenuto non viene eliminato e non si è bloccati in modo permanente. 

È comunque possibile accedere al servizio tramite il portale o il livello di gestione ([API REST](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)o Azure Resource Manager). Le funzioni di gestione sono operative tramite un ID sottoscrizione non una chiave API del servizio e quindi ancora disponibili anche se le chiavi API non lo sono. 

Dopo aver creato nuove chiavi tramite il portale o il livello di gestione, l'accesso viene ripristinato al contenuto (indici, indicizzatori, origini dati, mappe dei sinonimi) una volta che si dispone delle nuove chiavi e fornire tali chiavi sulle richieste.

## <a name="secure-api-keys"></a>Proteggere le chiavi API
La sicurezza delle chiavi viene garantita limitando l'accesso tramite il portale o le interfacce di Resource Manager (PowerShell o interfaccia della riga di comando). Come indicato, gli amministratori delle sottoscrizioni possono visualizzare e rigenerare tutte le chiavi API. Per precauzione, esaminare le assegnazioni di ruolo per sapere chi ha accesso alle chiavi amministratore.

+ Nella dashboard del servizio, fare clic su **Controllo di accesso (IAM)** e quindi sulla scheda **Assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo per il servizio.

I membri dei ruoli seguenti possono visualizzare e ricreare una chiave: proprietario, collaboratore, [collaboratore Servizio di ricerca](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Per gli accessi in base al ruolo sui risultati della ricerca è possibile creare filtri di sicurezza per vagliare i risultati in base all'identità, rimuovendo eventuali documenti a cui il richiedente non dovrebbe avere accesso. Per altre informazioni, vedere [Filtri di sicurezza](search-security-trimming-for-azure-search.md) e [Secure with Active Directory](search-security-trimming-for-azure-search-with-aad.md) (Protezione mediante Active Directory).

## <a name="see-also"></a>Vedere anche

+ [Controllo degli accessi in base al ruolo in Ricerca cognitiva di AzureRole-based access control in Azure Cognitive Search](search-security-rbac.md)
+ [Gestire usando PowerShell](search-manage-powershell.md) 
+ [Articolo su prestazioni e ottimizzazione](search-performance-optimization.md)