---
title: Proteggere l'API con Gestione API di Azure | Microsoft Azure
description: Informazioni su come proteggere l'API con criteri di quota e limitazione (limitazione della frequenza).
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 5553bcb8f9fd38630f694151dc644a684266387c
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Proteggere le API con limiti di frequenza usando Gestione API di Azure
Questa guida illustra quanto sia semplice aggiungere la protezione all'API back-end configurando il limite di frequenza e i criteri di quota con Gestione API di Azure.

In questa esercitazione si creerà un prodotto API "Free Trial" che consente agli sviluppatori di eseguire fino a 10 chiamate al minuto e un massimo di 200 chiamate API alla settimana usando i criteri [Limit call rate per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) (Limita frequenza delle chiamate per sottoscrizione) e [Set usage quota per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) (Imposta quota di utilizzo per sottoscrizione). L'API verrà quindi pubblicata e verranno testati i criteri del limite di frequenza.

Per scenari di limitazione più avanzati che usano i criteri [rate-limit-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota-by-key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), vedere [Limitazione avanzata delle richieste con Gestione API di Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Per creare un prodotto
In questo passaggio si creerà un prodotto con una versione di valutazione gratuita che non richiede l'approvazione della sottoscrizione.

> [!NOTE]
> Se è già stato configurato un prodotto da usare in questa esercitazione, è possibile passare direttamente alla sezione [Configurare i criteri relativi a limiti di frequenza e quota delle chiamate][Configure call rate limit and quota policies] e seguire l'esercitazione da quel punto, usando il proprio prodotto al posto del prodotto Free Trial.
> 
> 

Per iniziare, fare clic sul **portale di pubblicazione** nel Portale di Azure relativo al servizio Gestione API.

![Portale di pubblicazione][api-management-management-console]

> Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Gestire la prima API in Gestione API di Azure][Manage your first API in Azure API Management].
> 
> 

Scegliere **Prodotti** dal menu **Gestione API** sulla sinistra per visualizzare la pagina **Prodotti**.

![Add product][api-management-add-product]

Fare clic su **Aggiungi prodotto** (Aggiungi prodotto) per visualizzare la finestra di dialogo **Add new product** (Aggiungi nuovo prodotto).

![Aggiungi nuovo prodotto][api-management-new-product-window]

Nella casella **Titolo** digitare **Free Trial**.

Digitare **I sottoscrittori potranno eseguire 10 chiamate al minuto per un massimo di 200 chiamate alla settimana, dopodiché l'accesso verrà negato** nella casella di testo **Descrizione**.

Lo stato dei prodotti in Gestione API può essere Aperto o Protetto. Per poter usare i prodotti protetti, è necessario eseguire la sottoscrizione, mentre i prodotti aperti possono essere usati senza sottoscrizione. Per creare un prodotto protetto che richieda una sottoscrizione, assicurarsi che l'opzione **Require subscription** (Richiedi sottoscrizione) sia selezionata. Questa è l'impostazione predefinita.

Se si preferisce che i tentativi di sottoscrizione del prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore, selezionare **Richiedi approvazione della sottoscrizione**. Se la casella di controllo è deselezionata, i tentativi di sottoscrizione verranno approvati automaticamente. In questo esempio le sottoscrizioni vengono approvate automaticamente, quindi non selezionare la casella.

Per consentire agli account per sviluppatori di sottoscrivere più volte il nuovo prodotto, selezionare la casella di controllo **Consenti più sottoscrizioni simultanee** . Questa esercitazione non prevede l'uso di più sottoscrizioni simultanee, quindi lasciare la casella deselezionata.

Dopo aver immesso tutti i valori, fare clic su **Salva** per creare il prodotto.

![Product added][api-management-product-added]

Per impostazione predefinita, i nuovi prodotti sono visibili agli utenti nel gruppo **Amministratori** . Verrà ora aggiunto il gruppo **Sviluppatori** . Fare clic su **Free Trial** e fare clic sulla scheda **Visibilità**.

> In Gestione API i gruppi permettono di gestire quali prodotti sono visibili per gli sviluppatori. I prodotti garantiscono la visibilità ai gruppi e gli sviluppatori possono visualizzare ed effettuare la sottoscrizione ai prodotti visibili ai gruppi ai quali appartengono. Per altre informazioni, vedere [Come creare e usare i gruppi in Gestione API di Azure][How to create and use groups in Azure API Management].
> 
> 

![Add developers group][api-management-add-developers-group]

Selezionare la casella di controllo **Sviluppatori** e quindi fare clic su **Salva**.

