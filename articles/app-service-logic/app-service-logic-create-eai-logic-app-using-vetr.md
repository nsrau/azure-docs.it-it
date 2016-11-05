---
title: Creare un'app per la logica EAI con VETR nelle app per la logica nel servizio app di Azure | Microsoft Docs
description: Funzioni Validate, Encode e Transform dei Servizi XML BizTalk.
services: logic-apps
documentationcenter: .net,nodejs,java
author: rajeshramabathiran
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2016
ms.author: rajram

---
# Creare un'app per la logica EAI con VETR
[!INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Nella maggior parte degli scenari di integrazione di applicazioni aziendali (EAI, Enterprise Application Integration) si esegue una mediazione dei dati tra un'origine e una destinazione. Tali scenari spesso hanno un set di requisiti comune:

* Assicurarsi che i dati provenienti da sistemi differenti siano nel formato corretto.
* Eseguire una ricerca sui dati in entrata per prendere decisioni.
* Convertire i dati da un formato a un altro. Ad esempio, convertire i dati da un formato dati del sistema CRM a un formato dati del sistema ERP.
* Instradare i dati all'applicazione o al sistema desiderati.

Questo articolo illustra un modello comune di integrazione: "messaggio unidirezionale mediazione" o VETR (Validate, Enrich, Transform, Route). Il modello VETR esegue la mediazione dei dati tra un'entità di origine e un'entità di destinazione, solitamente origini di dati.

Si prenda in considerazione un sito Web che accetta ordini: gli utenti pubblicano ordini nel sistema usando il protocollo HTTP. Dietro le quinte, il sistema convalida la correttezza dei dati in entrata, li normalizza e li mantiene in una coda del bus di servizio per elaborarli ulteriormente. Il sistema preleva gli ordini dalla coda, prevedendone un particolare formato. In tal modo, il flusso end-to-end è il seguente:

**HTTP** → **Convalida** → **Trasformazione** → **Bus di servizio**

![Flusso di base VETR][1]

Le seguenti app per le API di BizTalk aiutano a creare questo modello:

* **HTTP Trigger**: origine di attivazione dell'evento messaggio
* **Convalida**: convalida la correttezza dei dati in entrata
* **Trasforma**: trasforma i dati dal formato in entrata al formato richiesto dal sistema di downstream
* **Service Bus Connector**: entità di destinazione a cui vengono inviati i dati

## Creazione del modello VETR Basic
### Nozioni di base
Nel portale di Azure selezionare **+Nuovo**, selezionare **Web e dispositivi mobili** e quindi selezionare **App per la logica**. Scegliere un nome, un percorso, una sottoscrizione, un gruppo di risorse e una posizione validi. I gruppi di risorse fungono da contenitori per le app. Tutte le risorse per l'app sono dirette allo stesso gruppo di risorse.

Vengono quindi aggiunti trigger e azioni.

## Aggiungere trigger HTTP
1. In **Modelli di app per la logica** selezionare **Crea da zero**.
2. Selezionare **Listener HTTP** dalla raccolta per creare un nuovo listener. Denominarlo **HTTP1**.
3. Impostare l'opzione **Inviare risposta automaticamente?** su false. Configurare l'azione di attivazione impostando *Metodo HTTP* su *POST* e impostando *URL relativo* su */OneWayPipeline*: ![Trigger HTTP][2]
4. Selezionare il segno di spunta verde per completare il trigger.

## Aggiungere un'azione di convalida
A questo punto, è possibile aggiungere azioni che verranno eseguite ogni volta che si riceve una chiamata sull'endpoint HTTP.

1. Aggiungere **BizTalk XML Validator** dalla raccolta e denominarlo *(Validate1)* per creare un'istanza.
2. Configurare uno schema XSD per convalidare i messaggi XML in entrata. Selezionare l'azione *Validate* e quindi selezionare *triggers(‘httplistener’).outputs.Content* come valore per il parametro *inputXml*.

A questo punto, l'azione di convalida è la prima azione dopo il listener HTTP.

![BizTalk XML Validator][3]

In maniera analoga, si aggiungeranno le restanti azioni.

## Aggiungere un'azione di trasformazione
È ora possibile configurare le trasformazioni per normalizzare i dati in entrata.

1. Aggiungere **Servizio BizTalk Transform** dalla raccolta.
2. Per configurare una trasformazione in modo da trasformare i messaggi XML in entrata, selezionare l'azione **Transform** come azione da eseguire quando si chiama questa API. Selezionare ```triggers(‘httplistener’).outputs.Content``` come valore per *inputXml*. *Map* è un parametro facoltativo perché i dati in entrata vengono confrontati con tutte le trasformazioni configurate e solo quelli che corrispondono allo schema vengono applicati.
3. Infine, l'azione Transform viene eseguita solo se l'azione Validate ha esito positivo. Per configurare questa condizione, selezionare l'icona a forma di ingranaggio in alto a destra e selezionare *Aggiungere una condizione da soddisfare*. Impostare la condizione su ```equals(actions('xmlvalidator').status,'Succeeded')```:

![Trasformazioni di BizTalk][4]

## Aggiungere il connettore del bus di servizio
A questo punto, è possibile aggiungere la destinazione in cui scrivere i dati, ovvero una coda del bus di servizio.

1. Aggiungere un **Service Bus Connector** dalla raccolta. Impostare **Nome** su *Servicebus1*, impostare **Stringa di connessione** sulla stringa di connessione all'istanza del bus di servizio, impostare **Nome entità** su *Coda* e ignorare **Nome sottoscrizione**.
2. Selezionare l'azione **Invia messaggio** e impostare il campo **Messaggio** per l'azione su *actions('transformservice').outputs.OutputXml*.
3. Impostare il campo **Tipo contenuto** su *application/xml*:

![Bus di servizio][5]

## Inviare una risposta HTTP
Dopo aver terminato l'elaborazione pipeline, verrà reinviata una risposta HTTP per entrambi i risultati, con i seguenti passaggi:

1. Aggiungere un **Listener HTTP** dalla raccolta e selezionare l'azione **Invia risposta HTTP**.
2. Impostare **ID risposta** su *Invia messaggio*.
3. Impostare **Contenuto della risposta** su *Elaborazione pipeline completata*.
4. Impostare **Codice di stato della risposta** su *200* per indicare HTTP 200 OK.
5. Selezionare il menu a discesa nella parte superiore destra e selezionare **Aggiungere una condizione da soddisfare**. Impostare la condizione sull'espressione seguente: ```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
6. Ripetere i passaggi anche per inviare una risposta HTTP in caso di errore. Modificare **Condizione** impostando l'espressione seguente: ```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
7. Selezionare **OK** quindi **Crea**.

## Completamento
Ogni volta che qualcuno invia un messaggio all'endpoint HTTP attiva l'app ed esegue le azioni appena create. Per gestire eventuali app per la logica create, selezionare **Sfoglia** nel portale di Azure e selezionare **App per la logica**. Selezionare l'app per visualizzare altre informazioni.

Alcuni argomenti utili:

[Gestire e monitorare le app per le API e i connettori](app-service-logic-monitor-your-connectors.md) <br/> [Monitorare le app per la logica](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0803_2016-->