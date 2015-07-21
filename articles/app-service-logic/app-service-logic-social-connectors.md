<properties 
	pageTitle="Connettori per social network delle app per le API di Microsoft Azure | Microservizio app per le API" 
	description="Informazioni su come creare app per le API per i connettori per social network di Microsoft Azure e aggiungere l'app per le API all'app per la logica; microservizi" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="connectors" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="mandia"/>

# Connettori per social network nel servizio app di Microsoft Azure


## Cos'è un connettore app per le API per social network
I connettori per social network sono "app per le API" che possono connettersi alle applicazioni di social networking ed eseguire diverse *Azioni* per conto dell'utente autenticato. La maggior parte dei connettori può anche essere configurata con un *Trigger*. I trigger sono eventi (simili agli eventi in .NET Framework) che possono essere configurati in alcuni connettori. I trigger possono quindi iniziare le azioni che sono state configurate per il connettore (o qualsiasi altro connettore). Ad esempio, un'istanza del connettore Twitter può essere configurata con un *nuovo messaggio*, dove il trigger è definito come qualsiasi nuovo tweet che citi @VisualStudio. Questo trigger potrà quindi essere configurato in modo da iniziare un'*Azione* per fare il retweet del tweet.

I connettori per social network seguenti sono disponibili presso Microsoft:

- Facebook
- Twitter
- Chatter
- Yammer
- Twilio

Di seguito è riportata una breve descrizione di ogni connettore per social network:

<table>
<tr>
<th> Nome</th>
<th> Descrizione</th>
<th> Trigger</th>
<th>Actions</th>

<tr>
<td>Facebook
<td>Un connettore Facebook consente di pubblicare e leggere messaggi, commenti, eventi e stati dal feed di Facebook. È anche possibile pubblicare video e foto.
<td>Nuovo post
<td><li>Pubblicare post
	<li>Pubblicare foto 
</tr>

<tr>
<td>Twitter
<td>Un connettore Twitter consente di eseguire diverse operazioni, quali pubblicare, ricevere tweet e cercare tra i tweet. È anche possibile usare un connettore Twitter per stare al passo con le novità su Twitter, ricevere informazioni come tweet, timeline, amici e follower.
<td>Nuovi tweet
<td><li>Cercare tweet
	<li>Inviare tweet
	<li>Ottenere la timeline utente
	<li>Fare il retweet
</tr>

<tr>
<td>Chatter
<td>Un connettore Chatter può essere usato per leggere, pubblicare e cercare messaggi nel feed di Chatter.
<td>Nuovo messaggio
<td><li>Pubblicare messaggio
<li>Cercare un messaggio
</tr>

<tr>
<td>Yammer
<td>Un connettore Yammer consente di pubblicare e leggere messaggi dai gruppi Yammer.
<td>Nuovi messaggi
<td><li>Pubblicare messaggio	
	<li>Ottenere il messaggio da gruppo\feed	
</tr>

<tr>
<td>Twilio
<td>Un connettore Twilio consente di inviare e ricevere SMS dal proprio account Twilio. Consente anche di recuperare numeri di telefono e dati sull'utilizzo.
<td>N/D
<td><li>Messaggi: invio, elenco, acquisizione e ricerca
	<li>Numeri di telefono: acquisto, elenco e acquisizione di numeri verdi e locali
</tr>
</table>


Dopo aver illustrato le funzionalità dei connettori per social network, verranno esaminati alcuni semplici casi di utilizzo per questi connettori.

## Perché usare i connettori?

I connettori accelerano lo sviluppo delle app e consentono anche a chi non è uno sviluppatore di creare applicazioni aziendali completamente funzionali senza dover imparare un linguaggio di programmazione o scrivere alcun codice.

### Monitoraggio dei suggerimenti clienti ###
Si supponga che la propria società abbia recentemente rilasciato una nuova app e che il team voglia sapere cosa ne dicono i clienti sui social media. Ogni membro del team potrà controllare periodicamente i vari siti dei social media e ipotizzare quali parole chiave i clienti potrebbero usare per discutere dell'app. In maniera molto più lineare, è possibile creare un semplice connettore Twitter e configurarlo in modo da monitorare specifici hashtag, citazioni e parole chiave su Twitter. Sarà quindi possibile usare il connettore SMTP per inviare ai membri del team un messaggio di posta elettronica che includa il contenuto dei tweet corrispondenti. È possibile procedere in maniera analoga monitorando Facebook e Yammer e inviando un SMS, tramite un connettore Twilio, al proprio team DevOps se il contenuto dei post di Facebook o Yammer indica che i clienti potrebbero aver riscontrato un problema critico. Tutto questo è possibile senza scrivere una singola riga di codice.