## <a name="add-api"> </a>Per aggiungere un'API al prodotto
In questo passaggio dell'esercitazione si aggiungerà l'API My Echo al nuovo prodotto Free Trial.

> Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. Per altre informazioni, vedere [Gestire la prima API in Gestione API di Azure][Manage your first API in Azure API Management].
> 
> 

Scegliere **Prodotti** dal menu **Gestione API** a sinistra e quindi fare clic su **Free Trial** per configurare il prodotto.

![Configure product][api-management-configure-product]

Fare clic su **Aggiungi API al prodotto**.

![Aggiungi API al prodotto][api-management-add-api]

Selezionare **Echo API** (API Echo) e quindi fare clic su **Salva**.

![Add Echo API][api-management-add-echo-api]

## <a name="policies"> </a>Per configurare i criteri relativi a limiti di frequenza e quota delle chiamate
I limiti di frequenza e le quote vengono configurate nell'editor dei criteri. In questa esercitazione verranno aggiunti i due criteri [Limit call rate per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) (Limita frequenza delle chiamate per sottoscrizione) e [Set usage quota per subscription](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) (Imposta quota di utilizzo per sottoscrizione). Questi criteri devono essere applicati nell'ambito del prodotto.

Scegliere **Criteri** dal menu **Gestione API** a sinistra. Nell'elenco **Prodotto** fare clic su **Free Trial**.

![Product policy][api-management-product-policy]

Fare clic su **Aggiungi criteri** per importare il modello dei criteri e iniziare a creare i criteri relativi a limiti di frequenza e quota.

![Aggiungi criteri][api-management-add-policy]

I criteri relativi a limiti di frequenza e quota sono criteri in ingresso, di conseguenza posizionare il cursore nell'elemento in ingresso.

![Policy editor][api-management-policy-editor-inbound]

Scorrere l'elenco dei criteri e individuare la voce del criterio **Limit call rate per subscription** (Limita frequenza delle chiamate per sottoscrizione).

![Policy statements][api-management-limit-policies]

Dopo aver posizionato il cursore nell'elemento dei criteri **inbound**, fare clic sulla freccia accanto a **Limit call rate per subscription** (Limita frequenza chiamate per sottoscrizione) per inserire il modello dei criteri corrispondente.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Come è possibile rilevare dal frammento, il criterio consente di impostare limiti per le operazioni e le API del prodotto. In questa esercitazione non verrà usata tale funzionalità, quindi eliminare gli elementi **api** e **operation** dall'elemento **rate-limit**, in modo che resti solo l'elemento **rate-limit** esterno, come illustrato nell'esempio seguente.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

Nel prodotto Free Trial la frequenza massima consentita è pari a 10 chiamate al minuto. Digitare quindi **10** come valore dell'attributo **calls** e **60** per l'attributo **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Per configurare il criterio **Set usage quota per subscription** (Imposta quota di utilizzo per sottoscrizione), posizionare il cursore immediatamente sotto il nuovo elemento **rate-limit** aggiunto nell'elemento **inbound** e quindi individuare la freccia a sinistra di **Set usage quota per subscription** (Imposta quota di utilizzo per sottoscrizione) e fare clic su di essa.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

Analogamente al criterio **Limit call rate per subscription** (Limita frequenza delle chiamate per sottoscrizione), il criterio **Set usage quota per subscription** (Imposta quota di utilizzo per sottoscrizione) consente di impostare limiti massimi per le operazioni e le API del prodotto. In questa esercitazione non verrà usata tale funzionalità, quindi eliminare gli elementi **api** e **operation** dall'elemento **quota**, come illustrato nell'esempio seguente.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

Le quote possono essere basate sul numero di chiamate per intervallo, larghezza di banda o entrambi. In questa esercitazione non verrà applicata la limitazione in base alla larghezza di banda, di conseguenza eliminare l'attributo **bandwidth** .

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

Nel prodotto Free Trial la quota è pari a 200 chiamate alla settimana. Specificare **200** come valore dell'attributo **calls** e **604800** come valore dell'attributo **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Gli intervalli dei criteri sono specificati in secondi. Per calcolare l'intervallo per una settimana, è possibile moltiplicare il numero di giorni (7) per il numero di ore in una giornata (24) per il numero di minuti in un'ora (60) per il numero di secondi in un minuto (60): 7 * 24 * 60 * 60 = 604800.
> 
> 

