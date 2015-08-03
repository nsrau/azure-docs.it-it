<properties
   pageTitle="BizTalk XPath Extractor"
   description="BizTalk XPath Extractor"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/01/2015"
   ms.author="rajram"/>

#BizTalk XPath Extractor

Il connettore BizTalk XPath Extract permette all'app di cercare ed estrarre i dati dal contenuto XML in base a un XPath specifico.

##Uso di BizTalk Xpath Extractor
1. Per usare BizTalk Xpath Extractor, è prima di tutto necessario creare un'istanza dell'app per le API BizTalk Xpath Extractor. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API BizTalk Xpath Extractor da Azure Marketplace.

	>[AZURE.NOTE]
2. [Creare una nuova app per la logica]. Aprire "Trigger e azioni" all'interno dell'app per la logica appena creata per visualizzare la finestra di progettazione delle app per la logica e configurare il flusso.
3. Nel riquadro destro della finestra di progettazione sono elencate le app per le API disponibili con cui creare il flusso. Individuare "BizTalk XPath Extractor". Selezionando questa app, Xpath Extractor verrà aggiunto al flusso e ne verrà eseguito il provisioning di un'istanza.
2. Dopo il provisioning, nella finestra di progettazione viene visualizzata l'azione associata all'app per le API BizTalk XPath Extractor.

![Azione di selezione di BizTalk XPath Extractor][1]

3. Scegliere "Extract Using XPath"

"Extract Using XPath" valuta l'espressione xpath di input in un XML di input specifico.

![Input di BizTalk XPath Extractor][2]

<table>
	<tr>
		<th>Parametro</th>
		<th>Tipo</th>
		<th>Descrizione del parametro</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>stringa</td>
		<td>Percorso di query in XML.</td>
	</tr>
	<tr>
		<td>XML di input</td>
		<td>stringa</td>
		<td>Contenuto XML di input.</td>
	</tr>
</table>

L'azione restituisce l'output sotto forma di stringa - Risultato. Il risultato contiene il valore del percorso di query in XML.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[Creare una nuova app per la logica]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO4-->