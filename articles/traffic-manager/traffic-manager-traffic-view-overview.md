---
title: Visualizzazione traffico di Gestione traffico di Microsoft Azure
description: Introduzione a Visualizzazione di traffico di Gestione traffico
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 39d6090b14a16f505413154df2a78d42b3fb3f3c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333704"
---
# <a name="traffic-manager-traffic-view"></a>Visualizzazione traffico di Gestione traffico

Gestione traffico fornisce il routing a livello DNS in modo che gli utenti finali vengano indirizzati a endpoint integri in base al metodo di routing specificato quando è stato creato il profilo. Visualizzazione traffico fornisce Gestione traffico con una visualizzazione delle basi utenti (a livello di granularità del resolver DNS) e del relativo modello di traffico. Quando si abilita Visualizzazione traffico, tali informazioni vengono elaborate per offrire approfondimenti attuabili. 

Con Visualizzazione traffico è possibile:
- comprendere dove si trovano le basi utente (fino a una granularità di livello del resolver DNS locale).
- visualizzare il volume di traffico (osservato come query DNS gestite da Gestione traffico di Azure) proveniente da tali aree.
- sapere qual è la latenza rappresentativa riscontrata dagli utenti.
- immergersi in modelli di traffico specifici da ognuna di queste basi utente alle aree di Azure in cui sono presenti endpoint. 

Ad esempio, è possibile usare Visualizzazione traffico per comprendere quali aree hanno un numero elevato di traffico ma sono soggette a latenze più elevate. È quindi possibile usare queste informazioni per pianificare l'espansione footprint a nuove aree di Azure in modo che questi utenti possano avere l'esperienza di una latenza più bassa.

## <a name="how-traffic-view-works"></a>Funzionamento di Visualizzazione traffico

Visualizzazione traffico fa in modo che Gestione traffico esamini le query in ingresso ricevute negli ultimi sette giorni da un profilo che ha questa funzionalità abilitata. Dalle informazioni delle query in ingresso, Visualizzazione traffico estrae l'IP di origine del resolver DNS che viene usato come rappresentazione della posizione degli utenti. Questi vengono quindi raggruppati in una granularità a livello del resolver DNS per creare aree di basi utente usando le informazioni geografiche degli indirizzi IP gestiti da Gestione traffico. Gestione traffico analizza quindi le aree di Azure a cui la query è stata indirizzata e costruisce una mappa del flusso di traffico per gli utenti da tali aree.  
Nel passaggio successivo Gestione traffico mette in correlazione l'area della base utenti con il mapping delle aree di Azure con le tabelle di latenza della intelligence di rete che mantiene per reti di utenti finali diverse per comprendere la latenza media riscontrata dagli utenti di tali aree quando si connettono ad aree di Azure. Tutti questi calcoli vengono quindi combinati a un livello di IP del resolver DNS locale prima di essere presentati all'utente. È possibile usare le informazioni in vari modi.

La frequenza di aggiornamento dei dati di visualizzazione traffico dipende da più variabili di servizio interne. Tuttavia, i dati vengono in genere aggiornati ogni 24 ore.

>[!NOTE]
>La latenza descritta in Visualizzazione traffico è una latenza rappresentativa tra l'utente finale e le aree di Azure a cui è stato connesso e non è la latenza di ricerca DNS. Visualizzazione traffico fa una stima del massimo sforzo della latenza tra il resolver DNS locale e l'area di Azure a cui è stata indirizzata la query. Se i dati disponibili sono insufficienti, la latenza restituita sarà Null. 

## <a name="visual-overview"></a>Panoramica della visualizzazione

Quando si accede alla sezione **Visualizzazione traffico** della pagina di Gestione traffico, viene visualizzata una mappa geografica con una sovrimpressione delle informazioni dettagliate di Visualizzazione traffico. La mappa fornisce informazioni sulla base di utenti e gli endpoint per il profilo di Gestione traffico.

### <a name="user-base-information"></a>Informazioni sulla base di utenti

Nella mappa vengono visualizzati i resolver DNS locali per cui sono disponibili le informazioni sulla posizione. Il colore del resolver DNS indica la latenza media riscontrata dagli utenti finali che hanno usato il resolver DNS per le query di Gestione traffico.

Se si passa il mouse su una posizione del resolver DNS nella mappa, verranno visualizzati i dati seguenti:
- l'indirizzo IP del resolver DNS
- il volume di traffico delle query DNS rilevato da Gestione traffico dall'indirizzo
- gli endpoint a cui è stato indirizzato il traffico dal resolver DNS, come linea tra l'endpoint e il resolver DNS 
- la latenza media da tale posizione all'endpoint, rappresentata come colore della linea di connessione

### <a name="endpoint-information"></a>Informazioni sugli endpoint

Le aree di Azure in cui risiedono gli endpoint vengono indicate come punti blu nella mappa. Se l'endpoint è esterno e non è mappata alcuna area di Azure, l'endpoint viene visualizzato nella parte superiore della mappa. Fare clic su un endpoint per visualizzare i diversi percorsi,in base al resolver DNS usato, da cui il traffico è stato indirizzato a questo endpoint. Le connessioni vengono indicate come linea tra l'endpoint e la posizione del resolver DNS e il colore varia in base alla latenza rappresentativa all'interno di tale coppia. È possibile anche esaminare il nome dell'endpoint, l'area di Azure in cui viene eseguito e il volume totale di richieste che sono state indirizzate a essa da questo profilo di Gestione traffico.


## <a name="tabular-listing-and-raw-data-download"></a>Elenco tabulare e download di dati non elaborati

È possibile visualizzare i dati di Visualizzazione traffico in formato tabulare nel portale di Azure. Esiste una voce per ogni coppia di indirizzo IP/endpoint del resolver DNS che mostra l'indirizzo IP del resolver DNS, il nome e la posizione geografica dell'area di Azure in cui risiede l'endpoint, se disponibile, il volume di richieste associate a quel resolver DNS a quell'endpoint e la latenza rappresentativa associata agli utenti finali che usano quel DNS, se disponibile. È possibile anche scaricare i dati di Visualizzazione traffico come file CSV che possono essere usati come parte di un flusso di lavoro di analisi scelto.

## <a name="billing"></a>Fatturazione

Quando si usa Visualizzazione traffico, la fatturazione viene eseguita in base al numero di punti dati usati per creare le informazioni presentate. L'unico tipo di punto dati attualmente usato corrisponde alle query ricevute dal profilo di Gestione traffico. Per altri dettagli sui prezzi, visitare la [pagina dei prezzi di Gestione Traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Domande frequenti

* [Che cosa fa Visualizzazione traffico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-does-traffic-view-do)

* [Come è possibile trarre vantaggio dall'utilizzo di Visualizzazione traffico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-benefit-from-using-traffic-view)

* [In che modo Visualizzazione traffico diverse le metriche di Traffic Manager disponibili tramite monitoraggio di Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Visualizzazione traffico USA le informazioni sulla subnet del client EDNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-view-use-edns-client-subnet-information)

* [Quanti giorni di dati Visualizzazione traffico usare?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-days-of-data-does-traffic-view-use)

* [In che modo Visualizzazione traffico gestisce gli endpoint esterni?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-handle-external-endpoints)

* [È necessario abilitare Visualizzazione traffico per ogni profilo nella sottoscrizione?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Come è possibile disattivare Visualizzazione traffico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-turn-off-traffic-view)

* [Come funziona la fatturazione Visualizzazione traffico?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [funzionamento di Gestione traffico](traffic-manager-overview.md)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