Una volta completata la configurazione, i criteri dovrebbero essere simili a quelli dell'esempio seguente.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Dopo avere configurato i criteri desiderati, fare clic su **Salva**.

![Save policy][api-management-policy-save]

## <a name="publish-product"> </a> Per pubblicare il prodotto
A questo punto dopo aver aggiunto le API e aver configurato i criteri, il prodotto deve essere pubblicato per poter essere usato dagli sviluppatori. Scegliere **Prodotti** dal menu **Gestione API** a sinistra e quindi fare clic su **Free Trial** per configurare il prodotto.

![Configure product][api-management-configure-product]

Fare clic su **Pubblica**, quindi su **Yes, publish it** (Sì, pubblica) per confermare.

![Pubblicazione prodotto][api-management-publish-product]

## <a name="subscribe-account"> </a>Per sottoscrivere un account per sviluppatore al prodotto
Una volta pubblicato, il prodotto è disponibile per essere sottoscritto e usato dagli sviluppatori.

> Gli amministratori di un'istanza di Gestione API sono automaticamente sottoscritti a tutti i prodotti. In questo passaggio dell'esercitazione verrà effettuata la sottoscrizione di uno degli account per sviluppatore non amministratore per il prodotto Free Trial. Se l'account per sviluppatore fa parte del ruolo Amministratori, è possibile procedere con questo passaggio anche se si è già effettuata la sottoscrizione.
> 
> 

Scegliere **Utenti** dal menu **Gestione API** a sinistra e fare clic sul nome dell'account per sviluppatore. In questo esempio verrà usato l'account per sviluppatore **Clayton Gragg** .

![Configure developer][api-management-configure-developer]

Fare clic su **Aggiungi sottoscrizione**.

![Aggiungi sottoscrizione][api-management-add-subscription-menu]

Selezionare **Free Trial** e quindi fare clic su **Sottoscrivi**.

![Aggiungi sottoscrizione][api-management-add-subscription]

> [!NOTE]
> In questa esercitazione non sono abilitate più sottoscrizioni simultanee per il prodotto con una versione di valutazione gratuita. Nel caso lo fossero, verrebbe richiesto di assegnare un nome alla sottoscrizione, come illustrato nell'esempio seguente.
> 
> 

![Aggiungi sottoscrizione][api-management-add-subscription-multiple]

Dopo aver fatto clic su **Sottoscrivi**, il prodotto verrà visualizzato nell'elenco **Sottoscrizione** dell'utente.

![Sottoscrizione aggiunta][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Per chiamare un'operazione e testare il limite di frequenza
A questo punto, dopo aver configurato e pubblicato il prodotto Free Trial, è possibile chiamare alcune operazioni e testare i criteri relativi ai limiti di frequenza.
Passare al portale per sviluppatori facendo clic su **Portale per sviluppatori** nel menu in alto a destra.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** nel menu in alto e quindi su **Echo API** (API Echo).

![Portale per sviluppatori][api-management-developer-portal-api-menu]

Fare clic su **GET Resource** (GET su risorsa) e quindi su **Prova**.

![Open console][api-management-open-console]

Mantenere i valori predefiniti dei parametri e selezionare la chiave della sottoscrizione per il prodotto con versione di valutazione gratuita.

![Chiave della sottoscrizione][api-management-select-key]

> [!NOTE]
> Se si hanno più sottoscrizioni, assicurarsi di selezionare la chiave per **Free Trial**, altrimenti i criteri configurati nei passaggi precedente non avranno effetto.
> 
> 

Fare clic su **Invia**e quindi visualizzare la risposta. Notare che il valore di **Stato della risposta** è **200 OK**.

![Operation results][api-management-http-get-results]

Fare clic su **Invia** a una frequenza maggiore di quella dei criteri relativi ai limiti di frequenza pari a 10 chiamate al minuto. Al superamento dei criteri dei limiti di frequenza, viene restituito lo stato di risposta **429 Troppe richieste** .

![Operation results][api-management-http-get-429]

In **Contenuto della risposta** è indicato che l'intervallo residuo prima dei nuovi tentativi avrà esito positivo.

Quando sono attivi i criteri dei limiti di frequenza pari a 10 chiamate al minuto, le chiamate successive non verranno effettuate finché non trascorrono 60 secondi dalla prima delle 10 chiamate riuscite al prodotto precedenti al superamento del limite. In questo esempio l'intervallo residuo è di 54 secondi.

## <a name="next-steps"> </a>Passaggi successivi
* Per una dimostrazione dell'impostazione dei limiti di frequenza e delle quote, vedere il video seguente.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

