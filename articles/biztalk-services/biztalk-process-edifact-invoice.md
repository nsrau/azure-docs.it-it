---
title: 'Esercitazione: Elaborare fatture EDIFACT con Servizi BizTalk di Azure | Documentazione Microsoft'
description: Come creare e configurare l'app per le API o il connettore Box e usarlo in un'app per la logica in Azure App Service
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b7ad7b91c6b836f26b45959ef65a99666a4bf69a
ms.contentlocale: it-it
ms.lasthandoff: 02/16/2017

---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Esercitazione: Elaborare fatture EDIFACT con Servizi BizTalk di Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

È possibile utilizzare il portale Servizi BizTalk per configurare e distribuire accordi EDIFACT e X12. In questa esercitazione viene illustrato come creare un accordo EDIFACT per lo scambio di fatture tra partner commerciali. L'esercitazione è relativa a una soluzione aziendale end-to-end che coinvolge due partner commerciali, Northwind e Contoso, che si scambiano messaggi EDIFAC.  

## <a name="sample-based-on-this-tutorial"></a>Esempio basato su questa esercitazione
Questa esercitazione è stata scritta in base a un esempio, **Sending EDIFACT Invoices Using BizTalk Services** (Invio di fatture EDIFACT con Servizi BizTalk), disponibile per il download in [MSDN Code Gallery](http://go.microsoft.com/fwlink/?LinkId=401005). È possibile usare l'esempio e scorrere questa esercitazione per comprendere come è stato creato oppure usare l'esercitazione per creare la propria soluzione. Questa esercitazione è mirata al secondo approccio, in modo che si possa comprendere come è stata creata questa soluzione. Inoltre, per quanto possibile, l'esercitazione è coerente con l'esempio e usa gli stessi nomi per gli elementi (ad esempio schemi e trasformazioni).  

> [!NOTE]
> Poiché questa soluzione prevede l'invio di un messaggio da un bridge EAI a un bridge EDI, riusa l' [esempio di concatenamento di bridge di Servizi BizTalk](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) .  
> 
> 

## <a name="what-does-the-solution-do"></a>Scopo della soluzione
In questa soluzione Northwind riceve da Contoso fatture EDIFACT che non sono in un formato EDI standard. Pertanto, prima dell'invio a Northwind, una fattura deve essere convertita in un documento fattura EDIFACT (nota anche come INVOIC). Alla ricezione, Northwind deve elaborare la fattura EDIFACT e restituire a Contoso un messaggio di controllo (noto anche come CONTRL).

![][1]  

Per ottenere questo scenario aziendale, Contoso usa le funzionalità fornite con Servizi BizTalk di Microsoft Azure.

* Contoso usa bridge EAI per convertire la fattura originale in INVOIC EDIFACT.
* Il bridge EAI quindi invia il messaggio a un bridge di trasmissione EDI distribuito come parte di un accordo configurato nel portale Servizi BizTalk.
* Il bridge di trasmissione EDI elabora la fattura INVOIC EDIFACT e la instrada a Northwind.
* Dopo aver ricevuto la fattura, Northwind restituisce un messaggio CONTRL al bridge di ricezione EDI distribuito come parte dell'accordo.  

> [!NOTE]
> Facoltativamente, questa soluzione illustra anche come usare l'invio in batch per trasmettere le fatture in batch anziché singolarmente.  
> 
> 

Per completare lo scenario, vengono usate code di bus di servizio per inviare la fattura da Contoso a Northwind o ricevere un acknowledgement da Northwind. Queste code possono essere create usando un'applicazione client, disponibile come download e inclusa nel pacchetto di esempio fornito come parte dell'esercitazione.  

## <a name="prerequisites"></a>Prerequisiti
* È necessario disporre di uno spazio dei nomi di bus di servizio. Per istruzioni sulla creazione di uno spazio dei nomi, vedere [Procedura: Creare o modificare uno spazio dei nomi del servizio del bus di servizio](https://msdn.microsoft.com/library/azure/hh674478.aspx). Si supponga di aver già effettuato il provisioning di uno spazio dei nomi di bus di servizio, denominato **edifactbts**.
* È necessario disporre di una sottoscrizione a Servizi BizTalk. Per le istruzioni, vedere [Creazione di servizi BizTalk tramite il portale di Azure](http://go.microsoft.com/fwlink/?LinkID=302280). Per questa esercitazione, si supponga di avere una sottoscrizione a Servizi BizTalk, denominata **contosowabs**.
* Registrare la propria sottoscrizione a Servizi BizTalk nel portale di Servizi BizTalk. Per istruzioni, vedere [Registrazione e aggiornamento di una distribuzione del servizio BizTalk nel portale dei servizi BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* È necessario aver installato Visual Studio.
* È necessario aver installato BizTalk Services SDK. È possibile scaricare l'SDK dal sito Web all'indirizzo [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Passaggio 1: creare le code del bus di servizio
Questa soluzione usa code di bus di servizio per lo scambio di messaggi tra i partner commerciali. Contoso e Northwind inviano messaggi alle code, da dove vengono usati dai bridge EAI e/o EDI. Per questa soluzione, sono necessarie tre code del bus di servizio:

* **northwindreceive** : Northwind riceve la fattura da Contoso tramite questa coda.
* **contosoreceive** : Contoso riceve l'acknowledgement da Northwind tramite questa coda.
* **suspended** – A questa coda vengono instradati tutti i messaggi sospesi. I messaggi vengono sospesi se presentano errori durante l'elaborazione.

È possibile creare queste code di bus di servizio usando un'applicazione client inclusa nel pacchetto di esempio.  

1. Dal percorso in cui è stato scaricato l'esempio, aprire **Tutorial Sending Invoices Using BizTalk Services EDI Bridges.sln**.
2. Premere **F5** per compilare e avviare l'applicazione **Tutorial Client** (Client esercitazione).
3. Nella schermata immettere lo spazio dei nomi, nonché il nome e la chiave dell'emittente ACS del bus di servizio.
   
   ![][2]  
4. Un messaggio informa che verranno create tre code nello spazio dei nomi del bus di servizio. Fare clic su **OK**.
5. Lasciare in esecuzione il client dell'esercitazione. Aprire , fare clic su **Bus di servizio** > ***spazio dei nomi del bus di servizio*** > **Code** e verificare che le tre code siano state create.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Passaggio 2: creare e distribuire un contratto tra partner commerciali
Creare un accordo tra partner commerciali tra Contoso e Northwind. Questo tipo di accordo definisce un contratto per gli scambi tra i due partner commerciali, ad esempio quale schema dei messaggi usare, quale protocollo di messaggistica applicare e così via. Un accordo tra partner commerciali include due bridge EDI, uno per inviare messaggi ai partner commerciali (denominato **bridge di trasmissione EDI**) e uno per ricevere messaggi dai partner commerciali (denominato **bridge di ricezione EDI**).

Nel contesto di questa soluzione il bridge di trasmissione EDI corrisponde al lato invio dell'accordo e viene usato per inviare la fattura EDIFACT da Contoso a Northwind. Analogamente, il bridge di ricezione EDI corrisponde al lato ricezione dell'accordo e viene usato per ricevere gli acknowledgement da Northwind.  

### <a name="create-the-trading-partners"></a>Creare i partner commerciali
Per iniziare, creare i partner commerciali per Contoso e Northwind.  

1. Nel portale di Servizi BizTalk fare clic su **Aggiungi** nella scheda **Partner**.
2. Nella pagina Nuovo partner immettere **Contoso** come nome del partner, quindi fare clic su **Salva**.
3. Ripetere il passaggio per creare il secondo partner, **Northwind**.  

### <a name="create-the-agreement"></a>Creare il contratto
Gli accordi tra partner commerciali vengono creati tra i profili di business dei partner coinvolti. Questa soluzione usa i profili di partner predefiniti creati automaticamente quando sono stati creati i partner.  

1. Nel portale di Servizi BizTalk fare clic su **Contratti** > **Aggiungi**.
2. Nella pagina **Impostazioni generali** del nuovo accordo specificare i valori mostrati nell'immagine seguente, quindi fare clic su **Continua**.
   
   ![][3]  
   
   Dopo avere fatto clic su **Continua**, diventano disponibili due schede, **Impostazioni di ricezione** e **Impostazioni di invio**.
3. Creare l'accordo di invio tra Contoso e Northwind. Questo accordo definisce le modalità con cui Contoso invia la fattura EDIFACT a Northwind.
   
   1. Fare clic su **Send Settings**.
   2. Mantenere i valori predefiniti nelle schede **Inbound URL** (URL in ingresso), **Trasforma** e **Batch**.
   3. Nella scheda **Protocollo** caricare lo schema **EFACT_D93A_INVOIC.xsd** nella sezione **Schemi**. Questo schema è disponibile con il pacchetto di esempio.
      
      ![][4]  
   4. Nella scheda **Trasporto** specificare i dettagli delle code del bus di servizio. Per l'accordo sul lato invio, viene usata la coda **northwindreceive** per inviare la fattura EDIFACT a Northwind, mentre la coda **suspended** viene usata per instradare gli eventuali messaggi che presentano problemi durante l'elaborazione e vengono sospesi. Queste code sono state create nel **Passaggio 1: Creare le code del bus di servizio** di questo argomento.
      
      ![][5]  
      
      In **Impostazioni trasporto > Tipo trasporto** e in **Message Suspension Settings (Impostazioni di sospensione messaggi) > Tipo trasporto** selezionare Bus di servizio di Azure e specificare i valori mostrati nell'immagine.
4. Creare l'accordo di ricezione tra Contoso e Northwind. Questo accordo definisce le modalità con cui Contoso riceve l'acknowledgement da Northwind.
   
   1. Fare clic su **Receive Settings**.
   2. Mantenere i valori predefiniti nelle schede **Trasporto** e **Trasforma**.
   3. Nella scheda **Protocollo** caricare lo schema **EFACT_4.1_CONTRL.xsd** nella sezione **Schemi**. Questo schema è disponibile con il pacchetto di esempio.
   4. Nella scheda **Route** creare un filtro per assicurarsi che a Contoso vengano instradati solo gli acknowledgement di Northwind. In **Route Settings** (Impostazioni route) fare clic su **Aggiungi** per creare il filtro di routing.
      
      ![][6]  
      
      1. Nei campi **Nome regola**, **Route rule** (Regola route) e **Route destination** (Destinazione route) specificare i valori mostrati nell'immagine.
      2. Fare clic su **Salva**.
   5. Sempre nella scheda **Route** , specificare dove devono essere instradati gli acknowledgement sospesi, ossia gli acknowledgement che presentano problemi durante l'elaborazione. Impostare il tipo di trasporto su Bus di servizio di Azure, il tipo di destinazione route su **Coda**, il tipo di autenticazione su **Firma di accesso condiviso**, specificare la relativa stringa di connessione per lo spazio dei nomi di bus di servizio quindi immettere **suspended** come nome della coda.
5. Fare infine clic su **Distribuisci** per distribuire l'accordo. Notare gli endpoint in cui vengono distribuiti gli accordi di invio e ricezione.
   
   * Nella scheda **Impostazioni di invio** prendere nota dell'endpoint in **Inbound URL** (URL in ingresso). Per inviare un messaggio da Contoso a Northwind usando il bridge di trasmissione EDI è necessario inviare un messaggio a questo endpoint.
   * Nella scheda **Impostazioni di ricezione** prendere nota dell'endpoint in **Trasporto**. Per inviare un messaggio da Northwind a Contoso usando il bridge di ricezione EDI è necessario inviare un messaggio a questo endpoint.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Passaggio 3: creare e distribuire il progetto di Servizi BizTalk
Nel passaggio precedente sono stati distribuiti gli accordi di invio e ricezione EDI per elaborare le fatture EDIFACT e gli acknowledgement. Questi accordi possono elaborare solo messaggi conformi allo schema EDIFACT standard. Tuttavia, in base allo scenario per questa soluzione, Contoso invia a Northwind una fattura in uno schema proprietario interno. Pertanto, prima di essere inviato al bridge di trasmissione EDI, il messaggio deve essere convertito dallo schema interno allo schema di fattura EDIFACT standard. Questa operazione viene eseguita dal progetto EAI di Servizi BizTalk.

Anche il progetto di Servizi BizTalk, **InvoiceProcessingBridge**, che converte il messaggio, è incluso come parte dell'esempio scaricato. Il progetto contiene i seguenti elementi:

* **INHOUSEINVOICE.XSD** :schema della fattura interna che viene inviata a Northwind.
* **EFACT_D93A_INVOIC.XSD**: schema della fattura EDIFACT standard.
* **EFACT_4.1_CONTRL.XSD**: schema dell'acknowledgement EDIFACT che Northwind invia a Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC.TRFM**: trasformazione che esegue il mapping dello schema della fattura interna allo schema della fattura EDIFACT standard.  

### <a name="create-the-biztalk-services-project"></a>Creare il progetto di Servizi BizTalk
1. Nella soluzione di Visual Studio espandere il progetto InvoiceProcessingBridge, quindi aprire il file **MessageFlowItinerary.bcs** .
2. Fare clic in qualsiasi punto del canvas e impostare **BizTalk Service URL** nella sezione delle proprietà in modo da specificare il nome della propria sottoscrizione a Servizi BizTalk. Ad esempio: `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Dalla casella degli strumenti trascinare uno strumento **Bridge XML unidirezionale** nel canvas. Impostare le proprietà **Nome entità** e **Indirizzo relativo** del bridge su **ProcessInvoiceBridge**. Fare doppio clic su **ProcessInvoiceBridge** per aprire l'area di configurazione del bridge.
4. Nella casella **Tipi di messaggio** fare clic sul pulsante più (**+**) per specificare lo schema del messaggio in arrivo. Poiché il messaggio in arrivo per il bridge EAI è sempre la fattura interna, impostarlo su **INHOUSEINVOICE**.
   
   ![][8]  
5. Fare clic sulla forma **Trasformazione XML** e nella sezione delle proprietà, per la proprietà **Mappe**, fare clic sul pulsante con i puntini di sospensione (**...**). Nella finestra di dialogo **Maps Selection** (Selezione mappe) selezionare il file di trasformazione **INHOUSEINVOICE_to_D93AINVOIC**, quindi fare clic su **OK**.
   
   ![][9]  
6. Tornare a **MessageFlowItinerary.bcs** e dalla casella degli strumenti trascinare un **endpoint di servizio esterno bidirezionale** a destra di **ProcessInvoiceBridge**. Impostare la proprietà **Nome entità** su **EDIBridge**.
7. In Esplora soluzioni espandere **MessageFlowItinerary.bcs** e fare doppio clic sul file **EDIBridge.config file**. Sostituire il contenuto di **EDIBridge.config** con quanto segue.
   
   > [!NOTE]
   > Perché è necessario modificare il file con estensione config? L'endpoint del servizio esterno aggiunto al canvas di progettazione del bridge rappresenta i bridge EDI distribuiti in precedenza. I bridge EDI sono bidirezionali, con un lato invio e un lato ricezione. Il bridge EAI aggiunto al canvas di progettazione è però un bridge unidirezionale. Pertanto, per gestire i diversi modelli di scambio dei messaggi dei due bridge, viene usato un comportamento di bridge personalizzato includendo la relativa configurazione nel file config. Il comportamento personalizzato gestisce anche l'autenticazione nell'endpoint del bridge di trasmissione EDI. Questo comportamento personalizzato è disponibile come esempio separato in [BizTalk Services Bridge chaining sample - EAI to EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) (Esempio di concatenamento di bridge di Servizi BizTalk: da EAI a EDI). Questa soluzione riusa l'esempio.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Aggiornare il file EDIBridge.config in modo da includere i dettagli di configurazione
   
   * In *<behaviors>* specificare lo spazio dei nomi del servizio contenitore di Azure e la chiave associati alla sottoscrizione a Servizi BizTalk.
   * In *<client>* specificare l'endpoint in cui è distribuito il contratto di invio EDI.
   
   Salvare le modifiche e chiudere il file di configurazione.
9. Nella casella degli strumenti fare clic su **Connettore** e aggiungere i componenti **ProcessInvoiceBridge** ed **EDIBridge**. Selezionare il connettore e nella casella delle proprietà impostare **Condizione filtro** su **Match All** (Corrispondenza con tutti). In questo modo, tutti i messaggi elaborati dal bridge EAI vengono instradati al bridge EDI.
   
   ![][10]  
10. Salvare le modifiche apportate alla soluzione.  

### <a name="deploy-the-project"></a>Distribuire il progetto
1. Nel computer in cui è stato creato il progetto di Servizi BizTalk scaricare e installare il certificato SSL per la propria sottoscrizione a Servizi BizTalk. Da , in Servizi BizTalk fare clic su **Dashboard**, quindi su **Scarica certificato SSL**. Fare doppio clic sul certificato e seguire i messaggi di richiesta per completare l'installazione. Assicurarsi di installare il certificato nell'archivio certificati **Autorità di certificazione radice disponibile nell'elenco locale** .
2. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto **InvoiceProcessingBridge** e scegliere **Distribuisci**.
3. Specificare i valori mostrati nell'immagine, quindi fare clic su **Distribuisci**. È possibile ottenere le credenziali ACS per i Servizi BizTalk facendo clic su **Informazioni di connessione** dal dashboard dei Servizi BizTalk.
   
   ![][11]  
   
   Dal riquadro di output copiare l'endpoint in cui è distribuito il bridge EAI, ad esempio `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. L'URL di questo endpoint sarà necessario più avanti.  

## <a name="step-4-test-the-solution"></a>Passaggio 4: testare la soluzione
In questo argomento viene illustrato come testare la soluzione utilizzando l'applicazione **client dell'esercitazione** fornita come parte dell'esempio.  

1. In Visual Studio premere F5 per avviare l'applicazione **client dell'esercitazione**.
2. Nella schermata devono già essere inseriti i valori specificati nel passaggio in cui sono state create le code del bus di servizio. Fare clic su **Avanti**.
3. Nella finestra successiva specificare le credenziali ACS per la sottoscrizione a Servizi BizTalk e gli endpoint in cui sono distribuiti i bridge EAI ed EDI (ricezione).
   
   L'endpoint del bridge EAI è stato copiato nel passaggio precedente. Per l'endpoint del bridge di ricezione EDI, nel portale di Servizi BizTalk passare all'accordo > Receive Settings > Trasporto > Endpoint.
   
   ![][12]  
4. Nella finestra successiva, in Contoso, fare clic sul pulsante **Send In-house Invoice** . Nella finestra di dialogo Apri file aprire il file INHOUSEINVOICE.txt. Esaminare il contenuto del file, quindi fare clic su **OK** per inviare la fattura.
   
   ![][13]  
5. Dopo pochi secondi Northwind riceve la fattura. Fare clic sul collegamento **Visualizza messaggio** per visualizzare la fattura ricevuta da Northwind. Si noti che la fattura ricevuta da Northwind è nello schema EDIFACT standard, mentre quella inviata da Contoso era in uno schema interno.
   
   ![][14]  
6. Selezionare la fattura, quindi fare clic su **Send Acknowledgement**. Nella finestra di dialogo che viene visualizzata si noti che l'ID dell'interscambio è uguale nella fattura ricevuta e nell'acknowledgement inviato. Fare clic su OK nella finestra di dialogo **Invia conferma** .
   
   ![][15]  
7. Dopo pochi secondi Contoso riceve correttamente l'acknowledgement.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Passaggio 5 (facoltativo): inviare le fatture EDIFACT in batch
I bridge EDI di Servizi BizTalk supportano anche l'invio in batch dei messaggi in uscita. Questa funzionalità è utile per i partner destinatari che preferiscono ricevere un batch di messaggi (che soddisfano un determinato criterio) anziché messaggi singoli.

Quando si usano i batch, l'aspetto più importante da considerare è l'effettivo rilascio del batch o, in altre parole, i criteri di rilascio. I criteri di rilascio possono essere basati sulla modalità con cui il partner destinatario desidera ricevere i messaggi. Se l'invio in batch è abilitato, il bridge EDI non invia il messaggio in uscita al partner destinatario finché i criteri di rilascio non sono soddisfatti. Ad esempio, se si impostano criteri di invio in batch basati sul numero di messaggi, viene inviato un batch solo quando vengono raggruppati 'n' messaggi. I criteri di batch possono anche essere basati sul tempo e in questo caso un batch può ad esempio essere inviato ogni giorno a una determinata ora. In questa soluzione si tenta di usare criteri basati sulla dimensione del messaggio.

1. Nel portale di Servizi BizTalk fare clic sull'accordo creato in precedenza. Fare clic su Send Settings > Batch > Add Batch.
2. Come nome del batch immettere **InvoiceBatch**, specificare una descrizione, quindi fare clic su **Avanti**.
3. Specificare criteri di batch che definiscano quali messaggi devono essere raggruppati. In questa soluzione vengono raggruppati tutti i messaggi. Selezionare pertanto l'opzione Use advanced definitions e immettere **1 = 1**. Questa è una condizione che sarà sempre vera, quindi tutti i messaggi verranno raggruppati in un batch. Fare clic su **Avanti**.
   
   ![][17]  
4. Specificare criteri di rilascio del batch. Dalla casella di riepilogo a discesa selezionare **MessageCountBased** e per **Conteggio** specificare **3**. Questo significa che a Northwind verrà inviato un batch di tre messaggi. Fare clic su **Avanti**.
   
   ![][18]  
5. Controllare il riepilogo, quindi fare clic su **Salva**. Fare clic su **Distribuisci** per ridistribuire l'accordo.
6. Tornare all'applicazione **Tutorial Client** (Client esercitazione), fare clic su **Send In-house Invoice** (Invia fattura interna) e seguire i prompt per inviare la fattura. Si noterà che Northwind non riceve alcuna fattura perché i criteri relativi al numero di messaggi per il batch non sono soddisfatti. Ripetere questo passaggio altre due volte, in modo da avere tre messaggi di fatture da inviare a Northwind. In questo caso, i criteri di rilascio, che prevedono un batch di 3 messaggi, vengono soddisfatti, pertanto ora dovrebbe risultare una fattura presso Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG


