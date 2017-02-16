---
title: Controlli pagina in Gestione API di Azure | Microsoft Docs
description: Informazioni sui controlli pagina disponibili per l&quot;uso nei modelli del portale per sviluppatori in Gestione API di Azure.
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 03e0ac8d-64ff-4e9a-b029-d7be14fb31e3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: 925aff209056f5a63b927dcb95ef6621967a0ffb

---
# <a name="azure-api-management-page-controls"></a>Controlli pagina in Gestione API di Azure
Gestione API di Azure mette a disposizione i seguenti controlli per l'uso nei modelli del portale per sviluppatori.  
  
 Per usare un controllo, inserirlo nella posizione desiderata nel modello del portale per sviluppatori. Alcuni controlli, ad esempio [app-actions](#app-actions), dispongono di parametri, come mostrato nell'esempio seguente.  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 I valori per i parametri vengono passati come parte del modello di dati per il modello. Nella maggior parte dei casi, basta incollare ciascun controllo nell'esempio fornito e funzionerà correttamente. Per ulteriori informazioni sui valori di parametro, è possibile vedere la sezione sul modello dei dati per ciascun modello in cui potrebbe essere usato un controllo.  
  
 Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Controlli pagina per i modelli nel portale per sviluppatori  
  
-   [app-actions](#app-actions)  
  
-   [basic-signin](#basic-signin)  
  
-   [paging-control](#paging-control)  
  
-   [provider](#providers)  
  
-   [search-control](#search-control)  
  
-   [sign-up](#sign-up)  
  
-   [subscribe-button](#subscribe-button)  
  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="a-nameapp-actionsa-app-actions"></a><a name="app-actions"></a> app-actions  
 Il controllo `app-actions` offre un'interfaccia utente per interagire con le applicazioni nella pagina del profilo utente nel portale per sviluppatori.  
  
 ![Controllo app&#45;actions](./media/api-management-page-controls/APIM-app-actions-control.png "Controllo app-actions in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>parameters  
  
|Parametro|Descrizione|  
|---------------|-----------------|  
|appId|L'ID dell'applicazione.|  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `app-actions` nei seguenti modelli del portale per sviluppatori.  
  
-   [Applicazioni](api-management-user-profile-templates.md#Applications)  
  
##  <a name="a-namebasic-signina-basic-signin"></a><a name="basic-signin"></a> basic-signin  
 Il controllo `basic-signin` permette di raccogliere le informazioni di accesso degli utenti nella pagina di accesso del portale per sviluppatori.  
  
 ![Controllo basic&#45;signin](./media/api-management-page-controls/APIM-basic-signin-control.png "Controllo basic-signin in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `basic-signin` nei seguenti modelli del portale per sviluppatori.  
  
-   [Accesso](api-management-page-templates.md#SignIn)  
  
##  <a name="a-namepaging-controla-paging-control"></a><a name="paging-control"></a> paging-control  
 Il controllo `paging-control` offre funzionalità di paging nelle pagine del portale per sviluppatore che presentano un elenco di elementi.  
  
 ![paging-control](./media/api-management-page-controls/APIM-paging-control.png "paging-control in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `paging-control` nei seguenti modelli del portale per sviluppatori.  
  
-   [Elenco API](api-management-api-templates.md#APIList)  
  
-   [Elenco di problemi](api-management-issue-templates.md#IssueList)  
  
-   [Elenco dei prodotti](api-management-product-templates.md#ProductList)  
  
##  <a name="a-nameprovidersa-providers"></a><a name="providers"></a> providers  
 Il controllo `providers` permette di selezionare i provider di autenticazione nella pagina di accesso del portale per sviluppatori.  
  
 ![Controllo providers](./media/api-management-page-controls/APIM-providers-control.png "Controllo providers in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `providers` nei seguenti modelli del portale per sviluppatori.  
  
-   [Accesso](api-management-page-templates.md#SignIn)  
  
##  <a name="a-namesearch-controla-search-control"></a><a name="search-control"></a> search-control  
 Il controllo `search-control` offre funzionalità di ricerca nelle pagine del portale per sviluppatore che presentano un elenco di elementi.  
  
 ![search- control](./media/api-management-page-controls/APIM-search-control.png "search-control in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `search-control` nei seguenti modelli del portale per sviluppatori.  
  
-   [Elenco API](api-management-api-templates.md#APIList)  
  
-   [Elenco dei prodotti](api-management-product-templates.md#ProductList)  
  
##  <a name="a-namesign-upa-sign-up"></a><a name="sign-up"></a> sign-up  
 Il controllo `sign-up` permette di raccogliere le informazioni di profilo degli utenti nella pagina di iscrizione del portale per sviluppatori.  
  
 ![Controllo sign&#45;up](./media/api-management-page-controls/APIM-sign-up-control.png "Controllo sign-up in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `sign-up` nei seguenti modelli del portale per sviluppatori.  
  
-   [Iscrizione](api-management-page-templates.md#SignUp)  
  
##  <a name="a-namesubscribe-buttona-subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 Il controllo `subscribe-button` consente di sottoscrivere un utente a un prodotto.  
  
 ![Controllo subscribe&#45;button](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controllo subscribe-button in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>parameters  
 Nessuna.  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `subscribe-button` nei seguenti modelli del portale per sviluppatori.  
  
-   [Prodotto](api-management-product-templates.md#Product)  
  
##  <a name="a-namesubscription-cancela-subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 Il controllo `subscription-cancel` consente di annullare la sottoscrizione a un prodotto nella pagina di profilo dell'utente nel portale per sviluppatori.  
  
 ![Controllo subscription&#45;cancel](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Controllo subscription-cancel in Gestione API di Azure")  
  
### <a name="usage"></a>Utilizzo  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>parameters  
  
|Parametro|Descrizione|  
|---------------|-----------------|  
|subscriptionId|L'ID della sottoscrizione da annullare.|  
|cancelUrl|L'URL di annullamento della sottoscrizione.|  
  
### <a name="developer-portal-templates"></a>Modelli del portale per sviluppatori  
 È possibile usare il controllo `subscription-cancel` nei seguenti modelli del portale per sviluppatori.  
  
-   [Prodotto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'uso dei modelli, vedere [Come personalizzare il portale per sviluppatori di Gestione API usando i modelli](api-management-developer-portal-templates.md).


<!--HONumber=Jan17_HO2-->


