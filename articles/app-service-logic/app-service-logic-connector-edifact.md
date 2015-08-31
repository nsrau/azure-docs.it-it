<properties 
   pageTitle="Connettore BizTalk Edifact" 
   description="Connettore BizTalk Edifact" 
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

# Connettore BizTalk Edifact
Il servizio Edifact di Microsoft Azure consente di ricevere e inviare messaggi mediante il protocollo Edifact nelle comunicazioni Business to Business. Edifact viene comunemente indicato anche come ASC Edifact o Accredited Standards Committee Edifact. Viene ampiamente impiegato in tutti i settori.

## Prerequisiti
- App per le API TPM: prima di creare un connettore Edifact, è necessario creare un [connettore BizTalk Trading Partner Management][1].
- Database SQL di Azure: ogni app per le API B2B richiede un proprio database SQL di Azure.
- Bus di servizio di Azure: è facoltativo e viene usato solo in caso di invio in batch.

## Uso del connettore Edifact
Per usare il connettore Edifact, prima è necessario creare un'istanza dell'app per le API del connettore AS2. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore AS2 da Azure Marketplace.

## Configurazione del connettore Edifact
I partner commerciali sono le entità coinvolte nelle comunicazioni B2B (Business to Business). Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

I passaggi per la creazione di un accordo tra partner commerciali sono descritti [qui][2].

## Uso del connettore Edifact nell'area di progettazione delle app per la logica
Il connettore Edifact può essere usato come trigger o come azione.

### Trigger
- Avviare la progettazione del flusso di app per la logica di Azure.
- Fare clic su Edifact Connector nel riquadro destro:![Impostazioni di trigger][3]
- Fare clic su ->: ![Opzioni di trigger][4]
- Il connettore Edifact espone un solo trigger. Selezionare *Release Batch*: ![Input di Release Batch][5]
- Questo trigger non include input. Fare clic su ->: ![Configurazione di Release Batch][6]
- Come parte dell'output, il connettore restituisce il payload Edifact, l'ID del contratto nonché informazioni che indicano se il messaggio viene inviato in batch.

### Azione
- Fare clic su Edifact Connector nel riquadro destro:![Impostazioni di azione][7]
- Fare clic su ->: ![Elenco di azioni][8]
- Il connettore Edifact supporta molte azioni. Selezionare *Encode*: ![Input di Encode][9]
- Fornire gli input per l'azione e configurarla: ![Configurazione di Encode][10]

	Parametro|Tipo|Descrizione del parametro
---|---|---
Content|stringa|Messaggio XML
Agreement ID|numero intero|ID del contratto
Is Batched Message|booleano|Indicazione dell'invio del messaggio in batch
Data Element Separator|stringa|Separatore di elementi dati
Component Separator|stringa|Separatore di componenti
Segment Terminator|stringa|Carattere di terminazione di segmento
Decimal Point Indicator|stringa|Indicatore del separatore decimale
Repetition Separator|stringa|Separatore di ripetizioni
Escape Character|stringa|Carattere di escape
Replacement Character|stringa|Carattere di sostituzione
Segment Terminator Suffix|stringa|Suffisso del carattere di terminazione di segmento

L'azione restituisce un oggetto contenente il payload EDIFACT al termine dell'esecuzione.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-edifact/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-edifact/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-edifact/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-edifact/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-edifact/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-edifact/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-edifact/EncodeConfigured.PNG

<!---HONumber=August15_HO8-->