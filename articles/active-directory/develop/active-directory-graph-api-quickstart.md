---
title: Come usare l'API Graph di Azure AD
description: L'API Graph di Azure Active Directory (AD) consente l'accesso a livello di codice ad Azure AD tramite gli endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph di Azure AD per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory.
services: active-directory
documentationcenter: n/a
author: rwike77
manager: CelesteDG
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a88b1e17812b9dc99fd1d5b391d95ba541f48a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533072"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Procedura: Usare l'API Graph di Azure AD

> [!IMPORTANT]
> È consigliabile usare [Microsoft Graph](https://developer.microsoft.com/graph) anziché Azure ad API Graph per accedere alle risorse Azure Active Directory (Azure ad). Le attività di sviluppo sono ora concentrate su Microsoft Graph e non sono previsti altri miglioramenti all'API Graph di Azure AD. Esistono un numero molto limitato di scenari per i quali Azure AD API Graph potrebbero ancora essere appropriati. per altre informazioni, vedere il post di Blog relativo alla [Microsoft Graph o al Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) ed [eseguire la migrazione di app graph Azure ad a Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

Il Azure AD API Graph fornisce l'accesso programmatico ai Azure AD tramite gli endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph di Azure AD per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. È ad esempio possibile usare l'API Graph di Azure AD per creare un nuovo utente, visualizzare o aggiornare le proprietà dell'utente, modificare la password utente, verificare l'appartenenza ai gruppi per l'accesso basato sui ruoli e disabilitare o eliminare l'utente. Per ulteriori informazioni su Azure AD API Graph le funzionalità e gli scenari di applicazione, vedere [Azure AD API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [Azure ad API Graph prerequisiti](https://msdn.microsoft.com/library/hh974476.aspx). Azure AD API Graph funziona solo con gli account aziendali o dell'Istituto di istruzione/organizzazione.

Questo articolo si applica all'API Graph di Azure AD. Per informazioni analoghe relative all'API Microsoft Graph, vedere [Use the Microsoft Graph API](https://developer.microsoft.com/graph/docs/concepts/use_the_api) (Usare l'API Microsoft Graph).

## <a name="how-to-construct-a-graph-api-url"></a>Come creare un URL dell'API Graph

Per accedere a dati e oggetti, ovvero risorse o entità, della directory su cui eseguire operazioni CRUD, nell'API Graph è possibile usare gli URL basati sul protocollo OData (Open Data). Gli URL usati nell'API Graph sono costituiti da quattro parti principali: radice del servizio, identificatore del tenant, percorso delle risorse e opzioni della stringa di query: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Considerare questo URL di esempio: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Radice del servizio**: nell'API Graph di Azure AD la radice del servizio è sempre https://graph.windows.net.
* **Identificatore del tenant**: questa sezione può corrispondere a un nome di dominio (registrato) verificato, come contoso.com nell'esempio precedente. Può anche essere un ID di oggetto tenant o l'alias "myorganization" o "me". Per ulteriori informazioni, vedere [indirizzamento delle entità e delle operazioni in Azure AD API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Percorso della risorsa**: questa sezione di un URL identifica la risorsa con cui interagire (utenti, gruppi, un determinato utente o un particolare gruppo e così via). Nell'esempio precedente, è il "gruppo" di livello principale che risolve il set di risorse. È anche possibile risolvere un'entità specifica, ad esempio "users/{objectId}" o "users/userPrincipalName".
* **Parametri di query**: un punto interrogativo (?) separa la sezione relativa al percorso delle risorse da quella dei parametri di query. Il parametro di query "api-version" è obbligatorio in tutte le richieste nell'API Graph di Azure AD. Quest'ultima supporta anche le seguenti opzioni di query OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Le opzioni di query seguenti non sono attualmente supportate: **$count**, **$inlinecount** e **$skip**. Per altre informazioni, vedere [Query, opzioni di paging e filtri supportati nell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versioni dell'API Graph

È possibile specificare la versione per una richiesta all'API Graph nel parametro di query "api-version". Per la versione 1.5 e successive, usare un valore di versione numerico: api-version=1.6. Per le versioni precedenti, si usa una stringa di data che rispetta il formato AAAA-MM-GG, ad esempio api-version=2013-11-08. Per le funzionalità di anteprima, usare la stringa "beta", ad esempio api-version=beta. Per ulteriori informazioni sulle differenze tra API Graph versioni, vedere [Azure AD API Graph controllo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)delle versioni.

## <a name="graph-api-metadata"></a>Metadati dell'API Graph

Per restituire il file di metadati dell'API Graph di Azure AD, aggiungere il segmento "$metadata" dopo l'identificatore tenant nell'URL. Ad esempio, l'URL seguente restituisce i metadati per una società demo: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. È possibile immettere l'URL nella barra degli indirizzi di un Web browser per visualizzare i metadati. Il documento di metadati CSDL restituito descrive le entità e tipi complessi, le rispettive proprietà e le funzioni e le azioni esposte dalla versione dell'API Graph richiesta. Se si omette il parametro api-version, vengono restituiti i metadati per la versione più recente.

## <a name="common-queries"></a>Query comuni

[Azure AD API Graph Query comuni](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) elenca le query comuni che possono essere usate con il grafico Azure ad, incluse le query che possono essere usate per accedere alle risorse di livello superiore nella directory e le query per eseguire operazioni nella directory.

Ad esempio, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` restituisce informazioni sulla società per la directory contoso.com.

In alternativa, `https://graph.windows.net/contoso.com/users?api-version=1.6` elenca tutti gli oggetti utente presenti nella directory contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Uso di Azure AD Graph Explorer
È possibile usare Azure AD Graph Explorer per consentire all'API Graph di Azure AD di eseguire query sui dati della directory mentre si crea l'applicazione.

Lo screenshot seguente mostra l'output che viene visualizzato se si passa ad Azure AD Graph Explorer, si esegue l'accesso e si immette `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` per visualizzare tutti gli utenti nella directory dell'utente connesso:

![Output di esempio in Azure AD API Graph Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

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

Ai fini di questa guida rapida, è possibile usare il debugger Web Fiddler per esercitarsi a eseguire operazioni di scrittura sulla directory di Azure AD. È ad esempio possibile ottenere e caricare foto del profilo dell'utente, mentre questo non è possibile con Azure AD Graph Explorer. Per altre informazioni e per l'installazione di Fiddler, vedere [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler).

L'esempio seguente usa il debugger Web Fiddler per creare un nuovo gruppo di sicurezza "MyTestGroup" nella directory di Azure AD.

**Ottenere un token di accesso**: per accedere ad Azure AD Graph, è necessario che i client vengano prima correttamente autenticati in Azure AD. Per ulteriori informazioni, vedere [scenari di autenticazione per Azure ad](v1-authentication-scenarios.md).

**Creare ed eseguire una query**: completare i passaggi seguenti.

1. Aprire il debugger Web Fiddler e passare alla scheda **Composer** .
2. Poiché si desidera creare un nuovo gruppo di sicurezza, scegliere **Post** come metodo HTTP dal menu a discesa. Per altre informazioni sulle operazioni e sulle autorizzazioni per un oggetto gruppo, vedere il [gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) all'interno del [riferimento all'API REST di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
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

Per altre informazioni su Azure AD entità e sui tipi esposti da Graph e sulle operazioni che possono essere eseguite con Graph, vedere informazioni di [riferimento sull'API REST di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Ulteriori informazioni sugli [ambiti di autorizzazione Azure AD API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
