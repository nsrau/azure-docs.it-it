---
title: Ripristino di emergenza geografico e disponibilità elevata di Funzioni di AzureAzure Functions geo-disaster recovery and high availability
description: Come usare le aree geografiche per la ridondanza ed eseguire il failover in Funzioni di Azure.How to use geographical regions for redundancy and to fail over in Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080233"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Ripristino di emergenza di Azure FunzioniAzure Functions geo-disaster recovery

Quando intere aree di Azure o data center riscontrano tempi di inattività, è fondamentale che il calcolo continui l'elaborazione in un'area diversa.  In questo articolo verranno illustrate alcune delle strategie che è possibile utilizzare per distribuire le funzioni per consentire il ripristino di emergenza.

## <a name="basic-concepts"></a>Concetti fondamentali

Funzioni di Azure eseguite in un'area specifica.  Per ottenere una maggiore disponibilità, è possibile distribuire le stesse funzioni in più aree.  Quando in più aree è possibile avere le funzioni in esecuzione nel modello *attivo/attivo* o *attivo/passivo.*  

* Attivo/attivo. Entrambe le aree sono attive e ricevono eventi (duplicati o rotazionali). Attivo/attivo è consigliato per le funzioni HTTPS in combinazione con lo sportello anteriore di Azure.Active/active is recommended for HTTPS functions in combination with Azure Front Door.
* Attivo/passivo. Un'area è attiva e riceve eventi, mentre una secondaria è inattiva.  Quando è necessario il failover, l'area secondaria viene attivata e assume l'elaborazione.  Questa operazione è consigliata per le funzioni non HTTP, ad esempio il bus di servizio e gli hub eventi.

Per altre informazioni sulle distribuzioni in più aree, vedere Come [eseguire app in più aree.](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="activeactive-for-https-functions"></a>Attivo/attivo per le funzioni HTTPS

Per ottenere distribuzioni attive/attive di funzioni, è necessario un componente in grado di coordinare gli eventi tra entrambe le aree.  Per le funzioni HTTPS, questo coordinamento viene eseguito usando [Porta frontale di Azure.](../frontdoor/front-door-overview.md)  Il front gate di Azure può instradare e round robin le richieste HTTPS tra più funzioni regionali.  Controlla inoltre periodicamente l'integrità di ogni endpoint.  Se una funzione regionale smette di rispondere ai controlli di integrità, la porta frontale di Azure lo toglierà dalla rotazione e inoltrerà solo il traffico alle funzioni integre.  

![Architettura per la porta e la funzione anteriore di AzureArchitecture for Azure Front Door and Function](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Attivo/attivo per funzioni non HTTPS

È comunque possibile ottenere distribuzioni attive/attive per le funzioni non HTTPS.  Tuttavia, è necessario considerare il modo in cui le due aree interagiranno o si coordineranno tra loro.  Se la stessa app per le funzioni fosse distribuita in due aree, ognuna attiva nella stessa coda del bus di servizio, fungerebbero da consumer concorrenti per l'eliminazione della coda.  Anche se questo significa che ogni messaggio viene elaborato solo da una delle istanze, significa anche che è ancora presente un singolo punto di errore nel singolo bus di servizio.  Se si distribuiscono due code del bus di servizio (una in un'area primaria, una in un'area secondaria) e le due app per le funzioni che puntano alla coda dell'area, la verifica viene ora in che modo i messaggi della coda vengono distribuiti tra le due aree.  Spesso ciò significa che ogni editore tenta di pubblicare un messaggio in *entrambe le* aree e ogni messaggio viene elaborato da entrambe le app per le funzioni attive.  Mentre questo crea un modello attivo/attivo, crea altre sfide per la duplicazione del calcolo e quando o come i dati vengono consolidati.  Per questi motivi, è consigliabile che i trigger non HTTPS utilizzino il modello attivo/passivo.

## <a name="activepassive-for-non-https-functions"></a>Attivo/passivo per funzioni non HTTPS

Attivo/passivo consente a una sola funzione di elaborare ogni messaggio, ma fornisce un meccanismo per eseguire il failover a un'area secondaria in caso di emergenza.  Funzioni di Azure funziona insieme al ripristino geografico del bus di servizio di Azure e al ripristino geografico degli hub eventi di Azure.Azure Functions works alongside [Azure Service Bus geo-recovery](../service-bus-messaging/service-bus-geo-dr.md) and [Azure Event Hubs geo-recovery](../event-hubs/event-hubs-geo-dr.md).

Usando i trigger di Hub eventi di Azure come esempio, il modello attivo/passivo comporterebbe quanto segue:Using Azure Event Hubs triggers as an example, the active/passive pattern would involve the following:

* Hub eventi di Azure distribuito in un'area primaria e secondaria.
* Area geografica-disastro abilitata per associare l'hub eventi primario e secondario.  Questo crea anche un "alias" che è possibile utilizzare per connettersi agli hub eventi e passare da primario a secondario senza modificare le informazioni di connessione.
* App per funzioni distribuite in un'area primaria e secondaria.
* Le app per le funzioni vengono attivate nella stringa di connessione *diretta* (non alias) per il rispettivo hub eventi. 
* I server di pubblicazione nell'hub eventi devono essere pubblicati nella stringa di connessione dell'alias. 

![Architettura di esempio attivo-passivo](media/functions-geo-dr/active-passive.png)

Prima del failover, i server di pubblicazione che inviano all'alias condiviso verranno instradati all'hub eventi primario.  L'app per le funzioni primarie è in ascolto esclusivamente sull'hub eventi primario.  L'app per le funzioni secondarie sarà passiva e inattiva.  Non appena viene avviato il failover, i server di pubblicazione che inviano all'alias condiviso verranno ora indirizzati all'hub eventi secondario.  L'app per le funzioni secondarie diventerà attiva e inizierà ad attivarsi automaticamente.  Il failover effettivo in un'area secondaria può essere guidato interamente dall'hub eventi, con le funzioni che diventano attive solo quando il rispettivo hub eventi è attivo.

Altre informazioni su informazioni e considerazioni per il failover con [il bus di](../service-bus-messaging/service-bus-geo-dr.md) servizio e gli hub [eventi](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Passaggi successivi

* [Creare lo sportello anteriore di AzureCreate Azure Front Door](../frontdoor/quickstart-create-front-door.md)
* [Considerazioni sul failover degli hub eventi](../event-hubs/event-hubs-geo-dr.md#considerations)
