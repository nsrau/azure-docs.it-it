<properties 
   pageTitle="Wait Connector" 
   description="Wait Connector" 
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
   ms.date="06/29/2015"
   ms.author="rajram"/>

#Wait Connector
Wait Connector consente di ritardare l'esecuzione di un'app per una durata specificata o fino al verificarsi di un orario specificato. Quando viene usato in un flusso di lavoro, può consentire di ritardare l'esecuzione.

##Uso di Wait Connector
Per usare Wait Connector, è necessario creare prima di tutto un'istanza dell'app per le API Wait Connector. Questa operazione può essere eseguita inline durante la creazione di un'app per la logica o selezionando l'app per le API Wait Connector da Azure Marketplace.

##Uso di Wait Connector nell'area di progettazione delle app per la logica
Wait Connector può essere usato come azione. Non include trigger.

###Azione
- Fare clic su Wait Connector nel riquadro destro.

	![Elenco di azioni][1]
- Wait Connector supporta due azioni: 
	- Delay
	- Delay Until
	 
- Selezionare *Delay*.

	![Input relativo al ritardo][2]
- Fornire gli input per l'azione e configurarla.

	![Azione configurata][3]

<table>
	<tr>
		<th>Parametro</th>
		<th>Tipo</th>
		<th>Descrizione del parametro</th>
	</tr>
	<tr>
		<td>Duration In Minutes</td>
		<td>numero intero</td>
		<td>Durata del ritardo in minuti</td>
	</tr>
</table>


## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=July15_HO5-->