---
title: Come usare l'API Graph di Azure AD
description: L'API Graph di Azure Active Directory (AD) consente l'accesso a livello di codice ad Azure AD tramite gli endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph di Azure AD per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e417d29341ecd175f5ef97761292568b200fc64e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884512"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Procedura: Usare l'API Graph di Azure AD

> [!IMPORTANT]
> È consigliabile usare [Microsoft Graph](https://developer.microsoft.com/graph) anziché l'API Graph di Azure AD per accedere alle risorse di Azure Active Directory (Azure AD). Le attività di sviluppo sono ora concentrate su Microsoft Graph e non sono previsti altri miglioramenti all'API Graph di Azure AD. Esistono un numero molto limitato di scenari per cui l'API Graph di Azure AD potrebbe essere ancora appropriata. Per altre informazioni, vedere il post di blog [di Microsoft Graph o Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) e Eseguire la migrazione delle app di Azure AD Graph a Microsoft [Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

L'API Graph di Azure AD fornisce l'accesso a livello di codice ad Azure AD tramite gli endpoint dell'API REST OData.The Azure AD Graph API provides programmatic access to Azure AD through OData REST API endpoints. Le applicazioni possono usare l'API Graph di Azure AD per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. Ad esempio, è possibile usare l'API Graph di Azure AD per creare un nuovo utente, visualizzare o aggiornare le proprietà dell'utente, modificare la password dell'utente, controllare l'appartenenza al gruppo per l'accesso basato sui ruoli, disabilitare o eliminare l'utente. Per altre informazioni sulle funzionalità dell'API Graph di Azure AD e sugli scenari applicativi, vedere Prerequisiti [dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) di Azure AD e [dell'API Graph di Azure AD.](https://msdn.microsoft.com/library/hh974476.aspx) L'API Grafico di Azure AD funziona solo con gli account aziendali o dell'istituto di istruzione/organizzazione.

Questo articolo si applica all'API Graph di Azure AD. Per informazioni analoghe relative all'API Microsoft Graph, vedere [Use the Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api) (Usare l'API Microsoft Graph).

## <a name="how-to-construct-a-graph-api-url"></a>Come creare un URL dell'API Graph

Per accedere a dati e oggetti, ovvero risorse o entità, della directory su cui eseguire operazioni CRUD, nell'API Graph è possibile usare gli URL basati sul protocollo OData (Open Data). Gli URL usati nell'API Graph sono costituiti da quattro parti principali: radice del servizio, identificatore del tenant, percorso delle risorse e opzioni della stringa di query: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Considerare questo URL di esempio: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Radice del servizio**: nell'API Graph di Azure AD la radice del servizio è sempre https://graph.windows.net.
* **Identificatore del tenant**: questa sezione può corrispondere a un nome di dominio (registrato) verificato, come contoso.com nell'esempio precedente. Può anche essere un ID oggetto tenant o l'alias "myorganization" o "me". Per altre informazioni, vedere [Indirizzamento di entità e operazioni nell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)di Azure AD.
* **Percorso risorsa:** questa sezione di un URL identifica la risorsa con cui interagire (utenti, gruppi, un utente specifico o un gruppo specifico e così via). Nell'esempio precedente, è il primo livello "gruppi" per indirizzare tale set di risorse. È inoltre possibile indirizzare un'entità specifica, ad esempio "utenti/" o "utenti/nomePrincipaleutente".
* **Parametri di query**: un punto interrogativo (?) separa la sezione relativa al percorso delle risorse da quella dei parametri di query. Il parametro di query "api-version" è obbligatorio in tutte le richieste nell'API Graph di Azure AD. Quest'ultima supporta anche le seguenti opzioni di query OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Le opzioni di query seguenti non sono attualmente supportate: **$count**, **$inlinecount** e **$skip**. Per altre informazioni, vedere [Query, opzioni di paging e filtri supportati nell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versioni dell'API Graph

Specificare la versione per una richiesta dell'API Graph nel parametro di query "api-version". Per la versione 1.5 e successive, usare un valore di versione numerico: api-version=1.6. Per le versioni precedenti, si usa una stringa di data che rispetta il formato AAAA-MM-GG, ad esempio api-version=2013-11-08. Per le funzioni di anteprima, utilizzare la stringa "beta"; ad esempio, api-version-beta. Per altre informazioni sulle differenze tra le versioni dell'API Graph, vedere Controllo delle versioni delle [API Graph di Azure AD.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)

## <a name="graph-api-metadata"></a>Metadati dell'API Graph

Per restituire il file di metadati dell'API Graph di Azure AD, aggiungere il segmento "$metadata" `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`dopo l'identificatore tenant nell'URL, ad esempio, l'URL seguente restituisce i metadati per una società demo: . È possibile immettere l'URL nella barra degli indirizzi di un Web browser per visualizzare i metadati. Il documento di metadati CSDL restituito descrive le entità e tipi complessi, le rispettive proprietà e le funzioni e le azioni esposte dalla versione dell'API Graph richiesta. Se si omette il parametro api-version, vengono restituiti i metadati per la versione più recente.

## <a name="common-queries"></a>Query comuni

[Le query comuni dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) di Azure AD elencano le query comuni che possono essere usate con il grafico di Azure AD, incluse le query che possono essere usate per accedere alle risorse di primo livello nella directory e le query per eseguire operazioni nella directory.

Ad esempio, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` restituisce informazioni sulla società per la directory contoso.com.

In alternativa, `https://graph.windows.net/contoso.com/users?api-version=1.6` elenca tutti gli oggetti utente presenti nella directory contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Uso di Azure AD Graph Explorer
È possibile usare Azure AD Graph Explorer per consentire all'API Graph di Azure AD di eseguire query sui dati della directory mentre si crea l'applicazione.

Lo screenshot seguente mostra l'output che viene visualizzato se si passa ad Azure AD Graph Explorer, si esegue l'accesso e si immette `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` per visualizzare tutti gli utenti nella directory dell'utente connesso:

![Output di esempio in Azure AD Graph API ExplorerExample output in Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Caricare Azure AD Graph Explorer**: per caricare lo strumento, usare l'indirizzo [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Fare clic su **Login** (Accedi) e accedere con le credenziali dell'account Azure AD per eseguire Azure AD Graph Explorer sul tenant personale. Se un utente esegue Azure AD Graph Explorer sul tenant personale, l'utente o il relativo amministratore deve fornire il proprio consenso in fase di accesso. Se si ha una sottoscrizione di Office 365, si ha automaticamente un tenant di Azure AD. Le credenziali usate per accedere a Office 365 corrispondono in realtà a un account Azure AD ed è possibile usare queste credenziali con Azure AD Graph Explorer.

**Eseguire una query**: per eseguire una query, digitarla nell'apposita casella di richiesta e fare clic su **OTTIENI** oppure premere il tasto **INVIO**. I risultati vengono visualizzati nella casella delle risposte. `https://graph.windows.net/myorganization/groups?api-version=1.6` elenca ad esempio tutti gli oggetti gruppo nella directory dell'utente connesso.

Azure AD Graph Explorer presenta le funzionalità e le limitazioni seguenti:

* Funzionalità di completamento automatico in set di risorse. Per visualizzarla, fare clic sulla casella di richiesta (dove viene visualizzato l'URL della società). È possibile selezionare un set di risorse dall'elenco a discesa.
* Cronologia delle richieste.
* Supporta gli alias di indirizzamento "me" e "myorganization". È ad esempio possibile usare `https://graph.windows.net/me?api-version=1.6` per restituire l'oggetto corrispondente all'utente connesso oppure `https://graph.windows.net/myorganization/users?api-version=1.6` per restituire tutti gli utenti nella directory dell'utente connesso.
* Supporto per tutte le operazioni CRUD sulla directory personale tramite `POST`, `GET`, `PATCH` e `DELETE`.
* Una sezione di intestazioni di risposta. Questa sezione può essere usata per consentire la risoluzione dei problemi che si verificano durante l'esecuzione di query.
* Un visualizzatore JSON per la risposta con funzionalità di espansione e compressione.
* Nessun supporto per la visualizzazione o il caricamento di una foto di anteprima.

## <a name="using-fiddler-to-write-to-the-directory"></a>Uso di Fiddler per scrivere nella directory

Ai fini di questa guida introduttiva, è possibile usare Fiddler Web Debugger per esercitarsi a eseguire operazioni di scrittura sulla directory di Azure AD. È ad esempio possibile ottenere e caricare foto del profilo dell'utente, mentre questo non è possibile con Azure AD Graph Explorer. Per ulteriori informazioni e per installare [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)Fiddler, vedere .

Nell'esempio seguente si usa Fiddler Web Debugger per creare un nuovo gruppo di sicurezza "MyTestGroup" nella directory di Azure AD.

**Ottenere un token di accesso**: per accedere ad Azure AD Graph, è necessario che i client vengano prima correttamente autenticati in Azure AD. Per altre informazioni, vedere Scenari di [autenticazione per Azure AD.](authentication-scenarios.md)

**Creare ed eseguire una query**: completare i passaggi seguenti.

1. Aprire il debugger Web Fiddler e passare alla scheda **Composer** scheda.
2. Poiché si vuole creare un nuovo gruppo di sicurezza, scegliere **Post** come metodo HTTP dal menu a discesa. Per altre informazioni sulle operazioni e sulle autorizzazioni per un oggetto gruppo, vedere Gruppo all'interno delle informazioni di riferimento sull'API REST di Azure AD Graph.For more information about operations and permissions on a group [object,](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)see [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) within the Azure AD Graph REST API reference .
3. Nel campo accanto a **Post** digitare l'URL di richiesta seguente: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > È necessario sostituire {mytenantdomain} con il nome di dominio della propria directory di Azure AD.

4. Nel campo appena sotto il menu a discesa Post digitare l'intestazione HTTP seguente:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Sostituire il &lt;token di accesso&gt; con il token di accesso per la directory di Azure AD.

5. Nel campo **Request body** (Corpo della richiesta) digitare il codice JSON seguente:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Per altre informazioni sulla creazione di gruppi, vedere [Crea gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Per altre informazioni sulle entità e i tipi di Azure AD esposti da Graph e sulle informazioni sulle operazioni che possono essere eseguite su di essi con Graph, vedere Informazioni di [riferimento sull'API REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)di Azure AD Graph.For more information on Azure AD entities that are exposed by Graph and information about the operations that can be performed on them with Graph, see Azure AD Graph REST API reference.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Altre informazioni sugli ambiti di [autorizzazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) di Azure ADLearn Learn more about Azure AD Graph API permission scopes
