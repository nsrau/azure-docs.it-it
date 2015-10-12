<properties 
   pageTitle="Uso del connettore BizTalk Edifact nelle app per la logica | Microsoft Azure App Service" 
   description="Come creare e configurare l'app per le API o il connettore BizTalk Edifact e usarlo in un'app per la logica in Azure App Service" 
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
   ms.date="08/23/2015"
   ms.author="rajram"/>

# Uso del connettore BizTalk Edifact e aggiunta all'app per la logica  
Usare il servizio Edifact per ricevere e inviare messaggi mediante il protocollo Edifact nelle comunicazioni B2B (Business to Business). Edifact è anche noto come ASC Edifact (Accredited Standards Committee Edifact) ed è ampiamente usato in vari settori.

È possibile aggiungere il connettore BizTalk Edifact al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica.

## Prerequisiti
- App per le API TPM: prima di creare un connettore Edifact, è necessario creare un [connettore BizTalk Trading Partner Management][1].
- Database SQL di Azure: ogni app per le API B2B richiede un proprio database SQL di Azure.
- Bus di servizio di Azure: è facoltativo e viene usato solo in caso di invio in batch.

## Uso del connettore Edifact
Per usare il connettore Edifact, è innanzitutto necessario creare un'istanza dell'app per le API del connettore AS2. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore AS2 da Azure Marketplace.

## Configurazione del connettore Edifact
I partner commerciali sono le entità coinvolte nelle comunicazioni B2B. Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

I passaggi relativi alla creazione di un contratto tra partner commerciali sono documentati in [questo articolo][2].

## Uso del connettore Edifact nell'area di progettazione delle app per la logica
Il connettore Edifact può essere usato come trigger o come azione.

### Trigger
- Avviare la progettazione del flusso di app per la logica di Azure.
- Fare clic sul connettore Edifact nel riquadro destro:  
![Impostazioni di trigger][3]
- Fare clic su ->:  
![Opzioni di trigger][4]
- Il connettore EDIFACT espone un solo trigger. Selezionare *Release Batch*:  
![Input di Release Batch][5]
- Questo trigger non include input. Fare clic su ->:  
![Configurazione di Release Batch][6]
- Come parte dell'output, il connettore restituisce il payload Edifact, l'ID del contratto nonché informazioni che indicano se il messaggio viene inviato in batch.

### Azione
- Fare clic sul connettore Edifact nel riquadro destro:  
![Impostazioni di azione][7]
- Fare clic su ->:  
![Elenco di azioni][8]
- Il connettore Edifact supporta molte azioni. Selezionare *Encode*:  
![Input di Encode][9]
- Fornire gli input per l'azione e configurarla:  
![Configurazione di Encode][10]

	Parametro|Tipo|Descrizione del parametro
---|---|---
Content|string|Messaggio XML
Agreement ID|integer|ID del contratto
Is Batched Message|boolean|Indicazione dell'invio del messaggio in batch
Data Element Separator|string|Separatore di elementi dati
Component Separator|string|Separatore di componenti
Segment Terminator|string|Carattere di terminazione di segmento
Decimal Point Indicator|string|Indicatore del separatore decimale
Repetition Separator|string|Separatore di ripetizioni
Escape Character|string|Carattere di escape
Replacement Character|string|Carattere di sostituzione
Segment Terminator Suffix|string|Suffisso del carattere di terminazione di segmento

L'azione restituisce un oggetto contenente il payload EDIFACT al termine dell'esecuzione.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

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

<!---HONumber=Oct15_HO1-->