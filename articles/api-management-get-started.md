<properties 
	pageTitle="Introduzione a Gestione API di Azure" 
	description="Informazioni su come creare API e operazioni e su come iniziare a usare Gestione API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Introduzione a Gestione API di Azure

Questa guida illustra le procedure per iniziare subito a usare Gestione API ed effettuare la prima chiamata API.

## Contenuto dell'argomento

-   [Creare un'istanza di Gestione API][Creare un'istanza di Gestione API]
-   [Creare un'API][Creare un'API]
-   [Aggiungere un'operazione][Aggiungere un'operazione]
-   [Aggiungere la nuova API a un prodotto][Aggiungere la nuova API a un prodotto]
-   [Sottoscrivere il prodotto che contiene l'API][Sottoscrivere il prodotto che contiene l'API]
-   [Chiamare un'operazione dal portale per sviluppatori][Chiamare un'operazione dal portale per sviluppatori]
-   [Visualizzare l'analisi][Visualizzare l'analisi]
-   [Passaggi successivi][Passaggi successivi]

## <a name="create-service-instance"> </a>Creare un'istanza di Gestione API

> Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

Per poter usare Gestione API, occorre innanzitutto creare un'istanza del servizio. Accedere al [portale di gestione][portale di gestione] e fare clic su **Nuovo**, **Servizi app**, **Gestione API**, **Crea**.

![API Management new instance][API Management new instance]

Per **URL** specificare un nome univoco di sottodominio da usare per l'URL del servizio.

Specificare i valori desiderati di **Livello di prezzo**, **Sottoscrizione** e **Area** per l'istanza del servizio. Per questa esercitazione si possono usare tutti i livelli di prezzo. Una volta effettuate le selezioni, fare clic sul pulsante Avanti.

![New API Management service][New API Management service]

Immettere **Contoso Ltd.** in **Nome organizzazione** e specificare l'indirizzo di posta elettronica nel campo Indirizzo di posta elettronica dell'amministratore.

> Questo indirizzo di posta elettronica viene usato per le notifiche inviate dal sistema Gestione API. Per altre informazioni, vedere [Configurare le notifiche][Configurare le notifiche].

Selezionare la casella di controllo per creare l'istanza del servizio.

![New API Management service][1]

![New API Management service][2]

Dopo aver creato l'istanza del servizio, l'operazione successiva consiste nel creare un'API.

## <a name="create-api"> </a>Creare un'API

Un'API rappresenta un set di operazioni che possono essere richiamate da un'applicazione client. Le operazioni API vengono trasmesse tramite proxy ai servizi Web esistenti.

Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo di esempio che restituisce l'input ad essa inviata. Per usarla, è possibile richiamare un qualsiasi verbo HTTP. Il valore restituito sarà uguale alle intestazioni e al corpo inviati.

In questa esercitazione viene usato il servizio Web <http://echoapi.cloudapp.net/api> per creare in Gestione API una nuova API denominata **My Echo Service**.

Le API vengono create e configurate dalla console di Gestione API, accessibile tramite il portale di gestione di Azure. Per passare alla console di Gestione API, fare clic su **Console di gestione** nel portale di Azure per il servizio Gestione API.

![New API Management console][New API Management console]

Per creare l'**API My Echo**, fare clic su **API** nel menu **Gestione API** sulla sinistra, quindi scegliere **Aggiungi API**.

![Create API][Create API]

![Add new API][Add new API]

Per configurare la nuova API, vengono usati i tre campi seguenti.

-   Digitare **My Echo API** nella casella di testo **Titolo API Web**. La casella **Titolo API Web** consente di specificare un nome univoco e descrittivo per l'API, che viene visualizzato nel portale di gestione e nel portale per sviluppatori.
-   Digitare **<http://echoapi.cloudapp.net/api>** nella casella di testo **URL del servizio Web**. Il valore specificato nella casella **URL del servizio Web** fa riferimento al servizio HTTP che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste.
-   Digitare **myecho** nella casella di testo **Suffisso dell'URL dell'API Web**. Il valore della casella **Suffisso dell'URL dell'API Web** viene aggiunto all'URL di base del servizio Gestione API. Le API condividono un URL di base comune al quale viene aggiunto un suffisso univoco che consente di distinguerle.

Fare clic su **Salva** per creare l'API. Una volta creata la nuova API, la pagina di riepilogo dell'API viene visualizzata nel portale di gestione.

