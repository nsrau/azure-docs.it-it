<properties pageTitle="How to create and configure advanced product settings in Azure API Management" metaKeywords="" description="Learn how to configure a product with quota and rate limit policies." metaCanonical="" services="" documentationCenter="API Management" title="How to create and configure advanced product settings in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Come creare e configurare le impostazioni avanzate del prodotto in Gestione API di Azure

In Gestione API di Azure (anteprima) i prodotti sono altamente configurabili allo scopo di soddisfare i requisiti degli autori di API. Questo argomento illustra come configurare alcune delle impostazioni avanzate del prodotto, inclusi i criteri relativi a limiti di frequenza e alle quote.

In questa esercitazione verrà creato e pubblicato un prodotto Free Trial che consente di effettuare fino a 10 chiamate al minuto per un massimo di 200 chiamate alla settimana e ne verranno verificati i criteri relativi ai limiti di frequenza.

## Contenuto dell'argomento

-   [Creare un prodotto][]
-   [Aggiungere un'API al prodotto][]
-   [Configurare i criteri relativi a limiti di frequenza e quota delle chiamate][]
-   [Pubblicare il prodotto][]
-   [Sottoscrivere un account per sviluppatore al prodotto][]
-   [Chiamare un'operazione e testare il limite di frequenza][]
-   [Passaggi successivi][]

## <a name="create-product"> </a>Creare un prodotto

In questo passaggio si creerà un prodotto Free Trial che non richiede l'approvazione della sottoscrizione.

Per iniziare, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API. Verrà visualizzato il portale amministrativo di Gestione API.

> Se non è stato creata un'istanza del servizio Gestione API, vedere [Creare un'istanza del servizio Gestione API][] nell'esercitazione [Introduzione a Gestione API di Azure][].

![API Management console][]

Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra per visualizzare la pagina **Prodotti**.

![Add product][]

Fare clic su **aggiungi prodotto** per visualizzare la finestra popup **Aggiungi nuovo prodotto**.

![Add new product][]

Digitare **Free Trial** nella casella di testo **Titolo**.

Digitare **Subscribers will be able to run 10 calls/minute up to a maximum of 200 calls/week after which access is denied.** nella casella di testo **Descrizione**.

Se si preferisce che i tentativi di sottoscrizione al prodotto vengano esaminati e quindi accettati o rifiutati da un amministratore, selezionare **Richiedi approvazione della sottoscrizione**. Se la casella è deselezionata, i tentativi di sottoscrizione verranno approvati automaticamente. In questo esempio le sottoscrizioni vengono approvate automaticamente, per cui non selezionare la casella.

Dopo aver immesso tutti i valori, fare clic su **Salva** per creare il prodotto.

![Product added][]

Per impostazione predefinita, i nuovi prodotti sono visibili agli utenti nel gruppo **Amministratori**. Verrà ora aggiunto il gruppo **Sviluppatori**. Fare clic su **Free Trial** e selezionare la scheda **Visibilità**.

> In Gestione API i gruppi permettono di gestire quali prodotti sono visibili per gli sviluppatori. I prodotti garantiscono la visibilità ai gruppi e gli sviluppatori possono visualizzare ed effettuare la sottoscrizione ai prodotti visibili ai gruppi ai quali appartengono. Per altre informazioni, vedere [Come creare e usare i gruppi in Gestione API di Azure][].

![Add developers group][]

Selezionare il gruppo **Sviluppatori** e fare clic su **Salva**.

## <a name="add-api"> </a>Aggiungere un'API al prodotto

In questo passaggio dell'esercitazione si aggiungerà l'API My Echo al nuovo prodotto Free Trial.

> Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. Per altre informazioni, vedere [Introduzione a Gestione API di Azure][].

Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra e scegliere **Free Trial** per configurare il prodotto.

![Configure product][]

Fare clic su **Aggiungi API al prodotto**.

![Add API to product][]

Selezionare la casella accanto a **API Echo** e fare clic su **Salva**.

![Add Echo API][]

## <a name="policies"> </a>Configurare i criteri relativi a limiti di frequenza e quota delle chiamate

I limiti di frequenza e le quote vengono configurate nell'editor dei criteri. Fare clic su **Criteri** sotto il menu **Gestione API** sulla sinistra e selezionare **Free Trial** nell'elenco a discesa **Prodotto in Ambito criteri**.

![Product policy][]

Fare clic su **Aggiungi criteri** per importare il modello dei criteri e iniziare a creare i criteri relativi a limiti di frequenza e quota.

![Add policy][]

Per inserire criteri, posizionare il cursore nella sezione **inbound** o **outbound** del modello dei criteri. I criteri relativi a limiti di frequenza e quota sono criteri in ingresso, di conseguenza posizionare il cursore nell'elemento in ingresso.

![Policy editor][]

I due criteri che verranno aggiunti in questa esercitazione sono **Limit call rate** e **Set usage quota**.

![Policy statements][]

Dopo aver posizionato il cursore nell'elemento dei criteri **inbound**, fare clic sulla freccia accanto a **Limit call rate** per inserire il modello dei criteri.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

È possibile usare **Limit call rate** a livello di prodotto, oltre che a livello di nome di singola operazione e di API. In questa esercitazione vengono usati solo criteri a livello di prodotto, quindi eliminare gli elementi **api** e **operation** dall'elemento **rate-limit**, come illustrato nell'esempio seguente.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Nel prodotto **Free Trial** la frequenza massima consentita è pari a 10 chiamate al minuto. Digitare quindi **10** come valore dell'attributo calls e **60** per l'attributo **renewal-period**.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Per configurare i criteri **Set usage quota**, posizionare il cursore immediatamente sotto il nuovo elemento **rate-limit** aggiunto nell'elemento **inbound** e fare clic sulla freccia a sinistra di **Set usage quota**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Dal momento che questi criteri devono essere intesi anche a livello di prodotto, eliminare gli elementi name di **api** e **operation**, come illustrato nell'esempio seguente.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Le quote possono essere basate sul numero di chiamate per intervallo, larghezza di banda o entrambi. In questa esercitazione non verrà applicata la limitazione in base alla larghezza di banda, di conseguenza eliminare l'attributo **bandwidth**.

    <quota calls="number" renewal-period="seconds">
    </quota>

Nel prodotto **Free Trial** la quota è pari a 200 chiamate alla settimana. Specificare **200** come valore dell'attributo calls e **604800** come valore di renewal-period.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> Gli intervalli dei criteri sono specificati in secondi. Per calcolare l'intervallo per una settimana, è possibile moltiplicare il numero di giorni (7) per il numero di ore in una giornata (24) per il numero di minuti in un'ora (60) per il numero di secondi in un minuto (60). 7 \* 24 \* 60 \* 60 = 604800.

Un volta completata la configurazione, i criteri dovrebbero essere simili a quelli dell'esempio seguente.

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

Dopo avere configurato i criteri desiderati, fare clic su **Salva**.

![Save policy][]

## <a name="publish-product"> </a>Pubblicare il prodotto

A questo punto dopo aver aggiunto le API e aver configurato i criteri, il prodotto è pronto per essere usato dagli sviluppatori. Prima di poter essere usato, però il prodotto deve essere pubblicato. Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra e scegliere **Free Trial** per configurare il prodotto.

![Configure product][]

Fare clic su **Pubblica**, quindi su **Sì, pubblica** per confermare.

![Publish product][]

## <a name="subscribe-account"> </a>Sottoscrivere un account per sviluppatore al prodotto

Una volta pubblicato, il prodotto è disponibile per essere sottoscritto e usato dagli sviluppatori.

> Gli amministratori di un'istanza di Gestione API sono automaticamente sottoscritti a tutti i prodotti. In questo passaggio dell'esercitazione si sottoscriverà uno degli account per sviluppatori non amministratori al prodotto Free Trial. Se l'account per sviluppatori fa parte del ruolo Amministratori, è possibile procedere con questo passaggio anche se si è già effettuata la sottoscrizione.

Fare clic su **Sviluppatori** nel menu **Gestione API** sulla sinistra e fare clic sul nome dell'account per sviluppatori. In questo esempio verrà usato l'account per sviluppatori **Clayton Gragg**.

![Configure developer][]

Fare clic su **Aggiungi sottoscrizione**.

![Add subscription][]

Selezionare la casella accanto a **Free Trial** e fare clic su **Sottoscrivi**.

![Add subscription][1]

## <a name="test-rate-limit"> </a>Chiamare un'operazione e testare il limite di frequenza

A questo punto, dopo aver configurato e pubblicato il prodotto Free Trial, è possibile chiamare alcune operazioni e testare i criteri relativi ai limiti di frequenza. Passare al portale per sviluppatori facendo clic su **Portale per sviluppatori** nel menu in alto a destra.

![Developer portal][]

Fare clic su **API** nel menu superiore e scegliere **API Echo**.

![Developer portal][2]

> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.

Selezionare l'operazione **GET su risorsa** e fare clic su **Apri console**.

![Open console][]

Mantenere i valori predefiniti dei parametri e selezionare la chiave della sottoscrizione per il prodotto **Free Trial**.

![Subscription key][]

> Se si hanno più sottoscrizioni, assicurarsi di selezionare la chiave per **Free Trial**, altrimenti i criteri configurati nei passaggi precedente non avranno effetto.

Fare clic su **GET HTTP** e visualizzare la risposta. Notare che il valore di **Stato della risposta** è **200 OK**.

![Operation results][]

Fare clic su **GET HTTP** a una frequenza maggiore di quella dei criteri relativi ai limiti di frequenza pari a 10 chiamate al minuto. Una volta superati i criteri dei limiti di frequenza, viene restituito lo stato di risposta **429 Troppe richieste**.

![Operation results][3]

In **Intestazioni della risposta** e **Contenuto della risposta** è indicato che l'intervallo residuo prima dei nuovi tentativi avrà esito positivo.

Quando sono attivi i criteri dei limiti di frequenza pari a 10 chiamati al minuto, le chiamate successive non verranno effettuate finché non trascorrono 60 secondi dalla prima delle 10 chiamate riuscite al prodotto precedenti al superamento del limite. In questo esempio l'intervallo residuo è di 43 secondi.

## <a name="next-steps"> </a>Passaggi successivi

-   Vedere gli altri argomenti nell'esercitazione [Introduzione alla configurazione API avanzata][].

  [Creare un prodotto]: #create-product
  [Aggiungere un'API al prodotto]: #add-api
  [Configurare i criteri relativi a limiti di frequenza e quota delle chiamate]: #policies
  [Pubblicare il prodotto]: #publish-product
  [Sottoscrivere un account per sviluppatore al prodotto]: #subscribe-account
  [Chiamare un'operazione e testare il limite di frequenza]: #test-rate-limit
  [Passaggi successivi]: #next-steps
  [Creare un'istanza del servizio Gestione API]: ../api-management-get-started/#create-service-instance
  [Introduzione a Gestione API di Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Add product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Add new product]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Product added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Come creare e usare i gruppi in Gestione API di Azure]: ../api-management-howto-create-groups
  [Add developers group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configure product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Add API to product]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Add Echo API]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Product policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Add policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Policy editor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Policy statements]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Save policy]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publish product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configure developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Add subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Developer portal]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Open console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Subscription key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operation results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced
