---
title: Creare, gestire e proteggere chiavi API di amministrazione e query
titleSuffix: Azure Cognitive Search
description: Una chiave API controlla l'accesso all'endpoint del servizio. Le chiavi amministratore concedono l'accesso in scrittura. Le chiavi di query possono essere create per l'accesso in sola lettura.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0e209e8114d8f1791a00e87894fa12206edcf34e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700223"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Creare e gestire le chiavi API per un servizio ricerca cognitiva di Azure

Per tutte le richieste a un servizio di ricerca è necessario un oggetto di sola lettura `api-key` che è stato generato in modo specifico per il servizio. `api-key`È l'unico meccanismo per autenticare l'accesso all'endpoint del servizio di ricerca e deve essere incluso in ogni richiesta. 

+ Nelle [soluzioni Rest](search-get-started-rest.md)la chiave API viene in genere specificata in un'intestazione di richiesta

+ Nelle [soluzioni .NET](search-howto-dotnet-sdk.md)una chiave viene spesso specificata come impostazione di configurazione e quindi passata come [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)

Le chiavi vengono create con il servizio di ricerca durante il provisioning del servizio. È possibile visualizzare e ottenere valori di chiave nel [portale di Azure](https://portal.azure.com).

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Pagina del portale, recuperare le impostazioni, sezione chiavi" border="false":::

## <a name="what-is-an-api-key"></a>Definizione di una chiave API

Una chiave API è una stringa composta da lettere e numeri generati casualmente. Tramite [autorizzazioni basate sui ruoli](search-security-rbac.md) è possibile eliminare o leggere le chiavi, ma non è possibile sostituire una chiave con una password definita dall'utente o usare Active Directory come metodologia di autenticazione principale per l'accesso alle operazioni di ricerca. 

Per accedere al servizio di ricerca vengono usati due tipi di chiavi: amministratore (di lettura-scrittura) e query (di sola lettura).

|Chiave|Descrizione|Limiti|  
|---------|-----------------|------------|  
|Amministratore|Concede diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati.<br /><br /> Due chiavi amministratore, chiamate chiave *primaria* e *secondaria* nel portale, vengono generate quando il servizio viene creato e possono essere generate di nuovo singolarmente su richiesta. Avere due chiavi consente di eseguire il rollover di una chiave mentre si usa la seconda per l'accesso continuo al servizio.<br /><br /> Le chiavi amministratore vengono specificate solo nelle intestazioni delle richieste HTTP. Non è possibile inserire un elemento api-key amministratore in un URL.|Un massimo di 2 per servizio|  
|Query|Concede l'accesso in sola lettura agli indici e ai documenti e viene in genere distribuita alle applicazioni client che inviano richieste di ricerca.<br /><br /> Le chiavi di query vengono create su richiesta. È possibile crearle manualmente nel portale o a livello di codice tramite l'[API REST di gestione](/rest/api/searchmanagement/).<br /><br /> Le chiavi di query possono essere specificate nell'intestazione di una richiesta HTTP per un'operazione di ricerca o suggerimento. In alternativa, è possibile passare una chiave di query come parametro in un URL. A seconda di come l'applicazione client formula la richiesta, può risultare più semplice passare la chiave come parametro di query:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50 per servizio|  

 Non esiste alcuna distinzione visiva tra una chiave amministratore o una chiave di query. Entrambe le chiavi sono stringhe composte da 32 caratteri alfanumerici generati in modo casuale. Se si è persa traccia del tipo di chiave specificato nell'applicazione, è possibile [controllare i valori delle chiavi nel portale](https://portal.azure.com) o usare l'[API REST](/rest/api/searchmanagement/) per restituire il valore e il tipo di chiave.  

> [!NOTE]  
>  Passare dati sensibili, ad esempio un elemento `api-key`, nell'URI della richiesta è considerato una procedura di sicurezza non ottimale. Per questo motivo, Azure ricerca cognitiva accetta solo una chiave di query come `api-key` nella stringa di query ed è consigliabile evitare di eseguire questa operazione a meno che i contenuti dell'indice non debbano essere disponibili pubblicamente. Come regola generale, è consigliabile passare l'elemento `api-key` come intestazione della richiesta.  

## <a name="find-existing-keys"></a>Trova chiavi esistenti

È possibile ottenere le chiavi di accesso nel portale o tramite l'[API REST di gestione](/rest/api/searchmanagement/). Per altre informazioni, vedere [Manage admin and query api-keys](search-security-api-keys.md) (Gestione di chiavi API query e amministratore).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Elencare i [servizi di ricerca](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio e nella pagina Panoramica fare clic su **Impostazioni**  > **chiavi** per visualizzare le chiavi di query e di amministrazione.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Pagina del portale, Visualizza impostazioni, sezione chiavi" border="false":::

## <a name="create-query-keys"></a>Creare chiavi di query

Le chiavi di query vengono usate per l'accesso in sola lettura ai documenti all'interno di un indice per le operazioni destinate a una raccolta di documenti. Le query di ricerca, filtro e suggerimento sono tutte operazioni che accettano una chiave di query. Qualsiasi operazione di sola lettura che restituisce i dati di sistema o le definizioni degli oggetti, ad esempio lo stato di una definizione di indice o di un indicizzatore, richiede una chiave amministratore.

La limitazione dell'accesso e delle operazioni nelle app client è essenziale per proteggere gli asset di ricerca nel servizio. Usare sempre una chiave di query anziché una chiave di amministrazione per qualsiasi query originata da un'app client.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Elencare i [servizi di ricerca](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) per la sottoscrizione.
3. Selezionare il servizio e nella pagina Panoramica fare clic su **Impostazioni**  > **chiavi**.
4. Fare clic su **Gestisci chiavi di query**.
5. Usare la chiave di query già generata per il servizio oppure creare fino a 50 nuove chiavi di query. La chiave di query predefinita non è denominata, ma le chiavi di query aggiuntive possono essere denominate per gestibilità.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Creare o usare una chiave di query" border="false":::

> [!Note]
> Un esempio di codice che illustra l'utilizzo della chiave di query è disponibile in [eseguire una query su un indice ricerca cognitiva di Azure in C#](./search-get-started-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Riscrivere una chiave amministratore

Vengono create due chiavi amministratore per ogni servizio, in modo che sia possibile ruotare una chiave primaria, usando la chiave secondaria per la continuità aziendale.

1. Nella pagina **Impostazioni**  > **chiavi** copiare la chiave secondaria.
2. Per tutte le applicazioni, aggiornare le impostazioni relative alle chiavi API per usare la chiave secondaria.
3. Riscrivere la chiave primaria.
4. Aggiornare tutte le applicazioni affinché usino la nuova chiave primaria.

Se si rigenerano inavvertitamente entrambe le chiavi contemporaneamente, tutte le richieste client che utilizzano tali chiavi avranno esito negativo con HTTP 403 non consentito. Tuttavia, il contenuto non viene eliminato e non viene bloccato definitivamente. 

È comunque possibile accedere al servizio tramite il portale o il livello di gestione ([API REST](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)o Azure Resource Manager). Le funzioni di gestione sono operative tramite un ID sottoscrizione non una chiave API del servizio e pertanto sono ancora disponibili anche se le chiavi API non lo sono. 

Dopo aver creato nuove chiavi tramite il portale o il livello di gestione, l'accesso viene ripristinato nel contenuto (indici, indicizzatori, origini dati, mappe sinonimi) dopo avere creato le nuove chiavi e fornire tali chiavi alle richieste.

## <a name="secure-api-keys"></a>Proteggere le chiavi API
La sicurezza delle chiavi viene garantita limitando l'accesso tramite il portale o le interfacce di Resource Manager (PowerShell o interfaccia della riga di comando). Come indicato, gli amministratori delle sottoscrizioni possono visualizzare e rigenerare tutte le chiavi API. Per precauzione, esaminare le assegnazioni di ruolo per sapere chi ha accesso alle chiavi amministratore.

+ Nella dashboard del servizio, fare clic su **Controllo di accesso (IAM)** e quindi sulla scheda **Assegnazioni di ruolo** per visualizzare le assegnazioni di ruolo per il servizio.

I membri dei ruoli seguenti possono visualizzare e ricreare una chiave: proprietario, collaboratore, [collaboratore Servizio di ricerca](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> Per gli accessi in base al ruolo sui risultati della ricerca è possibile creare filtri di sicurezza per vagliare i risultati in base all'identità, rimuovendo eventuali documenti a cui il richiedente non dovrebbe avere accesso. Per altre informazioni, vedere [Filtri di sicurezza](search-security-trimming-for-azure-search.md) e [Secure with Active Directory](search-security-trimming-for-azure-search-with-aad.md) (Protezione mediante Active Directory).

## <a name="see-also"></a>Vedere anche

+ [Controllo degli accessi in base al ruolo di Azure in Azure ricerca cognitiva](search-security-rbac.md)
+ [Gestire usando PowerShell](search-manage-powershell.md) 
+ [Articolo su prestazioni e ottimizzazione](search-performance-optimization.md)