![API summary][API summary]

La sezione API comprende quattro schede. Nella scheda **Riepilogo** sono visualizzate le informazioni e le metriche di base sull'API. La scheda **Impostazioni** consente di visualizzare e modificare la configurazione di un'API, incluse le credenziali di autenticazione per il servizio back-end. La scheda **Operazioni** consente di gestire le operazioni dell'API e viene usata nel passaggio successivo di questa esercitazione; la scheda **Problemi** può invece essere usata per visualizzare i problemi segnalati dagli sviluppatori che usano le API.

> Nell'API Echo di esempio non viene usata l'autenticazione, ma per altre informazioni sulla configurazione dell'autenticazione, vedere [Configurare le impostazioni dell'API][Configurare le impostazioni dell'API].

Dopo aver creato un'API e avere configurato le impostazioni, il passaggio successivo consiste nell'aggiungere le operazioni all'API. Le definizioni delle operazioni vengono usate per convalidare le richieste in arrivo e generare automaticamente la documentazione.

## <a name="add-operation"> </a>Aggiungere un'operazione

Fare clic su **Operazioni** per visualizzare il riquadro operazioni per l'API. Il riquadro non contiene operazioni dal momento che non ne sono ancora state aggiunte.

![Operations][Operations]

Fare clic su **aggiungi operazione** per aggiungere una nuova operazione. Verrà visualizzata la finestra **Nuova operazione** in cui la scheda **Firma** è visualizzata per impostazione predefinita.

![Operation signature][Operation signature]

In questo esempio verrà specificata un'operazione GET per il servizio Echo. Immettere i valori seguenti nei campi della scheda **Firma**.

-   Digitare **GET** nella casella **Verbo HTTP**. Quando si inizia a digitare, è possibile selezionare **GET** nell'elenco di verbi HTTP visualizzato.
-   Digitare **/resource** nella casella di testo **Modello di URL**.
-   Digitare **GET resource** nella casella di testo **Nome visualizzato**.
-   Digitare **A demonstration of a GET call on a sample resource. It is handled by an "echo" backend which returns a response equal to the request (the supplied headers and body are being returned as received).** nella casella di testo **Descrizione**. Questa descrizione viene usata per generare la documentazione per questa operazione quando gli sviluppatori usano l'API.

Fare clic su **Parametri** per configurare i parametri di query di tipo stringa per l'operazione. In questo esempio vengono usati due parametri di stringhe di query. Per aggiungere un parametro di query, fare clic su **Aggiungi parametro di query** e specificare i seguenti valori.

Per il primo parametro di query configurare i valori seguenti.

-   Digitare **param1** nella casella di testo **Nome**.
-   Digitare **A sample parameter that is required.** nella casella di testo **Descrizione**.
-   Fare clic nel campo **Tipo** e selezionare **string** nell'elenco. I tipi supportati sono **string**, **number**, **boolean** e **dateTime**.
-   Fare clic nel campo **Valori**, digitare **sample** nella casella di testo e fare clic sul segno più per aggiungere il testo del valore predefinito al parametro. Dopo aver aggiunto il testo predefinito, fare clic in un punto qualsiasi all'esterno del campo **Valori** per chiudere la finestra di aggiunta valori.
-   Selezionare la casella di controllo **Obbligatorio**.

Per il secondo parametro di query immettere i valori seguenti.

-   **Name**: **param2**
-   **Descrizione**: **Another sample parameter, set to not required.**
-   **Tipo**: **number**

È consigliabile fornire esempi di risposte per tutti i codici di stato restituiti dall'operazione. Per ogni codice di stato può esistere più di un esempio di corpo della risposta, ovvero uno per ogni tipo di contenuto supportato. In questa esercitazione verrà aggiunto il codice di risposta **200 OK**.

Fare clic su **Aggiungi** nella sezione Risposte, iniziare a digitare **200** nella casella di testo e quindi selezionare **200 OK** nell'elenco a discesa.

![Add response][Add response]

Dopo aver selezionato **200 OK**, all'operazione verrà aggiunto un nuovo codice di risposta e verrà visualizzata la finestra della risposta. Digitare **Returned in all cases.** nella casella di testo **Descrizione**.

![Add response][3]

> Per configurare le risposte in più rappresentazioni, viene usato **Aggiungi rappresentazione**. Per altre informazioni, vedere [Risposte][Risposte].

Fare clic su **Salva** per aggiungere la nuova operazione configurata all'API.

## <a name="add-api-to-product"> </a>Aggiungere la nuova API a un prodotto

Prima di effettuare chiamate API gli sviluppatori devono effettuare la sottoscrizione di un prodotto. I prodotti offrono l'accesso a una o più API e possono prevedere restrizioni relative all'accesso, come quote di utilizzo e limiti di velocità. In questo passaggio dell'esercitazione l'API My Echo verrà aggiunta a un prodotto esistente.

Fare clic su **Prodotti** nel menu **Gestione API** sulla sinistra per visualizzare e configurare i prodotti disponibili in questa istanza dell'API.

![Products][Products]

Per impostazione predefinita, con ogni istanza di Gestione API vengono forniti due prodotti di esempio:

-   **Starter**
-   **Senza limiti**

In questa esercitazione verrà usato il prodotto **Starter**. Fare clic su **Starter** per visualizzare le impostazioni, incluse le API associate al prodotto.

![Add API][Add API]

Fare clic su **Aggiungi API al prodotto**.

![Add API][4]

Selezionare la casella **API My Echo** e fare clic su **Salva**.

![API added][API added]

A questo punto, l'**API My Echo** è associata a un prodotto e gli sviluppatori possono effettuare la sottoscrizione per iniziare a usare l'API.

> In questo passaggio dell'esercitazione viene usato il prodotto Starter, che viene fornito preconfigurato e pronto per l'uso. Per una guida dettagliata sulla creazione e la pubblicazione di un nuovo prodotto, vedere [Come creare e pubblicare un prodotto][Come creare e pubblicare un prodotto].

## <a name="subscribe"> </a>Sottoscrivere il prodotto che contiene l'API

Per effettuare chiamate a un'API, gli sviluppatori devono prima sottoscrivere un prodotto che offre l'accesso all'API. La sottoscrizione dei prodotti può essere effettuata direttamente dagli sviluppatori nel portale per sviluppatori; in alternativa, gli amministratori possono sottoscrivere i prodotti per conto degli sviluppatori nella console di gestione. Dal momento che nei passaggi precedenti di questa esercitazione è stata creata un'istanza di Gestione API, per impostazione predefinita l'utente corrente è già amministratore, di conseguenza può effettuare la sottoscrizione di un account al prodotto **Starter**.

Fare clic su **Sviluppatori** nel menu **Gestione API** sulla sinistra per visualizzare e configurare gli sviluppatori in questa istanza del servizio.

![Developers][Developers]

Fare clic sul nome dello sviluppatore per configurare le impostazioni per l'utente, incluse le sottoscrizioni.

> In questo esempio verrà effettuata la sottoscrizione di uno sviluppatore il cui nome è Clayton Gragg. Se non sono stati creati account sviluppatore, è possibile sottoscrivere l'account amministratore. Per informazioni sulla creazione di account sviluppatore, vedere [Come gestire gli account sviluppatore in Gestione API di Azure][Come gestire gli account sviluppatore in Gestione API di Azure].

![Add subscription][Add subscription]

Fare clic su **Aggiungi sottoscrizione**.

![Add subscription][5]

Selezionare la casella **Starter** e fare clic su **Sottoscrivi**.

![Subscription added][Subscription added]

Una volta effettuata la sottoscrizione dell'account sviluppatore, è possibile chiamare le API del prodotto.

## <a name="call-operation"> </a>Chiamare un'operazione dal portale per sviluppatori

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questo passaggio dell'esercitazione verrà chiamato il metodo Get aggiunto all'**API My Echo**. Fare clic su **Portale per sviluppatori** nel menu in alto a destra del portale di gestione.

![Developer portal][Developer portal]

Fare clic su **API** nel menu superiore e quindi su **API My Echo** per visualizzare le operazioni disponibili.

![Developer portal][6]

Notare che vengono visualizzati la descrizione e i parametri aggiunti durante la creazione dell'operazione, che costituiscono la documentazione destinata agli sviluppatori che useranno l'operazione.

Fare clic su **GET su risorsa** e quindi su **Apri console**.

![Operation console][Operation console]

Immettere alcuni valori per i parametri, specificare la chiave sviluppatore e fare clic su **GET HTTP**.

![HTTP Get][HTTP Get]

Una volta richiamata un'operazione, nel portale per sviluppatori vengono visualizzati l'**URL richiesto** restituito dal servizio back-end, lo **Stato della risposta**, le **Intestazioni della risposta** e l'eventuale **Contenuto della risposta**.

![Response][Response]

## <a name="view-analytics"> </a>Visualizzare l'analisi

Per visualizzare l'analisi per l'**API My Echo**, tornare al portale di amministrazione selezionando **Gestisci** nel menu utente in alto a destra nel portale per sviluppatori.

![Manage][Manage]

La visualizzazione predefinita del portale di amministrazione è il dashboard, che offre una panoramica dell'istanza di Gestione API.

![Dashboard][Dashboard]

Passare il puntatore del mouse sul grafico dell'API My Echo per visualizzare le metriche specifiche relative all'utilizzo dell'API in un dato periodo di tempo.

> Se il grafico non contiene linee, tornare al portale per sviluppatori ed effettuare alcune chiamate all'API, attendere qualche secondo e quindi tornare al dashboard.

![Analytics][Analytics]

Fare clic su **Visualizza dettagli** per visualizzare la pagina di riepilogo per l'API, inclusa una versione più estesa delle metriche visualizzate.

![Summary][Summary]

Per report e metriche dettagliate fare clic su **Analisi** nel menu **Gestione API** sulla sinistra.

![Overview][Overview]

La sezione **Analisi** include le quattro schede seguenti.

-   **Riepilogo**: fornisce metriche complessive sull'utilizzo e l'integrità, oltre a indicare API, operazioni e prodotti più usati e gli sviluppatori più attivi.
-   **Utilizzo**: fornisce informazioni dettagliate sulle chiamate API e sulla larghezza di banda, inclusa una rappresentazione geografica.
-   **Integrità**: è incentrata sui codici di stato, sulle percentuali di operazioni sulla cache completate, sui tempi di risposta, oltre che sui tempi di risposta di API e servizi.
-   **Attività**: fornisce report che illustrano in dettaglio l'attività specifica in base a sviluppatore, prodotto, API e operazione.

## <a name="next-steps"> </a>Passaggi successivi

-   Vedere gli altri argomenti nell'esercitazione [Introduzione alla configurazione API avanzata][Introduzione alla configurazione API avanzata].

  [Creare un'istanza di Gestione API]: #create-service-instance
  [Creare un'API]: #create-api
  [Aggiungere un'operazione]: #add-operation
  [Aggiungere la nuova API a un prodotto]: #add-api-to-product
  [Sottoscrivere il prodotto che contiene l'API]: #subscribe
  [Chiamare un'operazione dal portale per sviluppatori]: #call-operation
  [Visualizzare l'analisi]: #view-analytics
  [Passaggi successivi]: #next-steps
  [versione di valutazione gratuita di Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a
  [portale di gestione]: https://manage.windowsazure.com/
  [API Management new instance]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [New API Management service]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Configurare le notifiche]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [New API Management console]: ./media/api-management-get-started/api-management-management-console.png
  [Create API]: ./media/api-management-get-started/api-management-create-api.png
  [Add new API]: ./media/api-management-get-started/api-management-add-new-api.png
  [API summary]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Configurare le impostazioni dell'API]: ../api-management-howto-create-apis/#configure-api-settings
  [Operations]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Operation signature]: ./media/api-management-get-started/api-management-operation-signature.png
  [Add response]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [Risposte]: ../api-management-howto-add-operations/#responses
  [Products]: ./media/api-management-get-started/api-management-list-products.png
  [Add API]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [API added]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Come creare e pubblicare un prodotto]: ../api-management-howto-add-products
  [Developers]: ./media/api-management-get-started/api-management-developers.png
  [Come gestire gli account sviluppatore in Gestione API di Azure]: ../api-management-howto-create-or-invite-developers/
  [Add subscription]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Subscription added]: ./media/api-management-get-started/api-management-subscription-added.png
  [Developer portal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operation console]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Response]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Manage]: ./media/api-management-get-started/api-management-manage-menu.png
  [Dashboard]: ./media/api-management-get-started/api-management-dashboard.png
  [Analytics]: ./media/api-management-get-started/api-management-mouse-over.png
  [Summary]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Overview]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Introduzione alla configurazione API avanzata]: ../api-management-get-started-advanced

<!--HONumber=46--> 
