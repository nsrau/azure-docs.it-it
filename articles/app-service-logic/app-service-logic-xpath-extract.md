<properties
   pageTitle="Uso di BizTalk XPath Extractor in app per la logica in Servizio app di Azure | Microsoft Azure"
   description="BizTalk XPath Extractor"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk XPath Extractor

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Il connettore BizTalk XPath Extract permette all'app di cercare ed estrarre i dati dal contenuto XML in base a un XPath specifico.

## Uso di BizTalk Xpath Extractor
1. Per usare BizTalk Xpath Extractor, è prima di tutto necessario creare un'istanza dell'app per le API BizTalk Xpath Extractor. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk Xpath Extractor da Azure Marketplace.

	>[AZURE.NOTE] Nessuna impostazione di configurazione associata a BizTalk Xpath Extractor.
2. [Creare una nuova app per la logica]. Aprire "Trigger e azioni" all'interno dell'app per la logica per visualizzare l'area di progettazione delle app per la logica e configurare il flusso.
3. Nel riquadro destro dell'area di progettazione sono elencate le app per le API disponibili per la compilazione del flusso. Individuare "BizTalk XPath Extractor". Selezionando questa app, Xpath Extractor viene aggiunto al flusso e ne viene effettuato il provisioning di un'istanza.
4. Al termine del provisioning, nell'area di progettazione viene visualizzata l'azione associata all'app per le API BizTalk XPath Extractor: ![Azione di selezione di BizTalk XPath Extractor][1]

5. Scegliere l'opzione di estrazione tramite XPath, che consente di valutare l'espressione xpath di input in uno specifico XML di input: ![Input di BizTalk XPath Extractor][2]

	Parametro|Tipo|Descrizione del parametro
---|---|---
XPath|stringa|Percorso di query in XML.
XML di input|stringa|Contenuto XML di input.

L'azione restituisce l'output sotto forma di stringa - Risultato. Il risultato contiene il valore del percorso di query in XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0420_2016-->