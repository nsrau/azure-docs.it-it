---
title: Avvio rapido per l'API Graph di Azure AD | Microsoft Docs
description: L'API Graph di Azure Active Directory consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory.
services: active-directory
documentationcenter: n/a
author: JimacoMS
manager: msmbaldwin
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: v-jibran@microsoft.com

---
# Avvio rapido per l'API Graph di Azure AD
L'API Graph di Azure Active Directory (AD) consente l'accesso a livello di codice ad Azure AD tramite endpoint dell'API REST OData. Le applicazioni possono usare l'API Graph per le operazioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete) su oggetti e dati della directory. Ad esempio, è possibile usare l'API Graph per creare un nuovo utente, visualizzare o aggiornare le proprietà dell'utente, modificare la password utente, verificare l'appartenenza ai gruppi per l'accesso basato sui ruoli e disabilitare o eliminare l'utente. Per altre informazioni sulle funzionalità dell'API Graph e sugli scenari di applicazione, vedere [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) e [Prerequisiti dell'API di Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx).

> [!IMPORTANT]
> L'API Graph di Azure AD è disponibile anche tramite [Microsoft Graph](https://graph.microsoft.io/), un'API unificata composta da API di altri servizi Microsoft, quali Outlook, OneDrive, OneNote, Planner e Office Graph, accessibile tramite un singolo endpoint e con un unico token di accesso.
> 
> 

## Come creare un URL dell'API Graph
Per accedere a dati e oggetti (in altri termini, le risorse o le entità) della directory su cui eseguire operazioni CRUD, nell'API Graph è possibile usare gli URL basati sul protocollo OData (Open Data). Gli URL usati nell'API Graph sono costituiti da quattro parti principali: radice del servizio, identificatore del tenant, percorso delle risorse e opzioni della stringa di query: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Considerare questo URL di esempio: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Radice del servizio**: nell'API Graph di Azure AD la radice del servizio è sempre https://graph.windows.net.
* **Identificatore del tenant**: può trattarsi di un nome di dominio (registrato) verificato, come contoso.com nell'esempio precedente. Può anche essere un ID di oggetto tenant o l'alias "myorganization" o "me". Per altre informazioni, vedere [Indirizzamento delle entità e delle operazioni nell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Percorso della risorsa**: questa sezione di URL identifica la risorsa con cui interagire (utenti, gruppi, un determinato utente o un gruppo specifico e così via). Nell'esempio sopra è l'elemento "groups" di livello principale a risolvere il set di risorse. È anche possibile risolvere un'entità specifica, ad esempio "users/{objectId}" o "users/userPrincipalName".
* **Parametri di query**: ? separa la sezione relativa al percorso delle risorse da quella dei parametri di query. Il parametro di query "api-version" è obbligatorio in tutte le richieste nell'API Graph. L'API Graph supporta anche le seguenti opzioni di query OData: **$filter**, **$orderby**, **$expand**, **$top** e **$format**. Le opzioni di query seguenti non sono attualmente supportate: **$count**, **$inlinecount** e **$skip**. Per altre informazioni, vedere [Query, opzioni di paging e filtri supportati nell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## Versioni dell'API Graph
È possibile specificare la versione per una richiesta all'API Graph nel parametro di query "api-version". Per la versione 1.5 e successive, usare un valore di versione numerico: api-version=1.6. Per le versioni precedenti, si usa una stringa di data che rispetta il formato AAAA-MM-GG, ad esempio api-version=2013-11-08. Per le funzionalità di anteprima, usare la stringa "beta", ad esempio api-version=beta. Per altre informazioni sulle differenze tra le versioni dell'API Graph, vedere [Controllo delle versioni dell'API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## Metadati dell'API Graph
Per restituire il file di metadati dell'API Graph, aggiungere il segmento "$metadata" dopo l'identificatore tenant nell'URL. Ad esempio, questo URL restituisce i metadati per la società demo usata da Esplora grafico: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. È possibile immettere l'URL nella barra degli indirizzi di un Web browser per visualizzare i metadati. Il documento di metadati CSDL restituito descrive le entità e tipi complessi, le rispettive proprietà e le funzioni e le azioni esposte dalla versione dell'API Graph richiesta. L'omissione del parametro api-version restituirà i metadati per la versione più recente.

## Query comuni
L'argomento [Query comuni dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) elenca le query comuni che è possibile usare con l'API Graph di Azure AD, incluse quelle per accedere alle risorse di livello principale nella directory e le query per eseguire operazioni nella directory.

Ad esempio, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` restituisce informazioni sulla società per la directory contoso.com.

In alternativa, `https://graph.windows.net/contoso.com/users?api-version=1.6` elenca tutti gli oggetti utente presenti nella directory contoso.com.

## Uso di Esplora grafico
È possibile usare Esplora grafico per l'API Graph di Azure AD per eseguire query sui dati di directory durante la creazione dell'applicazione.

> [!IMPORTANT]
> Esplora grafico non supporta la scrittura o l'eliminazione dei dati da una directory. Con Esplora grafico è possibile eseguire solo operazioni di lettura nella directory di Azure AD.
> 
> 

Di seguito è riportato l'output che viene visualizzato se si passa a Esplora grafico. Selezionare Usa società demo e immettere `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` per visualizzare tutti gli utenti nella directory demo:

![API Graph di Azure AD explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Caricare Esplora grafico**: per caricare lo strumento, passare a [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Fare clic su **Usa società demo** per eseguire lo strumento Esplora grafico sui dati di un tenant di esempio. Per usare la società demo non sono necessarie credenziali. In alternativa, è possibile fare clic su **Accedi** ed effettuare l'accesso con le credenziali dell'account di Azure AD per eseguire lo strumento Esplora grafico sul tenant. Se si esegue Esplora grafico per il proprio tenant, l'utente o l'amministratore dovranno fornire il consenso in fase di accesso. Se si ha una sottoscrizione di Office 365, si ha automaticamente un tenant di Azure AD. Le credenziali usate per accedere a Office 365 sono, in realtà, account di Azure AD ed è possibile usare tali credenziali con Esplora grafico.

**Eseguire una query**: per eseguire una query, digitarla nell'apposita casella di richiesta e fare clic su **OTTIENI** oppure premere il tasto **INVIO**. I risultati vengono visualizzati nella casella delle risposte. Ad esempio, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` elencherà tutti gli oggetti gruppo nella directory demo.

Si notino le limitazioni e le funzioni dello strumento Esplora grafico riportate di seguito:

* Funzionalità di completamento automatico in set di risorse. Per visualizzarla, fare clic su **Usa società demo** e quindi fare clic sulla casella di richiesta (dove viene visualizzato l'URL della società). È possibile selezionare un set di risorse dall'elenco a discesa.
* Supporta gli alias di indirizzamento "me" e "myorganization". Ad esempio, è possibile usare `https://graph.windows.net/me?api-version=1.6` per restituire l'oggetto utente dell'utente che ha effettuato l'accesso oppure `https://graph.windows.net/myorganization/users?api-version=1.6` per restituire tutti gli utenti nella directory corrente. Tenere presente che usando l'alias "me" viene restituito un errore per la società demo perché nessun utente connesso sta effettuando la richiesta.
* Una sezione di intestazioni di risposta. Può essere usata per consentire la risoluzione dei problemi che si verificano durante l'esecuzione di query.
* Un visualizzatore JSON per la risposta con funzionalità di espansione e compressione.
* Nessun supporto per la visualizzazione di una foto di anteprima.

## Uso di Fiddler per scrivere nella directory
Ai fini di questa guida rapida, è possibile usare il debugger Web Fiddler per esercitarsi a eseguire operazioni di scrittura sulla directory di Azure AD. Per altre informazioni e per l'installazione di Fiddler, vedere [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Nell'esempio seguente viene usato il debugger Web Fiddler per creare un nuovo gruppo di sicurezza 'MyTestGroup' nella directory di Azure AD.

**Ottenere un token di accesso**: per accedere ad Azure AD Graph, è necessario che i client vengano prima correttamente autenticati in Azure AD. Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md).

**Creare ed eseguire una query**: completare i passaggi seguenti.

1. Aprire il debugger Web Fiddler e passare alla scheda **Composer**.
2. Poiché si desidera creare un nuovo gruppo di sicurezza, scegliere **Post** come metodo HTTP dal menu a discesa. Per altre informazioni sulle operazioni e le autorizzazioni per un oggetto gruppo, vedere [Gruppo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) in [ Riferimento all'API REST Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Nel campo accanto a **Post**, come URL della richiesta, digitare: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.
   
   > [!NOTE]
   > È necessario sostituire mytenantdomain con il nome di dominio della directory di Azure AD.
   > 
   > 
4. Nel campo appena sotto il menu a discesa Post digitare:
   
    ```
   Host: graph.windows.net
   Authorization: your access token
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Sostituire &lt;your access token&gt; con il token di accesso per la directory di Azure AD.
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

## Passaggi successivi
* Altre informazioni su [API di Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Per altre informazioni, vedere [Ambiti di autorizzazione dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

<!---HONumber=AcomDC_0921_2016-->