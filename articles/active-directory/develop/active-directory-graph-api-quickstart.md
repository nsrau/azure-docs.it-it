---
title: Avvio rapido per l'API Graph di Azure AD | Microsoft Docs
description: L'API Graph di Azure Active Directory consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory.
services: active-directory
documentationcenter: n/a
author: viv-liu
manager: mbaldwin
editor: 
tags: 
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: viviali
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: adefef44d9d3d8e986b26ebe813ecd5d3a6c6f3f
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017


---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Avvio rapido per l'API Graph di Azure AD
L'API Graph di Azure Active Directory (AD) consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. Ad esempio, è possibile usare l'API Graph per creare un nuovo utente, visualizzare o aggiornare le proprietà dell'utente, modificare la password utente, verificare l'appartenenza ai gruppi per l'accesso basato sui ruoli e disabilitare o eliminare l'utente. Per altre informazioni sulle funzionalità dell'API Graph e sugli scenari di applicazione, vedere [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [Prerequisiti dell'API di Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Per accedere alle risorse di Azure Active Directoryi è consigliabile usare [Microsoft Graph](https://developer.microsoft.com/graph) anziché l'API Graph di Azure AD. Le attività di sviluppo sono ora concentrate su Microsoft Graph e non sono previsti altri miglioramenti all'API Graph di Azure AD. Il numero di scenari in cui potrebbe essere ancora appropriato usare l'API Graph di Azure AD è piuttosto limitato. Per altre informazioni, vedere il post [Microsoft Graph o Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) nel blog di Office Developer Center.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Come creare un URL dell'API Graph
Per accedere a dati e oggetti, ovvero risorse o entità, della directory su cui eseguire operazioni CRUD, nell'API Graph è possibile usare gli URL basati sul protocollo OData (Open Data). Gli URL usati nell'API Graph sono costituiti da quattro parti principali: radice del servizio, identificatore del tenant, percorso delle risorse e opzioni della stringa di query: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Considerare questo URL di esempio: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Radice del servizio**: nell'API Graph di Azure AD la radice del servizio è sempre https://graph.windows.net.
* **Identificatore del tenant**: questa sezione può corrispondere a un nome di dominio (registrato) verificato, come contoso.com nell'esempio precedente. Può anche essere un ID di oggetto tenant o l'alias "myorganization" o "me". Per altre informazioni, vedere [Indirizzamento delle entità e delle operazioni nell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Percorso della risorsa**: questa sezione di URL identifica la risorsa con cui interagire (utenti, gruppi, un determinato utente o un gruppo specifico e così via). Nell'esempio sopra è l'elemento "groups" di livello principale a risolvere il set di risorse. È anche possibile risolvere un'entità specifica, ad esempio "users/{objectId}" o "users/userPrincipalName".
* **Parametri di query**: un punto interrogativo (?) separa la sezione relativa al percorso delle risorse da quella dei parametri di query. Il parametro di query "api-version" è obbligatorio in tutte le richieste nell'API Graph. L'API Graph supporta anche le seguenti opzioni di query OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Le opzioni di query seguenti non sono attualmente supportate: **$count**, **$inlinecount** e **$skip**. Per altre informazioni, vedere [Query, opzioni di paging e filtri supportati nell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versioni dell'API Graph
È possibile specificare la versione per una richiesta all'API Graph nel parametro di query "api-version". Per la versione 1.5 e successive, usare un valore di versione numerico: api-version=1.6. Per le versioni precedenti, si usa una stringa di data che rispetta il formato AAAA-MM-GG, ad esempio api-version=2013-11-08. Per le funzionalità di anteprima, usare la stringa "beta", ad esempio api-version=beta. Per altre informazioni sulle differenze tra le versioni dell'API Graph, vedere [Controllo delle versioni dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Metadati dell'API Graph
Per restituire il file di metadati dell'API Graph, aggiungere il segmento "$metadata" dopo l'identificatore tenant nell'URL. Ad esempio, questo URL restituisce i metadati per una società demo: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. È possibile immettere l'URL nella barra degli indirizzi di un Web browser per visualizzare i metadati. Il documento di metadati CSDL restituito descrive le entità e tipi complessi, le rispettive proprietà e le funzioni e le azioni esposte dalla versione dell'API Graph richiesta. Se si omette il parametro api-version, vengono restituiti i metadati per la versione più recente.

## <a name="common-queries"></a>Query comuni
[Query comuni dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) elenca le query comuni che è possibile usare con l'API Graph di Azure AD, incluse quelle per accedere alle risorse di livello principale nella directory e le query per eseguire operazioni nella directory.

Ad esempio, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` restituisce informazioni sulla società per la directory contoso.com.

In alternativa, `https://graph.windows.net/contoso.com/users?api-version=1.6` elenca tutti gli oggetti utente presenti nella directory contoso.com.

## <a name="using-the-graph-explorer"></a>Uso di Esplora grafico
È possibile usare Esplora grafico per l'API Graph di Azure AD per eseguire query sui dati di directory durante la creazione dell'applicazione.

Di seguito è riportato l'output che viene visualizzato se si passa a Graph explorer, si accede e si immette `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` per visualizzare tutti gli utenti nella directory dell'utente connesso:

![API Graph di Azure AD explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Caricare Graph explorer**: per caricare lo strumento, passare a [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/). Fare clic su **Accedi** ed effettuare l'accesso con le credenziali dell'account Azure AD per eseguire Graph explorer sul tenant. Se si esegue Graph explorer nel proprio tenant, l'utente o l'amministratore devono acconsentire in fase di accesso. Se si ha una sottoscrizione di Office 365, si ha automaticamente un tenant di Azure AD. Le credenziali usate per accedere a Office 365 sono, in realtà, account di Azure AD ed è possibile usare tali credenziali con Esplora grafico.

**Eseguire una query**: per eseguire una query, digitarla nell'apposita casella di richiesta e fare clic su **OTTIENI** oppure premere il tasto **INVIO**. I risultati vengono visualizzati nella casella delle risposte. `https://graph.windows.net/myorganization/groups?api-version=1.6` elenca ad esempio tutti gli oggetti gruppo nella directory dell'utente connesso.

Si notino le limitazioni e le funzioni dello strumento Esplora grafico riportate di seguito:

* Funzionalità di completamento automatico in set di risorse. Per visualizzarla, fare clic sulla casella di richiesta (dove viene visualizzato l'URL della società). È possibile selezionare un set di risorse dall'elenco a discesa.
* Supporta gli alias di indirizzamento "me" e "myorganization". Ad esempio, è possibile usare `https://graph.windows.net/me?api-version=1.6` per restituire l'oggetto utente dell'utente che ha effettuato l'accesso oppure `https://graph.windows.net/myorganization/users?api-version=1.6` per restituire tutti gli utenti nella directory corrente.
* Una sezione di intestazioni di risposta. Questa sezione può essere usata per consentire la risoluzione dei problemi che si verificano durante l'esecuzione di query.
* Un visualizzatore JSON per la risposta con funzionalità di espansione e compressione.
* Nessun supporto per la visualizzazione di una foto di anteprima.

## <a name="using-fiddler-to-write-to-the-directory"></a>Uso di Fiddler per scrivere nella directory
Ai fini di questa guida rapida, è possibile usare il debugger Web Fiddler per esercitarsi a eseguire operazioni di scrittura sulla directory di Azure AD. Per altre informazioni e per l'installazione di Fiddler, vedere [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

L'esempio seguente usa il debugger Web Fiddler per creare un nuovo gruppo di sicurezza "MyTestGroup" nella directory di Azure AD.

**Ottenere un token di accesso**: per accedere ad Azure AD Graph, è necessario che i client vengano prima correttamente autenticati in Azure AD. Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

**Creare ed eseguire una query**: completare i passaggi seguenti.

1. Aprire il debugger Web Fiddler e passare alla scheda **Composer** .
2. Poiché si desidera creare un nuovo gruppo di sicurezza, scegliere **Post** come metodo HTTP dal menu a discesa. Per altre informazioni sulle operazioni e le autorizzazioni per un oggetto gruppo, vedere [Gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) in [Riferimento all'API REST Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Nel campo accanto a **Post**, come URL della richiesta, digitare: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.
   
   > [!NOTE]
   > È necessario sostituire mytenantdomain con il nome di dominio della directory di Azure AD.
   > 
   > 
4. Nel campo appena sotto il menu a discesa Post digitare:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Sostituire il &lt;token di accesso&gt; con il token di accesso per la directory di Azure AD.
   > 
   > 
5. Nel campo del **corpo della richiesta** digitare quanto segue:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Per altre informazioni sulla creazione di gruppi, vedere [Crea gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Per altre informazioni sulle entità e i tipi di Azure AD esposti da Graph e sulle operazioni che è possibile eseguirvi con Graph, vedere [Riferimento all'API REST Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni su [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Per altre informazioni, vedere [Ambiti di autorizzazione dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)


