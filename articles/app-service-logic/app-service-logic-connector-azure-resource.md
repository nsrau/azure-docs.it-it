<properties
   pageTitle="Connettore di risorse di Azure"
   description="Connettore di risorse di Azure"
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
   ms.date="07/16/2015"
   ms.author="stepsic"/>

#Connettore di risorse di Azure
Il connettore di risorse di Azure offre un modo semplice per gestire le risorse di Azure in app logica.

##Creazione dell'applicazione API
Per utilizzare l'API di App di Azure risorsa connettore, è necessario creare innanzitutto un'istanza. È possibile eseguire questa operazione inline durante la creazione di un'app per la logica o tramite la selezione dell'app per le API del connettore Azure Resource Manger da Azure Marketplace.

Per configurarlo, si verifica è necessario impostare un'entità servizio con le autorizzazioni per eseguire le operazioni che si desidera eseguire in Azure. Tutte le chiamate verranno effettuate per conto di questa entità di servizio configurata. In questo modo è possibile definire l'ambito del connettore per utilizzare solo esattamente ciò che si desidera eseguire e nient'altro.

David Ebbo ha scritto un[post di blog](http://blog.davidebbo.com/2014/12/azure-service-principal.html)su come impostare questa funzionalità. Seguire le istruzioni presenti e ottenere **ID Tenant****ID Client**e**Secret**. Questi tre campi, più il**ID sottoscrizione**sono quelli necessari per configurare il connettore.

##Utilizzando il connettore di risorse di Azure nell'area di progettazione logica App
###Trigger
Esistono due trigger che sono supportati nel connettore:

Nome | Descrizione 
---- | ----------- 
Si verifica l'evento | Trigger quando si verifica un evento a una risorsa nella sottoscrizione. 
Metrica supera la soglia | Si attiva quando una metrica soddisfa una determinata soglia.

###Azione

Analogamente, è possibile fornire un numero elevato di azioni all'interno della sottoscrizione di Azure:
 
Per**gruppi di risorse**è possibile:

Nome | Descrizione 
---- | -----------
Elencare i gruppi di risorse | Elencare tutti i gruppi di risorse nella sottoscrizione.
Ottenere un gruppo di risorse. | Ottenere un gruppo di risorse con il relativo id.
Creare gruppo di risorse | Creare o aggiornare un gruppo di risorse.
Eliminare gruppo di risorse | Eliminare un gruppo di risorse

Per**risorse**è possibile:

Nome | Descrizione 
---- | -----------
Elenco risorse | Elenco delle risorse nel subscription dai diversi tipi di filtri.
Ottenere risorsa | Ottenere una singola risorsa dal relativo ID risorsa
Creare o aggiornare la risorsa | Creare una risorsa o aggiornare una risorsa esistente. È necessario fornire tutte le proprietà per tale risorsa.
Azione risorsa | Eseguire qualsiasi altra operazione su una risorsa. È necessario conoscere il nome dell'azione e il payload (se presente) viene eseguita questa azione.
Eliminazione risorsa | Eliminare una risorsa.

Per**i provider di risorse**è possibile:

Nome | Descrizione 
---- | -----------
Elencare il Provider di risorse | Elencare tutti i provider di risorse disponibili nella sottoscrizione.

Per**le distribuzioni del gruppo di risorse**è possibile:

Nome | Descrizione 
---- | -----------
Elencare distribuzioni | Elencare tutte le distribuzioni in un gruppo di risorse.
Ottenere la distribuzione | Ottenere una distribuzione del modello per il relativo id.
Creazione di distribuzione | Creare una nuova distribuzione del gruppo di risorse fornendo un modello.

Per**eventi**sulle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere gli eventi | Ottenere gli eventi in una sottoscrizione o di una risorsa.

Per**metriche**sulle risorse è possibile:

Nome | Descrizione
---- | -----------
Ottenere Metriche | Ottenere una metrica per una risorsa ID.

## Più vantaggi con il connettore
Dopo aver creato il connettore, è possibile aggiungerlo a un flusso aziendale usando un'app per la logica. Vedere [Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md)

È anche possibile esaminare le statistiche relative alle prestazioni e controllare la sicurezza del connettore. Vedere l'articolo relativo alla [gestione e al monitoraggio delle app per le API e del connettore](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->

<!--Links -->
[Creating a Logic App]: app-service-logic-create-a-logic-app.md

<!---HONumber=July15_HO4-->