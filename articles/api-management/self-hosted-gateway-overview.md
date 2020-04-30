---
title: Panoramica del gateway self-hosted | Microsoft Docs
description: Informazioni su come la funzionalità gateway self-hosted di gestione API di Azure consente alle organizzazioni di gestire le API in ambienti ibridi e multicloud.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232973"
---
# <a name="self-hosted-gateway-overview"></a>Panoramica del gateway self-hosted

Questo articolo illustra il modo in cui la funzionalità gateway self-hosted di gestione API di Azure consente la gestione di API ibride e multicloud, presenta l'architettura di alto livello ed evidenzia le sue funzionalità.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestione API ibrida e multicloud

La funzionalità gateway self-hosted espande il supporto di gestione API per gli ambienti ibridi e multicloud e consente alle organizzazioni di gestire in modo efficiente e sicuro le API ospitate in locale e tra cloud da un singolo servizio gestione API in Azure.

Con il gateway self-hosted i clienti hanno la possibilità di distribuire una versione in contenitori del componente gateway di gestione API negli stessi ambienti in cui ospitano le API. Tutti i gateway indipendenti sono gestiti dal servizio gestione API con cui sono federati, offrendo così ai clienti la visibilità e l'esperienza di gestione unificata in tutte le API interne ed esterne. L'inserimento dei gateway vicino alle API consente ai clienti di ottimizzare i flussi di traffico API e risolvere i requisiti di sicurezza e conformità.

Ogni servizio gestione API è costituito dai componenti principali seguenti:

-   Piano di gestione, esposto come API, usato per configurare il servizio tramite il portale di Azure, PowerShell e altri meccanismi supportati.
-   Il gateway o il piano dati è responsabile per l'inoltro delle richieste API, l'applicazione di criteri e la raccolta di dati di telemetria
-   Portale per sviluppatori usato dagli sviluppatori per individuare, apprendere e caricare per usare le API

Per impostazione predefinita, tutti questi componenti vengono distribuiti in Azure, causando il passaggio di tutto il traffico API (visualizzato come frecce nere solide nell'immagine seguente) in Azure indipendentemente da dove sono ospitati i backend che implementano le API. La semplicità operativa di questo modello comporta un aumento della latenza, dei problemi di conformità e, in alcuni casi, di costi aggiuntivi per il trasferimento dei dati.

![Flusso del traffico API senza gateway indipendenti](media/self-hosted-gateway-overview/without-gateways.png)

La distribuzione di gateway indipendenti negli stessi ambienti in cui sono ospitate le implementazioni dell'API back-end consente il flusso del traffico API direttamente alle API back-end, che consente di migliorare la latenza, ottimizzare i costi di trasferimento dei dati e garantire la conformità, mantenendo al tempo stesso i vantaggi derivanti dall'uso di un singolo punto di gestione, osservabilità e individuazione di tutte le API

![Flusso del traffico API con gateway indipendenti](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Creazione di pacchetti e funzionalità

Il gateway self-hosted è una versione in contenitori, equivalente dal punto di vista funzionale, del gateway gestito distribuito in Azure come parte di ogni servizio gestione API. Il gateway self-hosted è disponibile come [contenitore](https://aka.ms/apim/sputnik/dhub) Docker basato su Linux da Microsoft container Registry. Può essere distribuito in Docker, Kubernetes o qualsiasi altra soluzione di orchestrazione dei contenitori in esecuzione in un cluster di server in locale, in un'infrastruttura cloud o a scopo di valutazione e sviluppo, in una personal computer.

Le funzionalità seguenti trovate nei gateway gestiti **non sono disponibili** nei gateway self-hosted:

- Log di Monitoraggio di Azure
- Upstream (lato back-end) versione TLS e Gestione crittografia
- Convalida dei certificati server e client usando i [certificati radice dell'autorità di certificazione](api-management-howto-ca-certificates.md) caricati nel servizio gestione API. Per aggiungere il supporto per la CA personalizzata, aggiungere un livello all'immagine del contenitore del gateway self-hosted che installa il certificato radice della CA.
- Integrazione con il [Service Fabric](../service-fabric/service-fabric-api-management-overview.md)
- Ripresa della sessione TLS
- Rinegoziazione del certificato client. Ciò significa che per l' [autenticazione del certificato client](api-management-howto-mutual-certificates-for-clients.md) ai consumer dell'API di lavoro devono essere presenti i certificati come parte dell'handshake TLS iniziale. Per assicurarsi che, abilitare l'impostazione Negotiate client certificate quando si configura un nome host personalizzato del gateway self-hosted.
- Cache predefinita. Per informazioni sull'uso della cache esterna nei gateway indipendenti, vedere questo [documento](api-management-howto-cache-external.md) .

## <a name="connectivity-to-azure"></a>Connettività ad Azure

I gateway indipendenti richiedono la connettività TCP/IP in uscita in Azure sulla porta 443. Ogni gateway self-hosted deve essere associato a un singolo servizio gestione API e viene configurato tramite il relativo piano di gestione. Il gateway self-hosted usa la connettività ad Azure per:

-   Segnalazione dello stato inviando messaggi heartbeat ogni minuto
-   Verifica regolarmente (ogni 10 secondi) e applicazione degli aggiornamenti della configurazione ogni volta che sono disponibili
-   Invio di metriche e log delle richieste a monitoraggio di Azure, se configurato per farlo
-   Invio di eventi a Application Insights, se impostato

Quando la connettività ad Azure viene persa, il gateway self-hosted non sarà in grado di ricevere gli aggiornamenti della configurazione, di segnalarne lo stato o di caricare i dati di telemetria.

Il gateway self-hosted è progettato per "generare un errore statico" e può sopravvivere a una perdita temporanea di connettività ad Azure. Può essere distribuito con o senza il backup della configurazione locale. Nel primo caso, i gateway indipendenti salvano regolarmente una copia di backup dell'ultima configurazione scaricata in un volume permanente collegato al relativo contenitore o pod.

Quando il backup della configurazione è disattivato e viene interrotta la connettività ad Azure:

-   L'esecuzione di gateway indipendenti continuerà a funzionare usando una copia in memoria della configurazione
-   I gateway indipendenti arrestati non potranno essere avviati

Quando viene attivato il backup della configurazione e viene interrotta la connettività ad Azure:

-   L'esecuzione di gateway indipendenti continuerà a funzionare usando una copia in memoria della configurazione
-   I gateway indipendenti arrestati saranno in grado di iniziare a usare una copia di backup della configurazione

Quando viene ripristinata la connettività, ogni gateway indipendente interessato dall'interruzione si riconnetterà automaticamente al servizio gestione API associato e scaricherà tutti gli aggiornamenti della configurazione che si sono verificati mentre il gateway era "offline".

## <a name="next-steps"></a>Passaggi successivi

-   [Leggi un white paper per informazioni aggiuntive su questo argomento](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Distribuire il gateway self-hosted in Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [Distribuire il gateway self-hosted in Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
