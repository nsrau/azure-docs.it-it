---
title: Pull di dati pubblici in Hub eventi di Azure | Documentazione Microsoft
description: Panoramica dell&quot;importazione di hub eventi da un esempio Web
services: event-hubs
documentationcenter: na
author: spyrossak
manager: timlt
editor: 
ms.assetid: 2a5d7f9e-efd3-4200-908c-a8d7418b1d43
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: spyros;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 243191631e102ac4d13cb6a0e786c7e35e5ddf1a


---
# <a name="pulling-public-data-into-azure-event-hubs"></a>Pull di dati pubblici in Hub eventi di Azure
In scenari tipici di Internet delle cose (IoT) sono presenti dispositivi per i quali è possibile programmare un push dei dati in Azure, a un hub eventi di Azure o a un hub IoT. Questi hub sono punti di ingresso in Azure per l'archiviazione, l'analisi e la visualizzazione tramite i numerosi strumenti disponibili in Microsoft Azure. Entrambi gli hub richiedono tuttavia che venga eseguito il push dei dati, formattati come JSON e protetti in modi specifici. Questo porta alla domanda seguente. Come si deve procedere se si vuole importare dati da origini pubbliche o private in cui i dati sono esposti come servizio Web o feed generico, ma non si è in grado di modificare la modalità di pubblicazione dei dati? Si consideri il meteo, il traffico o le quotazioni azionarie: non è possibile chiedere a enti quali NOAA (National Oceanic and Atmospheric Administration), WSDOT (Washington State Department of Transportation) o NASDAQ (National Association of Securities Dealers Automated Quotation) di configurare un'operazione push all'hub eventi. Per risolvere questo problema, è stato scritto e reso open source un piccolo esempio di cloud, modificabile e distribuibile, che eseguirà il pull dei dati da un'origine di questo tipo e ne eseguirà il push all'hub eventi. A quel punto sarà possibile eseguire le operazioni desiderate, ovviamente soggette alle condizioni di licenza del produttore. L'applicazione è disponibile [qui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Si noti che il codice in questo esempio illustra solo come eseguire il pull dei dati da feed Web tipici e come scriverli in un hub eventi di Azure. L'applicazione NON è stata progettata come applicazione di produzione e non sono stati eseguiti tentativi di renderla adatta all'uso in tale ambiente. Si tratta solo ed esclusivamente di un esempio destinato agli sviluppatori. L'esistenza di questo esempio NON corrisponde inoltre all'indicazione che è necessario eseguire il **pull** dei dati in Azure anziché il **push**. Prima di stabilire un'architettura end-to-end, è consigliabile esaminare sicurezza, prestazioni, funzionalità e fattori di costo.

## <a name="application-structure"></a>Struttura dell'applicazione
L'applicazione è scritta in C# e la [descrizione d'esempio](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contiene tutte le informazioni necessarie per modificarla, compilarla e pubblicarla. Le sezioni seguenti contengono una panoramica generale delle funzionalità dell'applicazione.

Si presuppone che si abbia accesso a un feed di dati. È ad esempio possibile eseguire il pull dei dati relativi al traffico dal WSDOT o dei dati meteorologici dalla NOAA per visualizzare i report personalizzati o per combinare i dati con altri dati nell'applicazione. Sarà inoltre necessario avere configurato un hub eventi di Azure e conoscere la stringa di connessione necessaria per accedervi.

Quando la soluzione GenericWebToEH si avvia, legge un file di configurazione (App.config) per ottenere una serie di elementi:

1. L'URL o un elenco di URL per il sito che pubblica i dati. In teoria si tratta di un sito che pubblica i dati in formato JSON, come quelli a cui fa riferimento WSDOT [qui](http://www.wsdot.wa.gov/Traffic/api/). 
2. Le credenziali per l'URL, se necessario. Molte origini pubbliche non richiedono le credenziali oppure è possibile inserirle nella stringa dell'URL. Per altre origini, le credenziali devono essere specificate separatamente. Si noti che è possibile specificare solo un set di credenziali in questa applicazione, che quindi funzionerà solo se si specifica un singolo URL e non un elenco di URL.
3. La stringa di connessione e il nome dell'hub eventi nello spazio dei nomi di Hub eventi in cui si effettuerà il push dei dati. Queste informazioni sono disponibili nel portale di Azure.
4. Un intervallo di sospensione, in millisecondi, per l'intervallo di polling del sito di dati pubblici. L'impostazione di questa opzione richiede una certa cautela. Se si esegue il polling troppo raramente, si potrebbero perdere dati. D'altra parte, se si esegue il polling troppo frequentemente, si possono ottenere molti dati ripetitivi o si può persino essere bloccati come robot dannosi. Considerare la frequenza di aggiornamento dell'origine dati: i dati relativi al meteo o al traffico possono essere aggiornati ogni 15 minuti, ma le quotazioni azionarie devono essere aggiornate ogni pochi secondi, in base alla provenienza dei dati. 
5. Un flag per indicare all'applicazione se i dati in entrata sono in formato JSON o XML. Poiché è necessario eseguire il push dei dati a un hub eventi, l'applicazione include un modulo per convertire il formato XML in JSON prima dell'invio.

Dopo aver letto il file di configurazione, l'applicazione viene eseguita in un ciclo infinito: accede al sito Web pubblico, converte i dati se necessario, li scrive nell'hub eventi e quindi attende l'intervallo di sospensione prima di ricominciare. In particolare:

* Lettura del sito Web pubblico. Per la ricezione di dati pronti per l'invio, l'istanza della classe RawXMLWithHeaderToJsonReader è usata da Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs, che legge il flusso di origine nel metodo GetData() e quindi lo suddivide in parti più piccole (ad esempio in record) tramite GetXmlFromOriginalText. 
  Questo metodo leggerà il formato XML nonché il formato JSON corretto o una matrice JSON. Viene avviata l'elaborazione usando la configurazione MergeToXML da App.config (predefinito=vuoto).
* I dati di ricezione e invio vengono implementati in un ciclo infinito nel metodo Process() in Program.cs. 
  Dopo la ricezione dei risultati di output da GetData(), il metodo accoda valori separati nell'hub eventi.

## <a name="next-steps"></a>Passaggi successivi
Per distribuire la soluzione, clonare o scaricare l'applicazione [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , modificare il file App.config, compilarlo e infine pubblicarlo. Dopo aver pubblicato l'applicazione, è possibile visualizzarla in esecuzione nel portale di Azure classico in Servizi Cloud e modificare alcune delle impostazioni di configurazione, ad esempio la destinazione dell'hub eventi e l'intervallo di sospensione, nella scheda **Configura** .

Per altri esempi relativi agli hub eventi, vedere la [raccolta di esempi di Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) e [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).




<!--HONumber=Nov16_HO3-->


