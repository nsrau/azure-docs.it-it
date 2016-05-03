<properties 
   pageTitle="Uso del connettore BizTalk Trading Partner Management nelle app per la logica | Microsoft Azure App Service" 
   description="Come creare e configurare l'app per le API o il connettore BizTalk Trading Partner Management e usarlo in un'app per la logica in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
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

# Uso di BizTalk Trading Partner Management e aggiunta all'app per la logica

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


Il servizio BizTalk Trading Partner Management (TPM) consente di definire e mantenere relazioni Business to Business, come partner e contratti, insieme ad elementi associati come schemi e certificati. Queste relazioni possono essere quindi applicate da servizi API correlati come AS2, EDIFACT e X12.

L'app per le API TPM è il requisito di base del connettore AS2 e delle app per le API di X12 o EDIFACT. È possibile aggiungere BizTalk Trading Partner Management al flusso di lavoro aziendale ed elaborare i dati come parte di un flusso di lavoro Business to Business nell'ambito di un'app per la logica.

## Prerequisiti
- Database SQL di Azure vuoto: è necessario creare un database SQL di Azure vuoto prima di creare una nuova app per le API TPM.

## Informazioni su partner, contratti e profili
Ulteriori informazioni su come [creare un contratto tra partner commerciali][1].

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso di lavoro aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova l’app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere [Gestire e monitorare le app per le API e i connettori predefiniti](app-service-logic-monitor-your-connectors.md).

<!--References-->
[1]: app-service-logic-create-a-trading-partner-agreement.md

<!---HONumber=AcomDC_0420_2016-->