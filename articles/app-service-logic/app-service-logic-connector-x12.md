<properties 
   pageTitle="Connettore BizTalk X12" 
   description="Connettore BizTalk X12" 
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

#Connettore BizTalk X12
Il servizio X12 di Microsoft Azure consente di ricevere e inviare messaggi mediante il protocollo X12 nelle comunicazioni Business to Business. X12 viene comunemente indicato anche come ASC X12 o Accredited Standards Committee X12. Viene ampiamente impiegato in tutti i settori.

##Prerequisiti
- App per le API TPM: prima di creare un connettore X12, è necessario creare un [connettore BizTalk Trading Partner Management][1].
- Database SQL di Azure: ogni app per le API B2B richiede un proprio database SQL di Azure.
- Bus di servizio di Azure: è facoltativo e viene usato solo in caso di invio in batch.

##Uso del connettore BizTalk X12
Per usare il connettore BizTalk X12, è prima di tutto necessario creare un'istanza dell'app per le API del connettore BizTalk X12. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore BizTalk X12 da Azure Marketplace.

##Configurazione del connettore BizTalk X12
I partner commerciali sono le entità coinvolte nelle comunicazioni B2B (Business to Business). Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

I passaggi per la creazione di un contratto tra partner commerciali sono documentati [qui][2].

##Uso del connettore X12 nell'area di progettazione delle app per la logica
Il connettore X12 può essere usato come trigger o come azione.

###Trigger
- Avviare la progettazione del flusso di app per la logica di Azure.
- Fare clic sul connettore X12 dal riquadro destro.

	![Impostazioni di trigger][3]
- Fare clic su ->

	![Opzioni di trigger][4]
- Il connettore BizTalk X12 espone un solo trigger. Selezionare *Release Batch*.

	![Input di Release Batch][5]
- Questo trigger non include input. Fare clic su ->

	![Configurazione di Release Batch][6]
- Come parte dell'output, il connettore restituisce il payload X12, l'ID del contratto nonché informazioni che indicano se il messaggio viene inviato in batch.

###Azione
- Fare clic sul connettore X12 dal riquadro destro.

	![Impostazioni di azione][7]
- Fare clic su ->

	![Elenco di azioni][8]
- Il connettore X12 supporta molte azioni. Selezionare *Encode*.

	![Input di Encode][9]
- Fornire gli input per l'azione e configurarla.

	![Configurazione di Encode][10]

<table>
	<tr>
		<th>Parametro</th>
		<th>Tipo</th>
		<th>Descrizione del parametro</th>
	</tr>
	<tr>
		<td>Content</td>
		<td>stringa</td>
		<td>Messaggio XML</td>
	</tr>
	<tr>
		<td>Agreement ID</td>
		<td>stringa</td>
		<td>ID del contratto</td>
	</tr>
	<tr>
		<td>Is Batched Message</td>
		<td>stringa</td>
		<td>Indicazione dell'invio del messaggio in batch</td>
	</tr>
</table>

L'azione restituisce un oggetto contenente il payload X12.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=July15_HO3-->