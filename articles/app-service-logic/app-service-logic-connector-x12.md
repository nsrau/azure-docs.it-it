<properties 
   pageTitle="Uso del connettore BizTalk X12 nelle app per la logica | Microsoft Azure App Service" 
   description="Come creare e configurare l'app per le API o il connettore BizTalk X12 e usarlo in un'app per la logica in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Uso dell'app per le API di BizTalk X12 e aggiunta all'app per la logica


[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Usare il servizio BizTalk X12 per ricevere e inviare messaggi mediante il protocollo X12 nelle comunicazioni B2B (Business to Business). X12 è anche noto come ASC X12 (Accredited Standards Committee X12) ed è ampiamente usato in vari settori.

È possibile aggiungere l'app per le API di BizTalk X12 al flusso di lavoro aziendale ed elaborare i dati come parte di un flusso di lavoro Business to Business nell'ambito di un'app per la logica.


## Prerequisiti
- App per le API TPM: prima di creare il connettore X12, è necessario creare un [connettore BizTalk Trading Partner Management][1].
- Database SQL di Azure: ogni app per le API B2B richiede un proprio database SQL di Azure.
- Bus di servizio di Azure: è facoltativo e viene usato solo in caso di invio in batch.

## Uso del connettore BizTalk X12
Per usare il connettore BizTalk X12, è innanzitutto necessario creare un'istanza dell'app per le API del connettore BizTalk X12. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore BizTalk X12 da Azure Marketplace.

## Configurazione del connettore BizTalk X12
I partner commerciali sono le entità coinvolte nelle comunicazioni B2B. Quando due partner stabiliscono una relazione, questa è definita Contratto. Il contratto definito si basa sulla comunicazione che i due partner vogliono ottenere ed è specifico del protocollo o del trasporto.

Fare riferimento alla procedura per [creare un contratto tra partner commerciali][2].

## Uso del connettore X12 nell'area di progettazione delle app per la logica
Il connettore X12 può essere usato come trigger o come azione.

### Trigger
- Avviare la progettazione del flusso delle app per la logica di Azure.
- Fare clic sul connettore X12 dal riquadro destro: ![Impostazioni di trigger][3]
- Fare clic su ->: ![Opzioni di trigger][4]
- Il connettore BizTalk X12 espone un solo trigger. Selezionare *Release Batch*: ![Input di Release Batch][5]
- Questo trigger non include input. Fare clic su ->: ![Configurazione di Release Batch][6]
- Come parte dell'output, il connettore restituisce il payload X12, l'ID del contratto nonché informazioni che indicano se il messaggio viene inviato in batch.

### Azione
- Fare clic sul connettore X12 dal riquadro destro: ![Impostazioni di azione][7]
- Fare clic su ->: ![Elenco di azioni][8]
- Il connettore X12 supporta molte azioni. Selezionare *Encode*: ![Input di Encode][9]
- Fornire gli input per l'azione e configurarla: ![Configurazione di Encode][10]

Parametro|Tipo|Descrizione del parametro
---|---|---
Content|string|Messaggio XML
Agreement ID|string|ID del contratto
Is Batched Message|string|Indicazione dell'invio del messaggio in batch

L'azione restituisce un oggetto contenente il payload X12.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 


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

<!---HONumber=AcomDC_0727_2016-->