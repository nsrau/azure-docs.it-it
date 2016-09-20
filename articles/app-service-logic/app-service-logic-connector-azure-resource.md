<properties
   pageTitle="Uso del connettore Azure Resource nelle app per la logica | Servizio app di Microsoft Azure"
   description="Come creare e configurare l'app per le API o il connettore Azure Resource e usarlo in un'app per la logica nel servizio app di Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>  

# Iniziare a usare il connettore Azure Resource e aggiungerlo all'app per la logica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Usare il connettore Azure Resource per gestire in modo semplice le risorse di Azure nell'app per la logica.

## Creare il connettore Azure Resource
Per usare l'app per le API del connettore Azure Resource, è necessario creare prima di tutto un'istanza. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore Azure Resource da Azure Marketplace.

Per configurarlo, si verifica è necessario impostare un'entità servizio con le autorizzazioni per eseguire le operazioni che si desidera eseguire in Azure. Tutte le chiamate verranno effettuate per conto di questa entità servizio configurata. In questo modo, è possibile definire l'ambito del connettore affinché esegua solo le operazioni desiderate.

David Ebbo ha scritto un [interessante post di blog](http://blog.davidebbo.com/2014/12/azure-service-principal.html) su come impostare questa funzionalità. Seguire le istruzioni presenti e ottenere l'**ID tenant**, l'**ID client** e il **segreto**. Questi tre campi, in aggiunta all'**ID sottoscrizione**, sono necessari per configurare il connettore.

## Uso del connettore Azure Resource Connector nella finestra di progettazione delle app per la logica
### Trigger
Esistono due trigger che sono supportati nel connettore:

Nome | Descrizione
---- | -----------
Si verifica l'evento | Si attiva quando si verifica un evento in una risorsa della sottoscrizione.
Metrica supera la soglia | Si attiva quando una metrica soddisfa una determinata soglia.

### Azione

Analogamente, è possibile includere un numero elevato di azioni nella sottoscrizione Azure.

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
Elencare risorse | Elencare le risorse nella sottoscrizione in base a diversi tipi di filtri.
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
Ottenere eventi | Ottenere gli eventi in una sottoscrizione o per una risorsa.

In **Metriche** relative alle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere metriche | Ottenere una metrica in base a un ID risorsa.

## Altri vantaggi del connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

>[AZURE.NOTE] Per iniziare a usare le app per la logica di Azure prima di registrarsi per ottenere un account Azure, passare a [Prova l'app per la logica](https://tryappservice.azure.com/?appservice=logic), dove è possibile creare immediatamente un'app per la logica iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

Per informazioni di riferimento sull'API REST Swagger, vedere [Informazioni di riferimento su connettori e app per le API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->  

<!--Links -->  
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0907_2016-->