Di seguito sono riportati i requisiti iniziali.


## Creare un connettore
I connettori possono essere creati usando il portale di Azure

### Creare un connettore per social network nel portale di Microsoft Azure

1. Nel portale di Azure selezionare **NUOVO** > **Web e dispositivi mobili** > **Azure Marketplace**.
2. **Cercare** il connettore o selezionarlo nell'elenco. Una volta selezionato, si apre un nuovo pannello o finestra. Selezionare **Crea**. 
3. Immettere le seguenti proprietà per il connettore: 
	<table>
    <tr><th>Proprietà</th> <th>Descrizione</th> </tr>
    <tr><td>Nome</td> <td>Immettere un nome per l'app per le API, ad esempio RulesDiscountTaxCode o APIAppValidateXML.</td> </tr>
    <tr><td>Piano di servizio app</td> <td>Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.</th> </td>
    <tr><td>Piano tariffario</td> <td>Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure.</td> </tr>
    <tr><td>Gruppo di risorse</td> <td>Creare un nuovo gruppo di risorse o usare un gruppo esistente. L'articolo Uso dei gruppi di risorse illustra questa proprietà.</td> </tr>
    <tr><td>Sottoscrizione</td> <td>Proprietà di sola lettura che elenca la sottoscrizione corrente.</td> </tr>
    <tr><td>Location</td> <td>La località geografica che ospita il servizio di Azure. </td></tr>
    <tr><td>Aggiungi a schermata iniziale</td> <td>Selezionare questa opzione per aggiungere l'app per le API alla schermata iniziale (home page).</td></tr>
</table>
4. Selezionare **Crea**. Il connettore verrà creato. Il completamento dell'operazione può richiedere tempo. Durante la creazione del connettore verrà visualizzata la schermata iniziale. Usare la voce di menu Notifiche sulla sinistra per monitorare lo stato del connettore.

Dopo aver creato il primo connettore, è possibile usarlo per compilare un'app per la logica, mobile o Web.


### Accedere a un connettore con le API REST

[Accedere ai connettori con le API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)


## Aggiungere il connettore a un'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare questi connettori. Ad esempio, è possibile creare un'app per la *logica* combinando uno o più connettori *logicamente* in un'unica app.

Per usare i connettori nell'app per l'app per la *logica*, è sufficiente selezionare un connettore preconfigurato nell'elenco, aggiungerlo al flusso di lavoro di progettazione e apportare le modifiche necessarie alla configurazione.

Per seguire questa procedura, è necessaria un'app Web, un'app mobile o un'app per la logica. Vedere <> per i passaggi specifici. Una volta che l'applicazione è disponibile, aggiungere i connettori. Ecco come:

Usare i passaggi seguenti per aggiungere un connettore a un'app per la logica:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare la propria app per la logica, per dispositivi mobili o Web. 

	Se si sta creando una nuova app, cercare un'app per la logica, un'app mobile o un'app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.
3. Nella **Raccolta** selezionare il connettore. Verrà aggiunto all'app.
4. Configurare il connettore:
5. Le proprietà di ogni connettore sono specifiche del servizio e dell'ambiente a cui si connette. Immettere i dettagli per le proprietà. Tenere presente che alcune proprietà sono facoltative.
6. Selezionare **OK** per salvare le modifiche.


## Sicurezza
I connettori usano OAuth oppure nomi utente e password.

## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md) | 
[Siti Web e app Web in Azure App Service](../app-service-web/app-service-web-overview.md) |


## Altri connettori

[Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md) | 
[Connettori Enterprise](app-service-logic-enterprise-connectors.md) | 
[Connettori Business-to-Business](app-service-logic-b2b-connectors.md)￼ | 
[Connettori per protocolli](app-service-logic-protocol-connectors.md)￼ | 
[Connettori per i servizi app e dati](app-service-logic-data-connectors.md)￼ | 
[Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> 
[Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->