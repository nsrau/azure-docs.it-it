---
title: Correggere gli errori "502 - Gateway non valido" e "503 - Servizio app di Azure | Microsoft Docs
description: Risoluzione degli errori "502 - Gateway non valido" e "503 - Servizio non disponibile" nelle app ospitate nel Servizio App di Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 502 - Gateway non valido, 503 - Servizio non disponibile, errore 503, errore 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5a4b8b2fd3e232d7b42b2f510075c3964ca50531
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652575"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Risolvere gli errori HTTP "502 - Gateway non valido" e "503 - Servizio non disponibile" nel Servizio app di Azure
Gli errori "502 - Gateway non valido" e "503 - Servizio non disponibile" sono comuni nelle app ospitate nel [Servizio app di Azure](https://go.microsoft.com/fwlink/?LinkId=529714). Questo articolo fornisce informazioni utili per la risoluzione di questi errori.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito di supporto per Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## <a name="symptom"></a>Sintomo
Quando si passa all'app, viene restituito un errore HTTP "502 - Gateway non valido" o HTTP "503 - Servizio non disponibile".

## <a name="cause"></a>Causa
Spesso la causa dell'errore deriva da problemi a livello dell'applicazione, ad esempio:

* le richieste impiegano troppo tempo
* utilizzo elevato di memoria/CPU da parte dell'applicazione
* arresto anomalo dell'applicazione a causa di un'eccezione.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Passaggi per la risoluzione degli errori "503 - Servizio non disponibile" e "502 - Gateway non valido"
La risoluzione dei problemi prevede tre attività distinte, in ordine sequenziale:

1. [Osservare e monitorare il comportamento dell'applicazione](#observe)
2. [Raccogliere i dati](#collect)
3. [Attenuare il problema](#mitigate)

Il [servizio app](overview.md) presenta diverse opzioni per ogni passaggio.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Osservare e monitorare il comportamento dell'applicazione
#### <a name="track-service-health"></a>Tenere traccia dell'integrità del servizio
Microsoft Azure pubblica un annuncio ogni volta che si verifica un'interruzione del servizio o una riduzione delle prestazioni. È possibile verificare l'integrità del servizio nel [portale di Azure](https://portal.azure.com/). Per altre informazioni, vedere [Tenere traccia dell’integrità del servizio](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Monitorare l'app
Questa opzione consente di trovare eventuali problemi nell'applicazione. Nel pannello dell'app fare clic sul riquadro **Richieste ed errori**. Il pannello **Metrica** mostra le metriche che si possono aggiungere.

Le metriche più comunemente monitorate per le app sono

* Working set della memoria medio
* Tempo di risposta medio
* Tempo CPU
* Working set della memoria
* Requests

![Monitorare le app per risolvere gli errori HTTP 502 - Gateway non valido e 503 - Servizio non disponibile](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Per altre informazioni, vedere:

* [Monitorare le app in Servizio app di Azure](web-sites-monitor.md)
* [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Raccogliere i dati
#### <a name="use-the-diagnostics-tool"></a>Usare lo strumento di diagnostica
Il servizio app offre un'esperienza dinamica e interattiva che consente di risolvere i problemi delle app senza ricorrere ad alcun intervento di configurazione. Quando vengono rilevati problemi nell'app, lo strumento di diagnostica ne evidenzia la natura per poter semplificare e velocizzare l'individuazione e la risoluzione del problema.

Per accedere alla diagnostica del servizio app, passare alla app del servizio app o all'ambiente del servizio app nel [portale di Azure](https://portal.azure.com). Nel riquadro di spostamento a sinistra fare clic su **Diagnostica e risoluzione dei problemi**.

#### <a name="use-the-kudu-debug-console"></a>Usare la console di debug Kudu
Il servizio app include una console di debug che è possibile usare per il debug, l'esplorazione e il caricamento di file, nonché endpoint JSON per ottenere informazioni sull'ambiente in uso. Questa console è chiamata *console Kudu* o *dashboard SCM* dell'app.

È possibile accedere a questo dashboard selezionando il collegamento **https://&lt;nome app>.scm.azurewebsites.net/**.

Elementi forniti dalla console Kudu:

* impostazioni di ambiente per l'applicazione
* log in streaming
* dump di diagnostica
* console di debug in cui è possibile eseguire cmdlet di PowerShell e comandi DOS di base.

Inoltre, nel caso in cui l'applicazione generi eccezioni first-chance, è possibile usare Kudu e l'utilità della riga di comando Procdump dello strumento SysInternals per creare dump della memoria. I dump della memoria sono snapshot del processo e semplificano la risoluzione di problemi più complessi riscontrati nell'app.

Per altre informazioni sulle funzionalità disponibili in Kudu, vedere il post di blog relativo agli [strumenti online di Siti Web di Azure che è opportuno conoscere](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Attenuare il problema
#### <a name="scale-the-app"></a>Ridimensionare l'app
Nel servizio app di Azure, per ottimizzare le prestazioni e la velocità effettiva è possibile modificare la scalabilità in cui è in esecuzione l'applicazione. Aumentare le prestazioni di un'app implica due azioni correlate: passare a un piano tariffario superiore e configurare determinate impostazioni una volta adottato il nuovo piano.

Per altre informazioni sul ridimensionamento, vedere [Ridimensionare un'app nel Servizio app di Azure](web-sites-scale.md).

È anche possibile scegliere di eseguire l'applicazione in più di un'istanza. In questo modo, non solo si ottiene una maggiore capacità di elaborazione, ma si può usufruire della tolleranza di errore. Se il processo si arresta in un'istanza, l'altra istanza continuerà a gestire le richieste.

È possibile impostare il ridimensionamento manuale o automatico.

#### <a name="use-autoheal"></a>Usare la funzionalità AutoHeal
La funzionalità AutoHeal consente di riciclare il processo di lavoro per l'app in base alle impostazioni specificate, ad esempio modifiche di configurazione, richieste, limiti basati sulla memoria o il tempo necessario per l'esecuzione di una richiesta. Nella maggior parte dei casi, riciclare il processo costituisce il modo più veloce per risolvere un problema. Anche se è possibile riavviare l'app direttamente dall'interno del portale di Azure, la funzionalità AutoHeal eseguirà questa operazione automaticamente. È sufficiente aggiungere alcuni trigger nel file web.config radice per l'app. Si noti che queste impostazioni funzionano allo stesso modo anche per le applicazioni non .NET.

Per altre informazioni, vedere il post di blog relativo alla [correzione automatica di Siti Web di Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Riavviare l'app
Questo è spesso il modo più semplice per risolvere problemi occasionali. Nel pannello dell'app del [portale di Azure](https://portal.azure.com/) sono disponibili le opzioni per arrestare o riavviare l'app.

 ![riavviare l'app per risolvere gli errori HTTP "502 - Gateway non valido" e "503 - Servizio non disponibile"](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

È anche possibile gestire l'app usando Azure PowerShell. Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).

