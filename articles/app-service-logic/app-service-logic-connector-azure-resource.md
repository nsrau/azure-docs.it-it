<properties
   pageTitle="Uso del connettore Azure Resource nelle app per la logica | Microsoft Azure App Service"
	description="Come creare e configurare l'app per le API o il connettore Azure Resource e usarlo in un'app per la logica in Azure App Service"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="stepsic-microsoft-com"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="stepsic"/>

# Uso del connettore Azure Resource e aggiunta all'app per la logica 
Usare il connettore Azure Resource per gestire in modo semplice le risorse di Azure nell'app per la logica.

## Creare il connettore Azure Resource
Per usare l'app per le API del connettore Azure Resource, è necessario creare innanzitutto un'istanza. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore Azure Resource da Azure Marketplace.

Per configurarlo, si verifica è necessario impostare un'entità servizio con le autorizzazioni per eseguire le operazioni che si desidera eseguire in Azure. Tutte le chiamate verranno effettuate per conto di questa entità servizio configurata. In questo modo, è possibile definire l'ambito del connettore affinché esegua solo le operazioni desiderate.

David Ebbo ha scritto un [interessante post di blog](http://blog.davidebbo.com/2014/12/azure-service-principal.html) su come impostare questa funzionalità. Seguire le istruzioni presenti e ottenere l'**ID tenant**, l'**ID client** e il **segreto**. Questi tre campi, in aggiunta all'**ID sottoscrizione**, sono necessari per configurare il connettore.

## Uso del connettore Azure Resource nell'area di progettazione delle app per la logica
### Trigger
Esistono due trigger che sono supportati nel connettore:

Nome | Descrizione 
---- | ----------- 
Si verifica l'evento | Si attiva quando si verifica un evento in una risorsa della sottoscrizione. 
Metrica supera la soglia | Si attiva quando una metrica soddisfa una determinata soglia.

### Azione

Analogamente, è possibile fornire un numero elevato di azioni nella sottoscrizione Azure:
 
In **Gruppi di risorse** è possibile:

Nome | Descrizione 
---- | -----------
Elencare i gruppi di risorse | Elencare tutti i gruppi di risorse nella sottoscrizione.
Ottenere un gruppo di risorse | Ottenere un gruppo di risorse con il relativo ID.
Creare un gruppo di risorse | Creare o aggiornare un gruppo di risorse.
Eliminare un gruppo di risorse | Eliminare un gruppo di risorse.

In **Risorse** è possibile:

Nome | Descrizione 
---- | -----------
Elencare risorse | Elencare le risorse nella sottoscrizione in base ai diversi tipi di filtri.
Ottenere una risorsa | Ottenere una singola risorsa in base al relativo ID risorsa.
Creare o aggiornare una risorsa | Creare una risorsa o aggiornare una risorsa esistente. È necessario fornire tutte le proprietà per tale risorsa.
Azione risorsa | Eseguire qualsiasi altra operazione su una risorsa. È necessario conoscere il nome dell'azione e il payload (se presente) eseguito dall'azione.
Eliminare una risorsa | Eliminare una risorsa.

In **Provider di risorse** è possibile:

Nome | Descrizione 
---- | -----------
Elencare i provider di risorse | Elencare tutti i provider di risorse disponibili nella sottoscrizione.

In **Distribuzioni del gruppo di risorse** è possibile:

Nome | Descrizione 
---- | -----------
Elencare distribuzioni | Elencare tutte le distribuzioni in un gruppo di risorse.
Ottenere una distribuzione | Ottenere una distribuzione del modello in base al relativo ID.
Creare una distribuzione | Creare una nuova distribuzione del gruppo di risorse fornendo un modello.

In **Eventi** relativi alle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere eventi | Ottenere gli eventi in una sottoscrizione o di una risorsa.

In **Metriche** relative alle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere metriche | Ottenere una metrica in base a un ID risorsa.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=August15_HO9-->