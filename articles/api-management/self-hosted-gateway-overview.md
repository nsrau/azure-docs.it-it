---
title: Panoramica del gateway di gestione API di Azure self-hosted | Microsoft Docs
description: Informazioni sul modo in cui il gateway di gestione API di Azure self-hosted consente alle organizzazioni di gestire le API negli ambienti ibridi e multicloud.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513718"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Panoramica del gateway di gestione API self-hosted

Questo articolo illustra il modo in cui la funzionalità gateway self-hosted consente la gestione di API ibride e multicloud, presenta l'architettura di alto livello e ne evidenzia le funzionalità fondamentali.

> [!NOTE]
> La funzionalità gateway self-hosted è in anteprima. Durante l'anteprima, il gateway self-hosted è disponibile solo nei livelli Developer e Premium senza costi aggiuntivi. Il livello Developer è limitato a una singola distribuzione del gateway self-hosted.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestione API ibrida e multicloud

La funzionalità gateway self-hosted espande il supporto di gestione API per gli ambienti ibridi e multicloud e consente alle organizzazioni di gestire in modo efficiente e sicuro le API ospitate in locale e tra cloud da un singolo servizio gestione API in Azure.

Con il gateway self-hosted i clienti hanno la possibilità di distribuire una versione in contenitori del componente gateway di gestione API negli stessi ambienti in cui ospitano le API. Tutti i gateway indipendenti sono gestiti dal servizio gestione API con cui sono federati, offrendo così ai clienti la visibilità e l'esperienza di gestione unificata in tutte le API interne ed esterne. L'inserimento dei gateway vicino alle API consente ai clienti di ottimizzare i flussi di traffico API e risolvere i requisiti di sicurezza e conformità.

Ogni servizio gestione API è costituito dai componenti principali seguenti:

-   Piano di gestione, esposto come API, usato per configurare il servizio tramite il portale di Azure, PowerShell e altri meccanismi supportati.
-   Il gateway o il piano dati è responsabile per l'inoltro delle richieste API, l'applicazione di criteri e la raccolta di dati di telemetria
-   Portale per sviluppatori usato dagli sviluppatori per individuare, apprendere e caricare per usare le API

Per impostazione predefinita, tutti questi componenti vengono distribuiti in Azure, causando il passaggio di tutto il traffico API (visualizzato come frecce nere solide nell'immagine seguente) in Azure indipendentemente da dove sono ospitati i backend che implementano le API. La semplicità operativa di questo modello comporta un aumento della latenza, dei problemi di conformità e, in alcuni casi, di costi aggiuntivi per il trasferimento dei dati.

![Flusso del traffico API senza gateway indipendenti](media/self-hosted-gateway-overview/without-gateways.png)

La distribuzione di gateway indipendenti negli stessi ambienti delle implementazioni dell'API back-end e l'aggiunta al servizio gestione API consente il flusso del traffico API direttamente alle API back-end, migliorando la latenza, ottimizzando i costi di trasferimento dei dati e abilitando conformità mantenendo i vantaggi derivanti dall'uso di un singolo punto di gestione e individuazione di tutte le API all'interno dell'organizzazione, indipendentemente dalla posizione in cui sono ospitate le loro implementazioni.

![Flusso del traffico API con gateway indipendenti](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Creazione di pacchetti e funzionalità

Il gateway self-hosted è una versione in contenitori, equivalente dal punto di vista funzionale, del gateway gestito distribuito in Azure come parte di ogni servizio gestione API. Il gateway self-hosted è disponibile come contenitore Docker basato su Linux da Microsoft Container Registry. Può essere distribuito in Docker, Kubernetes o qualsiasi altra soluzione di orchestrazione dei contenitori in esecuzione in un desktop, in un cluster di server o in un'infrastruttura cloud.

> [!IMPORTANT]
> Alcune funzionalità disponibili nel gateway gestito non sono ancora disponibili in anteprima. In particolare: accedere ai criteri dell'hub eventi, all'integrazione Service Fabric, a valle HTTP/2. Non è previsto alcun piano per rendere disponibile una cache predefinita nel gateway self-hosted.

## <a name="connectivity-to-azure"></a>Connettività ad Azure

Il gateway self-hosted richiede la connettività TCP/IP in uscita ad Azure sulla porta 443. Ogni gateway self-hosted deve essere associato a un singolo servizio gestione API e viene configurato tramite il relativo piano di gestione. Il gateway self-hosted usa la connettività ad Azure per:

-   Segnalazione dello stato inviando messaggi heartbeat ogni minuto
-   Verifica regolarmente (ogni 10 secondi) e applicazione degli aggiornamenti della configurazione ogni volta che sono disponibili
-   Invio di metriche e log delle richieste a monitoraggio di Azure, se configurato per farlo
-   Invio di eventi a Application Insights, se impostato

Quando la connettività ad Azure viene persa, il gateway self-hosted non sarà in grado di ricevere gli aggiornamenti della configurazione, di segnalarne lo stato o di caricare i dati di telemetria.

Il gateway self-hosted è progettato per "generare un errore statico" e può sopravvivere alla perdita temporanea di connettività ad Azure. Può essere distribuito con o senza il backup della configurazione locale attivato. Nel primo caso, i gateway self-hosted salveranno regolarmente una copia di backup della configurazione in un volume permanente collegato al contenitore o pod.

Quando il backup della configurazione è disattivato e viene interrotta la connettività ad Azure:

-   I gateway self-hosted che eseguono continueranno a funzionare usando una copia in memoria della configurazione
-   I gateway indipendenti arrestati non potranno essere avviati

Quando viene attivato il backup della configurazione e viene interrotta la connettività ad Azure:

-   I gateway self-hosted che eseguono continueranno a funzionare usando una copia in memoria della configurazione
-   I gateway indipendenti arrestati inizieranno a usare una copia di backup della configurazione

Quando viene ripristinata la connettività, ogni gateway indipendente interessato dall'interruzione si riconnetterà automaticamente al servizio gestione API associato e scaricherà tutti gli aggiornamenti della configurazione che si sono verificati mentre il gateway era "offline".

## <a name="next-steps"></a>Passaggi successivi

-   [Leggi un white paper per informazioni aggiuntive su questo argomento](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuire il gateway self-hosted in Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Distribuire il gateway self-hosted in Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
