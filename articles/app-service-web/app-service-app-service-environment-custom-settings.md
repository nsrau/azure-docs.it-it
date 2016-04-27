<properties 
	pageTitle="Impostazioni personalizzate per gli ambienti del servizio app" 
	description="Impostazioni di configurazione personalizzate per gli ambienti del servizio app" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# Impostazioni di configurazione personalizzate per gli ambienti del servizio app

## Panoramica ##
Gli ambienti del servizio app sono specifici di un singolo cliente: per questo motivo, sono disponibili alcune impostazioni di configurazione che possono essere applicate esclusivamente a un ambiente del servizio app. Questo articolo descrive le diverse personalizzazioni disponibili per l'ambiente del servizio app.

Le personalizzazioni dell'ambiente del servizio app vengono archiviate usando un array nel nuovo attributo "clusterSettings" all'interno del dizionario "Properties" dell'entità ARM *hostingEnvironments*.

Il frammento di modello ARM abbreviato (in basso) mostra l'attributo "clusterSettings":


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L'attributo "clusterSettings" può essere incluso in un modello ARM per aggiornare l'ambiente del servizio app.

In alternativa il valore dell'attributo può essere aggiornato usando [Esplora risorse di Azure](https://resources.azure.com). In Esplora risorse di Azure, andare al nodo per l'ambiente del servizio app (subscriptions --> resourceGroups --> providers --> Micrososft.Web --> hostingEnvironments) e fare clic sull'ambiente del servizio app specifico da aggiornare.

Nella finestra del browser sulla destra, fare clic su "Lettura/Scrittura" nella barra degli strumenti in alto per consentire la modifica interattiva in Esplora risorse. Fare clic sul pulsante "Modifica" blu per rendere il modello ARM modificabile. Scorrere fino alla parte inferiore della finestra del browser sulla destra. L'attributo "clusterSettings" si trova nella parte inferiore, dove è possibile immettere o aggiornare il relativo valore.

Digitare (o copiare e incollare) l'array dei valori di configurazione desiderato all'interno dell'attributo "clusterSettings". Fare clic sul pulsante verde "PUT" nella parte superiore della finestra del browser sulla destra per eseguire il commit della modifica nell'ambiente del servizio app.

Indipendentemente dall'approccio usato per aggiornare l'ambiente del servizio app, dopo aver inviato la modifica, saranno necessari circa 30 minuti moltiplicati per il numero di server front-end dell'ambiente del servizio app perché le modifiche siano attive. Ad esempio se un ambiente del servizio app dispone di quattro front-end, occorreranno circa due ore per completare l'aggiornamento della configurazione. Durante la modifica della configurazione, non sarà possibile eseguire altre operazioni di ridimensionamento o di modifica della configurazione nell'ambiente del servizio app.

## Disabilitazione di TLS 1.0 ##
Una richiesta ricorrente dei clienti, in particolare da parte di coloro che usano controlli di conformità PCI, è la possibilità di disabilitare completamente lo standard TLS 1.0 per le proprie applicazioni.

TLS 1.0 può essere disabilitato con la voce *clusterSettings* seguente:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## Introduzione
Il sito dei modelli ARM di Guida introduttiva di Azure include un modello con la definizione di base per la [creazione di un ambiente del servizio app](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->