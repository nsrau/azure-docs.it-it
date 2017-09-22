---
title: Monitoraggio di Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come monitorare Funzioni di Azure.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 278fd4a811afac240b6ad3c2eb7f0a23cb686382
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="monitoring-azure-functions"></a>Monitoraggio di Funzioni di Azure

## <a name="overview"></a>Overview 


La scheda **Monitoraggio** per ogni funzione consente di esaminare ogni esecuzione di una funzione.

![Scheda Monitoraggio di Funzioni di Azure](./media/functions-monitoring/monitor-tab.png) 

La selezione di un'esecuzione consente di verificare la durata, i dati di input, gli errori e i file di log associati. Ciò risulta utile per il debug e per l'ottimizzazione delle prestazioni delle funzioni.

> [!IMPORTANT]
> Quando si usa il [piano di hosting a consumo](functions-overview.md#pricing) per Funzioni di Azure, il riquadro **Monitoraggio** nell'app per le funzioni non mostra alcun dato. Ciò avviene perché la piattaforma ridimensiona e gestisce automaticamente le istanze di calcolo. Queste metriche non sono quindi significative per un piano a consumo. Per monitorare l'utilizzo delle app per le funzioni, è necessario usare invece le indicazioni disponibili in questo articolo.
> 
> Lo screenshot seguente mostra un esempio:
> 
> ![Monitoraggio della funzione](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>Monitoraggio in tempo reale

Il monitoraggio in tempo reale è disponibile selezionando il **flusso eventi live**, come illustrato di seguito. 

![Opzione flusso eventi live per la scheda Monitoraggio](./media/functions-monitoring/monitor-tab-live-event-stream.png)

Il flusso di eventi live viene visualizzato in un grafico in una nuova scheda nel browser, come nell'esempio seguente: 

![Esempio di flusso eventi live](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> A causa di un problema noto, è possibile che il popolamento dei dati abbia esito negativo. Se si verifica questo problema, potrebbe essere necessario chiudere la scheda del browser che include il flusso eventi live e quindi fare di nuovo clic su **flusso eventi live** per consentire il popolamento corretto dei dati del flusso di eventi. 

Il flusso eventi live produrrà un grafico delle statistiche seguenti per la funzione:

* Esecuzioni avviate al secondo
* Esecuzioni completate al secondo
* Esecuzioni non riuscite al secondo
* Tempo di esecuzione medio in millisecondi

Queste statistiche sono in tempo reale, ma la creazione effettiva dei grafici relativi ai dati dell'esecuzione potrebbe comportare circa 10 secondi di latenza.


## <a name="monitoring-log-files-from-a-command-line"></a>Monitoraggio dei file di log da una riga di comando

È possibile trasmettere in streaming i file di log a una sessione della riga di comando su una workstation locale usando l'interfaccia della riga di comando di Azure 1.0 o PowerShell.

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>Monitoraggio dei file di log dell'app per le funzioni con l'interfaccia della riga di comando di Azure 1.0

Per iniziare, [installare l'interfaccia della riga di comando di Azure 1.0](../cli-install-nodejs.md)

Accedere all'account di Azure usando il comando seguente o una delle altre opzioni illustrate in [Accedere ad Azure dall'interfaccia della riga di comando di Azure 1.0](../xplat-cli-connect.md).

    azure login

Usare il comando seguente per abilitare l'interfaccia della riga di comando di Azure 1.0 nella modalità Gestione dei servizi classica:

    azure config mode asm

Se sono presenti più sottoscrizioni, usare i comandi seguenti per elencare le sottoscrizioni e impostare la sottoscrizione corrente sulla sottoscrizione che include l'app per le funzioni.

    azure account list
    azure account set <subscriptionNameOrId>

Il comando seguente trasmette in streaming i file di log dell'app per le funzioni alla riga di comando:

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>Monitoraggio dei file di log delle app per le funzioni tramite PowerShell

Per iniziare, [installare e configurare Azure PowerShell](/powershell/azure/overview).

Aggiungere il proprio account di Azure usando il comando seguente:

    PS C:\> Add-AzureAccount

Se sono presenti più sottoscrizioni, è possibile elencarle per nome con il comando seguente, in modo da verificare che sia attualmente selezionata la sottoscrizione corretta in base alla proprietà `IsCurrent`:

    PS C:\> Get-AzureSubscription

Se è necessario impostare la sottoscrizione attiva sulla sottoscrizione che include l'app per le funzioni, usare il comando seguente:

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

Trasmettere i log alla sessione di PowerShell con il comando seguente:

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

Per altre informazioni, vedere [Procedura: Eseguire lo streaming dei log per le app Web](../app-service/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Test di Funzioni di Azure](functions-test-a-function.md)
* [Scalabilità di Funzioni di Azure](functions-scale.md)


