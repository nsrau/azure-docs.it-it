<properties 
   pageTitle="Uso del connettore Wait nelle app per la logica | Microsoft Azure App Service" 
   description="Come creare e configurare l'app per le API o il connettore Wait e usarlo in un'app per la logica in Azure App Service" 
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
   ms.date="04/19/2016"
   ms.author="rajram"/>

# Uso del connettore Wait e aggiunta all'app per la logica

>[AZURE.NOTE] Il supporto per questo connettore sta per terminare. La funzionalità del connettore, infatti, è ora inclusa per impostazione predefinita come **azione di ritardo** nella procedura di creazione di nuove app per la logica. È consigliabile aggiornare tutte le app per la logica che usano questo connettore. Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Il connettore Wait consente di ritardare l'esecuzione di un'app per una durata specificata o fino al verificarsi di un orario specificato. È possibile aggiungere il connettore Wait al flusso di lavoro aziendale ed elaborare i dati come parte di questo flusso di lavoro nell'ambito di un'app per la logica. Quando viene usato in un'app per la logica, può consentire di ritardare l'esecuzione.

## Uso del connettore Wait
Per usare il connettore Wait, è innanzitutto necessario creare un'istanza dell'app per le API del connettore Wait. Questa operazione può essere eseguita inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore Wait da Azure Marketplace.

## Uso del connettore Wait nell'area di progettazione delle app per la logica
Il connettore Wait può essere usato come azione. Non include trigger.

### Azione
- Fare clic sul connettore Wait nel riquadro destro: ![Elenco di azioni][1]
- Il connettore Wait supporta due azioni:
	- Delay
	- Delay Until
	 
- Selezionare *Delay*: ![Input relativo al ritardo][2]
- Fornire gli input per l'azione e configurarla: ![Azione configurata][3]

Parametro|Tipo|Descrizione del parametro
---|---|---
Duration In Minutes|integer|Durata del ritardo in minuti


## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

 

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0727_2016-->