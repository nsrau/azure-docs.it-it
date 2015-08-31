<properties
   pageTitle="BizTalk JSON Encoder"
   description="BizTalk JSON Encoder"
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
   ms.date="08/19/2015"
   ms.author="rajram"/>

# BizTalk JSON Encoder
Il connettore di codifica e decodifica BizTalk JSON consente l'interoperabilità di app tra i dati JSON e XML. Può convertire una determinata istanza JSON in XML e viceversa.

## Uso di BizTalk JSON Encoder
Per usare BizTalk JSON Encoder, è prima di tutto necessario creare un'istanza dell'app per le API BizTalk JSON Encoder. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk JSON Encoder da Azure Marketplace.

## Uso di BizTalk JSON Encoder nell'area di progettazione di app per la logica
Seguire la procedura in [Creare una nuova app per la logica]. BizTalk JSON Encoder può essere usato come azione. Non include trigger.

### Azione
- Fare clic su BizTalk JSON Encoder dal riquadro destro

	![Impostazioni di azione][3]
- Fare clic su ->

	![Elenco di azioni][4]
- BizTalk JSON Encoder supporta due azioni. Selezionare *Xml to JSON*

	![Input di Xml to JSON][5]
- Fornire gli input per l'azione e configurarla.

	![Configurazione di Encode & Send][6]

Parametro|Tipo|Descrizione del parametro
---|---|---
XML di input|oggetto|Contenuto XML di input
Remove Outer Envelope|stringa|Flag impostato per rimuovere il nodo radice dal contenuto XML

L'azione restituisce una rappresentazione JSON del contenuto dell'input.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[Creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO8-->