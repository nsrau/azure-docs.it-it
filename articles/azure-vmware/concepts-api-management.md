---
title: Concetti-gestione API
description: Informazioni su come gestione API protegge le API in esecuzione in macchine virtuali (VM) della soluzione VMware di Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: f412ee81fc77435f2586a31c1bf6f6bdf22c66e2
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670357"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Gestione API per pubblicare e proteggere le API in esecuzione in macchine virtuali basate su soluzioni VMware di Azure

[Gestione API](https://azure.microsoft.com/services/api-management/) di Microsoft Azure consente di pubblicare in modo sicuro in consumer interni o esterni.  Solo gli SKU Developer e Premium consentono all'integrazione della rete virtuale di Azure di pubblicare le API in esecuzione nei carichi di lavoro della soluzione VMware di Azure.  Entrambi gli SKU abilitano in modo sicuro la connettività tra il servizio gestione API e il back-end. 

>[!NOTE]
>Lo SKU Developer è progettato per lo sviluppo e il test, mentre lo SKU Premium è per le distribuzioni di produzione.

La configurazione di gestione API è la stessa per i servizi back-end eseguiti su macchine virtuali (VM) della soluzione VMware di Azure e in locale. Per entrambe le distribuzioni, gestione API configura l'IP virtuale (VIP) nel servizio di bilanciamento del carico come endpoint back-end quando il server back-end è posizionato dietro una NSX Load Balancer nella soluzione VMware di Azure. 


## <a name="external-deployment"></a>Distribuzione esterna

Una distribuzione esterna pubblica le API utilizzate dagli utenti esterni tramite un endpoint pubblico. Gli sviluppatori e i tecnici DevOps possono gestire le API tramite il portale di Azure o PowerShell e il portale per sviluppatori di gestione API.

Il diagramma di distribuzione esterna Mostra l'intero processo e gli attori coinvolti (visualizzati nella parte superiore). Gli attori sono:

- **Amministratore/i:** Rappresenta il team di amministrazione o DevOps, che gestisce la soluzione VMware di Azure tramite i meccanismi di automazione e portale di Azure come PowerShell o Azure DevOps.

- **Utenti:**  Rappresenta i consumer delle API esposte e rappresenta sia gli utenti che i servizi che utilizzano le API.

Il flusso del traffico passa attraverso l'istanza di gestione API, che astrae i servizi back-end, collegati alla rete virtuale dell'hub. Il gateway ExpressRoute instrada il traffico al canale di Copertura globale ExpressRoute e raggiunge una NSX Load Balancer la distribuzione del traffico in ingresso nelle diverse istanze di servizi back-end.

Gestione API ha un'API pubblica di Azure ed è consigliabile attivare il servizio protezione DDOS di Azure. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Distribuzione esterna-gestione API per la soluzione VMware di Azure":::


## <a name="internal-deployment"></a>Distribuzione interna

Una distribuzione interna pubblica le API utilizzate da utenti o sistemi interni. Gli sviluppatori di team e API di DevOps utilizzano gli stessi strumenti di gestione e il portale per sviluppatori della distribuzione esterna.

Le distribuzioni interne possono essere eseguite [con applicazione Azure gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) per creare un endpoint pubblico e sicuro per l'API.  Le funzionalità del gateway vengono usate per creare una distribuzione ibrida che consente diversi scenari.  

* Usare la stessa risorsa di gestione API per l'utilizzo da parte di consumer interni ed esterni.

* Disporre di una singola risorsa di gestione API con un subset di API definite e disponibili per gli utenti esterni.

* Fornire un modo semplice per passare all'accesso a gestione API dalla rete Internet pubblica.

Il diagramma di distribuzione seguente mostra i consumer che possono essere interni o esterni, a ogni tipo che accede alle stesse API o a API diverse.

In una distribuzione interna le API vengono esposte alla stessa istanza di gestione API. Davanti a gestione API, il gateway applicazione viene distribuito con la funzionalità Web Application Firewall (WAF) di Azure attivata. Distribuito anche un set di listener HTTP e regole per filtrare il traffico, esponendo solo un subset dei servizi back-end in esecuzione nella soluzione VMware di Azure.


* Il traffico interno passa attraverso il gateway ExpressRoute al firewall di Azure e quindi a gestione API, direttamente o attraverso le regole del traffico.   

* Il traffico esterno entra in Azure attraverso il gateway applicazione, che usa il livello di protezione esterno per gestione API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Distribuzione esterna-gestione API per la soluzione VMware di Azure" lightbox="media/api-management/internal-deployment.png":::