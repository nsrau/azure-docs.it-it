<properties 
   pageTitle="Creare un'app per la logica EAI con VETR" 
   description="Questo argomento illustra le funzionalità Validate, Encode e Transform dei Servizi XML BizTalk." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/24/2015"
   ms.author="rajram"/>


# Creare un'app per la logica EAI con VETR

Nella maggior parte degli scenari di integrazione di applicazioni aziendali (EAI, Enterprise Application Integration) si esegue una mediazione dei dati tra un'origine e una destinazione. Tali scenari spesso hanno un set di requisiti comune:

- Assicurarsi che il formato dei dati provenienti da sistemi differenti sia corretto
- Eseguire una ricerca sui dati in entrata per prendere decisioni
- Convertire i dati da un formato a un altro (ad esempio, da un formato dati del sistema CRM a un formato dati del sistema ERP)
- Dirigere i dati all'applicazione o al sistema desiderati

In questo articolo viene illustrato un modello comune di integrazione: "messaggio unidirezionale mediazione" o VETR (Validate, Enrich, Transform, Route). Il modello VETR esegue la mediazione dei dati tra un'entità di origine e un'entità di destinazione, solitamente origini di dati.

Si prenda in considerazione un sito Web che accetta ordini: gli utenti pubblicano ordini nel sistema usando il protocollo HTTP. Dietro le quinte, il sistema convalida la correttezza dei dati in entrata, li normalizza e li mantiene in una coda del bus di servizio per elaborarli ulteriormente. Il sistema preleva gli ordini dalla coda, prevedendone un particolare formato. In tal modo, il flusso end-to-end è il seguente:

HTTP -> Validate -> Transform -> Service Bus

![Flusso di base VETR][1]

Le seguenti app per le API di BizTalk aiutano a creare questo modello:

*HTTP Trigger*: origine di attivazione dell'evento messaggio*Validate*: convalida la correttezza dei dati in entrata*Transform*: trasforma i dati dal formato in entrata al formato richiesto dal sistema di downstream *Service Bus Connector*: entità di destinazione a cui vengono inviati i dati


## Creazione del modello VETR Basic
### Nozioni di base

Nel portale di gestione di Azure fare clic sul pulsante **+Nuovo** in basso a sinistra dello schermo, quindi scegliere App per la logica. Scegliere un nome, un percorso, una sottoscrizione, un gruppo di risorse e una posizione validi. I gruppi di risorse fungono da contenitori per le app e tutte le risorse per l'app sono dirette allo stesso gruppo di risorse.

Vengono quindi aggiunti trigger e azioni.


## Aggiungere trigger HTTP

1. Selezionare **Listener HTTP** dalla raccolta per creare un nuovo listener. Denominarlo **HTTP1**.
2. Lasciare l'impostazione **Inviare risposta automaticamente?** su false. Configurare l'azione di attivazione impostando _HTTP Method_ su _POST_ e impostando _Relative URL_ su _/OneWayPipeline_.

![Trigger HTTP][2]


## Aggiungere un'azione di convalida

A questo punto, è possibile aggiungere azioni che verranno eseguite ogni volta che si riceve una chiamata sull'endpoint HTTP.

1. Aggiungere **BizTalk XML Validator** dalla raccolta e denominarlo _(Validate1)_ per creare un'istanza.
2. Configurare uno schema XSD per convalidare i messaggi XML in entrata. Selezionare l'azione _Validate_ e quindi selezionare _triggers(‘httplistener’).outputs.Content_ come valore per il parametro _inputXml_.

A questo punto, l'azione di convalida è la prima azione dopo il listener HTTP. In maniera analoga, si aggiungeranno le restanti azioni.

![BizTalk XML Validator][3]


## Aggiungere un'azione di trasformazione
È ora possibile configurare le trasformazioni per normalizzare i dati in entrata.

1. Aggiungere **Transform** dalla raccolta. 
2. Per configurare una trasformazione in modo da trasformare i messaggi XML in entrata, selezionare l'azione **Transform** come quella da eseguire quando questa API viene chiamata e selezionare ```triggers(‘httplistener’).outputs.Content``` come valore per _inputXml_. Map è un parametro facoltativo perché i dati in entrata vengono confrontati con tutte le trasformazioni configurate e solo quelli che corrispondono allo schema vengono applicati.
3. Infine, l'azione Transform viene eseguita solo se l'azione Validate ha esito positivo. Per configurare questa condizione, selezionare l'icona a forma di ingranaggio in alto a destra accanto a _"Aggiungere una condizione da soddisfare"_. Impostare la condizione su ```equals(actions('xmlvalidator').status,'Succeeded')```:


![Trasformazioni di BizTalk][4]


## Aggiungere il connettore del bus di servizio
A questo punto, è possibile aggiungere la destinazione in cui scrivere i dati, ovvero una coda del bus di servizio.

1. Aggiungere un **Service Bus Connector** dalla raccolta. Impostare _Nome_ su _Servicebus1_, impostare _Stringa di connessione_ sulla stringa di connessione all'istanza del bus di servizio, impostare _Nome entità_ su _Coda_ e ignorare _Nome sottoscrizione_. 
2. Selezionare l'azione **Invia messaggio** e impostare il campo _Messaggio_ per l'azione su _actions('transformservice').outputs.OutputXml_

![Bus di servizio][5]


## Inviare una risposta HTTP
Dopo aver terminato l'elaborazione pipeline, verrà reinviata una risposta HTTP per entrambi i risultati, con i seguenti passaggi:

1. Aggiungere un **Listener HTTP** dalla raccolta e selezionare l'azione **Invia risposta HTTP**.
2. Impostare _Response Content_ su "Elaborazione pipeline completata", _Codice di stato della risposta_ su “200” per indicare HTTP 200 OK e la condizione sull'espressione ```@equals(actions('servicebusconnector').status,'Succeeded')```
	
Ripetere i passaggi precedenti anche per inviare una risposta HTTP in caso di errore. Impostare la condizione su ```@not(equals(actions('servicebusconnector').status,'Succeeded')).```


## Completamento
Ogni volta che qualcuno invia un messaggio all'endpoint HTTP attiva l'app ed esegue le azioni appena create. Per gestire eventuali app per la logica create, fare clic su **Sfoglia** nel portale di gestione di Azure e scegliere **App per la logica**. Fare clic sull'app per visualizzare altre informazioni.


<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

 

<!---HONumber=July15_HO4-->