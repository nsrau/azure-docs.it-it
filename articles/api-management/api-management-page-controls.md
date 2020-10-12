---
title: Controlli pagina in Gestione API di Azure | Microsoft Docs
description: Informazioni sui controlli pagina disponibili per l'uso nei modelli del portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b826c986a0af26bb79fb0823e4e8626f0165d460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86250023"
---
# <a name="azure-api-management-page-controls"></a>Controlli pagina in Gestione API di Azure
Gestione API di Azure mette a disposizione i seguenti controlli per l'uso nei modelli del portale per sviluppatori.  
  
Per usare un controllo, inserirlo nella posizione desiderata nel modello del portale per sviluppatori. Alcuni controlli, ad esempio [app-actions](#app-actions), dispongono di parametri, come mostrato nell'esempio seguente:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
I valori per i parametri vengono passati come parte del modello di dati per il modello. Nella maggior parte dei casi, basta incollare ciascun controllo nell'esempio fornito e funzionerà correttamente. Per ulteriori informazioni sui valori di parametro, è possibile vedere la sezione sul modello dei dati per ciascun modello in cui potrebbe essere usato un controllo.  

Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controlli pagina per i modelli nel portale per sviluppatori  
  
-   [app-azioni](#app-actions)  
-   [Basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [provider](#providers)  
-   [controllo di ricerca](#search-control)  
-   [Sign](#sign-up)  
-   [Sottoscrivi-pulsante](#subscribe-button)  
-   [sottoscrizione-Annulla](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 Il controllo `app-actions` offre un'interfaccia utente per interagire con le applicazioni nella pagina del profilo utente nel portale per sviluppatori.  
  
 ![controllo azioni&#45;app](./media/api-management-page-controls/APIM-app-actions-control.png "App gestione API-controllo azioni")  
  
### <a name="usage"></a>Uso  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametri  
  
|Parametro|Descrizione|  
|---------------|-----------------|  
|appId|ID dell'applicazione.|  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `app-actions` nei seguenti modelli del portale per sviluppatori:  
  
-   [Applicazioni](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 Il controllo `basic-signin` permette di raccogliere le informazioni di accesso degli utenti nella pagina di accesso del portale per sviluppatori.  
  
 ![controllo di base&#45;signin](./media/api-management-page-controls/APIM-basic-signin-control.png "GESTIONE API Basic-controllo signin")  
  
### <a name="usage"></a>Uso  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `basic-signin` nei seguenti modelli del portale per sviluppatori:  
  
-   [Accedi](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paging-controllo  
 Il controllo `paging-control` offre funzionalità di paging nelle pagine del portale per sviluppatore che presentano un elenco di elementi.  
  
 ![controllo paging](./media/api-management-page-controls/APIM-paging-control.png "Controllo paging gestione API")  
  
### <a name="usage"></a>Uso  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `paging-control` nei seguenti modelli del portale per sviluppatori:  
  
-   [Elenco delle API](api-management-api-templates.md#APIList)  
  
-   [Elenco di problemi](api-management-issue-templates.md#IssueList)  
  
-   [Elenco dei prodotti](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> provider  
 Il controllo `providers` permette di selezionare i provider di autenticazione nella pagina di accesso del portale per sviluppatori.  
  
 ![controllo provider](./media/api-management-page-controls/APIM-providers-control.png "Controllo provider gestione API")  
  
### <a name="usage"></a>Uso  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `providers` nei seguenti modelli del portale per sviluppatori:  
  
-   [Accedi](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 Il controllo `search-control` offre funzionalità di ricerca nelle pagine del portale per sviluppatore che presentano un elenco di elementi.  
  
 ![controllo di ricerca](./media/api-management-page-controls/APIM-search-control.png "Controllo di ricerca gestione API")  
  
### <a name="usage"></a>Uso  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `search-control` nei seguenti modelli del portale per sviluppatori:  
  
-   [Elenco delle API](api-management-api-templates.md#APIList)  
  
-   [Elenco dei prodotti](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> Sign  
 Il controllo `sign-up` permette di raccogliere le informazioni di profilo degli utenti nella pagina di iscrizione del portale per sviluppatori.  
  
 ![controllo&#45;di accesso](./media/api-management-page-controls/APIM-sign-up-control.png "Controllo di iscrizione gestione API")  
  
### <a name="usage"></a>Uso  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `sign-up` nei seguenti modelli del portale per sviluppatori:  
  
-   [Registrati](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 Il controllo `subscribe-button` consente di sottoscrivere un utente a un prodotto.  
  
 ![Sottoscrivi&#45;controllo Button](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controllo del pulsante di sottoscrizioni gestione API")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametri  
 No.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `subscribe-button` nei seguenti modelli del portale per sviluppatori:  
  
-   [Prodotto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 Il controllo `subscription-cancel` consente di annullare la sottoscrizione a un prodotto nella pagina di profilo dell'utente nel portale per sviluppatori.  
  
 ![controllo di annullamento della sottoscrizione&#45;](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Sottoscrizione di gestione API-controllo annulla")  
  
### <a name="usage"></a>Uso  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametri  
  
|Parametro|Descrizione|  
|---------------|-----------------|  
|subscriptionId|ID della sottoscrizione da annullare.|  
|cancelUrl|L'URL di annullamento della sottoscrizione.|  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `subscription-cancel` nei seguenti modelli del portale per sviluppatori:  
  
-   [Prodotto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).
