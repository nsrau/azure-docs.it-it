<properties 
   pageTitle="Connettore AS2" 
   description="Connettore AS2" 
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
   ms.date="06/14/2015"
   ms.author="rajram"/>

#Connettore AS2
Il connettore AS2 di Microsoft Azure consente di ricevere e inviare messaggi mediante il protocollo di trasporto AS2 nelle comunicazioni Business to Business. AS2 è l'acronimo di Applicability Statement 2. I dati vengono trasportati in modo sicuro e affidabile tramite Internet. La sicurezza viene garantita usando certificati digitali e la crittografia.

##Prerequisiti
- App per le API TPM: prima di creare un connettore AS2, è necessario creare un [connettore BizTalk Trading Partner Management][1].
- Database SQL di Azure: ogni app per le API B2B richiede un proprio database SQL di Azure.
- Contenitore di Archiviazione BLOB di Azure: archivia le proprietà dei messaggi quando è abilitata l'archiviazione AS2. Se l'archiviazione dei messaggi AS2 non è necessaria, il contenitore di archiviazione non è necessario. 

##Uso del connettore AS2
Per usare il connettore AS2, prima è necessario creare un'istanza dell'app per le API del connettore AS2. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore AS2 da Azure Marketplace.

##Configurazione del connettore AS2
I partner commerciali sono le entità coinvolte nelle comunicazioni B2B (Business to Business). Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

I passaggi per la creazione di un contratto tra partner commerciali sono documentati [qui][2].

##Uso del connettore AS2 nell'area di progettazione delle app per la logica
Il connettore AS2 può essere usato come trigger o come azione.

###Trigger
- Avviare la progettazione del flusso di app per la logica di Azure.
- Fare clic sul connettore AS2 dal riquadro destro.

	![Impostazioni di trigger][3]
- Fare clic su ->

	![Opzioni di trigger][4]
- Il connettore AS2 espone un solo trigger. Selezionare *Receive & Decode*.

	![Input di Receive & Decode][5]
- Questo trigger non include input. Fare clic su ->

	![Configurazione di Receive & Decode][6]
- Come parte dell'output, il connettore restituisce il payload AS2, nonché i metadati specifici di AS2.

###Azione
- Fare clic sul connettore AS2 dal riquadro destro.

	![Impostazioni di azione][7]
- Fare clic su ->

	![Elenco di azioni][8]
- Il connettore AS2 supporta solo un'azione. Selezionare *Encode & Send*.

	![Input di Encode & Send][9]
- Fornire gli input per l'azione e configurarla.

	![Configurazione di Encode & Send][10]

<table>
	<tr>
		<th>Parametro</th>
		<th>Tipo</th>
		<th>Descrizione del parametro</th>
	</tr>
	<tr>
		<td>Payload</td>
		<td>oggetto</td>
		<td>Payload</td>
	</tr>
	<tr>
		<td>AS2 From</td>
		<td>stringa</td>
		<td>AS2 di origine</td>
	</tr>
	<tr>
		<td>AS2 To</td>
		<td>stringa</td>
		<td>AS2 di destinazione</td>
	</tr>
	<tr>
		<td>Partner URL</td>
		<td>stringa</td>
		<td>URL del partner</td>
	</tr>
	<tr>
		<td>Enable Archiving</td>
		<td>booleano</td>
		<td>Abilita l'archiviazione</td>
	</tr>
</table>

L'azione restituisce un codice di risposta HTTP 200 al termine dell'esecuzione.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->