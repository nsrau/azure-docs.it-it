---
title: Panoramica del gateway di gestione API di Azure self-hosted - Documenti Microsoft
description: Informazioni su come il gateway di Gestione API di Azure self-hosted consente alle organizzazioni di gestire le API negli ambienti ibridi e multicloud.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513718"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Panoramica del gateway di gestione API self-hosted

Questo articolo spiega in che modo la funzionalità gateway self-hosted consente la gestione delle API ibride e multi-cloud, presenta l'architettura di alto livello e ne evidenzia le funzionalità fondamentali.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello sviluppatore è limitato a una singola distribuzione di gateway self-hosted.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestione api ibride e multi-cloud

La funzionalità gateway self-hosted espande il supporto di Gestione API per ambienti ibridi e multi-cloud e consente alle organizzazioni di gestire in modo efficiente e sicuro le API ospitate in locale e tra i cloud da un singolo servizio Di gestione API in Azure.

Con il gateway self-hosted, i clienti hanno la flessibilità di distribuire una versione in contenitori del componente gateway di Gestione API negli stessi ambienti in cui ospitano le API. Tutti i gateway self-hosted vengono gestiti dal servizio Gestione API con cui sono federati, fornendo così ai clienti la visibilità e l'esperienza di gestione unificata in tutte le API interne ed esterne. L'avvicinamento dei gateway vicino alle API consente ai clienti di ottimizzare i flussi di traffico delle API e di soddisfare i requisiti di sicurezza e conformità.

Ogni servizio di Gestione API è composto dai seguenti componenti chiave:

-   Piano di gestione, esposto come UN'API, utilizzato per configurare il servizio tramite il portale di Azure, PowerShell e altri meccanismi supportati.
-   Gateway (o piano dati) è responsabile dell'inoltro delle richieste API, dell'applicazione di criteri e della raccolta dei dati di telemetria
-   Portale per sviluppatori utilizzato dagli sviluppatori per individuare, apprendere e eseguire l'onboarding per usare le API

Per impostazione predefinita, tutti questi componenti vengono distribuiti in Azure, causando il flusso di tutto il traffico API (mostrato come frecce nere a tinta unita nell'immagine seguente) nell'area seguente, indipendentemente da dove sono ospitati i back-end che implementano le API. La semplicità operativa di questo modello comporta un aumento della latenza, dei problemi di conformità e, in alcuni casi, dei costi aggiuntivi per il trasferimento dei dati.

![Flusso di traffico API senza gateway self-hosted](media/self-hosted-gateway-overview/without-gateways.png)

La distribuzione di gateway self-hosted negli stessi ambienti delle implementazioni delle API back-end e la loro aggiunta al servizio Gestione API consente al traffico API di fluire direttamente alle API back-end, aumentando la latenza, ottimizzando i costi di trasferimento dei dati e mantenendo i vantaggi di avere un unico punto di gestione e individuazione di tutte le API all'interno dell'organizzazione, indipendentemente dalla posizione in cui sono ospitate le implementazioni.

![Flusso di traffico API con gateway self-hosted](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Imballaggio e caratteristiche

Il gateway self-hosted è una versione in contenitori e equivalente dal punto di lavoro del gateway gestito distribuita in Azure come parte di ogni servizio di gestione API. The self-hosted gateway is available as a Linux-based Docker container from the Microsoft Container Registry. Può essere distribuito in Docker, Kubernetes o qualsiasi altra soluzione di orchestrazione contenitore in esecuzione su un'infrastruttura desktop, server o cloud.

> [!IMPORTANT]
> Alcune funzionalità disponibili nel gateway gestito non sono ancora disponibili nell'anteprima. In particolare: criteri Log to Event Hub, Service Fabric integration, downstream HTTP/2. Non è prevista la disponibilità di una cache incorporata nel gateway self-hosted.

## <a name="connectivity-to-azure"></a>Connettività ad Azure

Il gateway self-hosted richiede la connettività TCP/IP in uscita ad Azure sulla porta 443.The self-hosted gateway requires outbound TCP/IP connectivity to Azure on port 443. Ogni gateway self-hosted deve essere associato a un singolo servizio Gestione API e viene configurato tramite il relativo piano di gestione. Il gateway self-hosted usa la connettività ad Azure per:Self-hosted gateway uses connectivity to Azure for:

-   Segnalazione dello stato inviando messaggi heartbeat ogni minuto
-   Controllare regolarmente la presenza di (ogni 10 secondi) e applicare gli aggiornamenti di configurazione ogni volta che sono disponibili
-   Invio di log e metriche delle richieste a Monitoraggio di Azure, se configurato per eseguire questa operazioneSending request logs and metrics to Azure Monitor, if configured to do so
-   Invio di eventi ad Application Insights, se impostato per eseguire questa operazioneSending events to Application Insights, if set to do so

Quando la connettività ad Azure viene persa, il gateway self-hosted non sarà in grado di ricevere aggiornamenti della configurazione, segnalarne lo stato o caricare dati di telemetria.

The self-hosted gateway is designed to "fail static" and can survive the temporary loss of connectivity to Azure. Può essere distribuito con o senza il backup della configurazione locale attivato. Nel primo caso, i gateway self-hosted salveranno regolarmente una copia di backup della configurazione su un volume persistente collegato al contenitore o al pod.

Quando il backup della configurazione è disattivato e la connettività ad Azure viene interrotta:When configuration backup is off and connectivity to Azure is interrupted:

-   I gateway self-hosted in esecuzione continueranno a funzionare utilizzando una copia in memoria della configurazione
-   I gateway self-hosted arrestati non saranno in grado di avviare

Quando il backup della configurazione è attivato e la connettività ad Azure viene interrotta:When configuration backup is turned on and connectivity to Azure is interrupted:

-   I gateway self-hosted in esecuzione continueranno a funzionare utilizzando una copia in memoria della configurazione
-   I gateway self-hosted arrestati inizieranno a utilizzare una copia di backup della configurazione

Quando viene ripristinata la connettività, ogni gateway indipendente interessato dall'interruzione si riconnetterà automaticamente al servizio Gestione API associato e scaricherà tutti gli aggiornamenti della configurazione che si sono verificati mentre il gateway era "offline".

## <a name="next-steps"></a>Passaggi successivi

-   [Leggi un white paper per ulteriori informazioni di base su questo argomento](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuire il gateway self-hosted in DockerDeploy self-hosted gateway to Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Distribuire gateway self-hosted a Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
