---
title: Concetti-gestione API
description: Informazioni su come gestione API protegge le API in esecuzione in macchine virtuali (VM) della soluzione VMware di Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 346d0f795c3d19b115ced771991263cce2104217
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262978"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>Gestione API per pubblicare e proteggere le API in esecuzione in macchine virtuali basate su soluzioni VMware di Azure

Microsoft Azure [gestione API](https://azure.microsoft.com/services/api-management/) consente agli sviluppatori e ai team DevOps di pubblicare in modo sicuro gli utenti interni o esterni.

Sebbene sia disponibile in diversi SKU, solo gli SKU Developer e Premium consentono l'integrazione della rete virtuale di Azure per pubblicare le API in esecuzione nei carichi di lavoro della soluzione VMware di Azure. Questi due SKU abilitano in modo sicuro la connettività tra il servizio gestione API e il back-end. Lo SKU Developer è progettato per lo sviluppo e il test, mentre lo SKU Premium è per le distribuzioni di produzione.

Per i servizi back-end eseguiti in macchine virtuali (VM) della soluzione VMware di Azure, la configurazione in gestione API, per impostazione predefinita, è identica a quella dei servizi back-end locali. Per le distribuzioni sia interne che esterne, gestione API configura l'IP virtuale (VIP) del servizio di bilanciamento del carico come endpoint back-end quando il server back-end si trova dietro una NSX Load Balancer sul lato della soluzione VMware di Azure.

## <a name="external-deployment"></a>Distribuzione esterna

Una distribuzione esterna pubblica le API utilizzate dagli utenti esterni tramite un endpoint pubblico. Gli sviluppatori e i tecnici DevOps possono gestire le API tramite portale di Azure o PowerShell e il portale per sviluppatori di gestione API.

Il diagramma di distribuzione esterna Mostra l'intero processo e gli attori coinvolti (visualizzati nella parte superiore). Gli attori sono:

- **Amministratore/i:** Rappresenta il team di amministrazione o DevOps, che gestisce la soluzione VMware di Azure tramite i meccanismi di automazione e portale di Azure come PowerShell o Azure DevOps.

- **Utenti:**  Rappresenta i consumer delle API esposte e rappresenta sia gli utenti che i servizi che utilizzano le API.

Il flusso del traffico passa attraverso l'istanza di gestione API, che astrae i servizi back-end, collegati alla rete virtuale dell'hub. Il gateway ExpressRoute instrada il traffico verso il canale Copertura globale di ExpressRoute e raggiunge una NSX Load Balancer la distribuzione del traffico in ingresso nelle diverse istanze di servizi back-end.

Gestione API ha un'API pubblica di Azure e l'attivazione del servizio protezione DDOS di Azure è consigliata. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Distribuzione esterna-gestione API per la soluzione VMware di Azure":::


## <a name="internal-deployment"></a>Distribuzione interna

Una distribuzione interna pubblica le API utilizzate da utenti o sistemi interni. Gli sviluppatori di team e API di DevOps utilizzano gli stessi strumenti di gestione e il portale per sviluppatori della distribuzione esterna.

Le distribuzioni interne possono essere [con applicazione Azure gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) per creare un endpoint pubblico e sicuro per l'API sfruttando le funzionalità e creando una distribuzione ibrida che consente diversi scenari.  L'API sfrutta le funzionalità e crea una distribuzione ibrida che consente diversi scenari.

* Usare la stessa risorsa di gestione API per l'utilizzo da parte di consumer interni ed esterni.

* Disporre di una singola risorsa di gestione API con un subset di API definite e disponibili per gli utenti esterni.

* Fornire un modo semplice per passare all'accesso a gestione API dalla rete Internet pubblica.

Il diagramma di distribuzione seguente mostra i consumer che possono essere interni o esterni, a ogni tipo che accede alle stesse API o a API diverse.

In una distribuzione interna le API vengono esposte alla stessa istanza di gestione API. Davanti a gestione API, il gateway applicazione viene distribuito con la funzionalità Web Application Firewall (WAF) di Azure attivata e un set di listener HTTP e regole per filtrare il traffico, esponendo solo un subset dei servizi back-end in esecuzione nella soluzione VMware di Azure.

* Il traffico interno viene indirizzato attraverso il gateway ExpressRoute al firewall di Azure e quindi a gestione API se le regole di traffico vengono stabilite o direttamente a gestione API.  

* Il traffico esterno entra in Azure attraverso il gateway applicazione, che usa il livello di protezione esterno per gestione API.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Distribuzione interna-gestione API per la soluzione VMware di Azure":::