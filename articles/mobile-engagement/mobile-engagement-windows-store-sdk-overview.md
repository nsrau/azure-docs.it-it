---
title: Azure Mobile Engagement - Integrazione di Windows Universal SDK | Documentazione Microsoft
description: Integrazione di Windows Universal per SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integrazione di Windows Universal SDK per Azure Mobile Engagement
Questo documento descrive tutte le opzioni di configurazione e integrazione disponibili per Windows Universal SDK per Azure Mobile Engagement.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario completare l' [esercitazione di 15 minuti](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Funzionalità avanzate
### <a name="reporting-features"></a>Funzionalità di segnalazione
È possibile aggiungere queste funzionalità:

1. [Opzioni di segnalazione avanzata](mobile-engagement-windows-store-advanced-reporting.md)
2. [Opzioni di configurazione avanzate](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notifiche
[Come integrare il servizio di copertura (di notifica) in un'app universale di Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementazione del piano di tag:
[Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Note sulla versione
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Miglioramenti della stabilità.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate diverse versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md)complete. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

### <a name="from-330-to-340"></a>Dalla versione 3.3.0 alla 3.4.0
#### <a name="test-logs"></a>Log di test
I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per personalizzare, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Risorse
La sovrimpressione Reach è stata migliorata. Fa parte delle risorse del pacchetto NuGet di SDK.

Durante l'aggiornamento alla nuova versione di SDK, è possibile scegliere se mantenere i file esistenti contenuti nella cartella della sovrimpressione delle risorse:

* Se la sovrimpressione precedente è in funzione o si stanno integrando manualmente gli elementi `WebView` , è possibile decidere di mantenere i file esistenti per poter proseguire.
* Per passare alla sovrimpressione nuova, sostituire l'intera cartella `overlay` delle risorse con quella nuova disponibile nel pacchetto SDK. Dopo aver completo l'aggiornamento, nelle app UWP è possibile ottenere la cartella della nuova sovrimpressione da %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources.

> [!WARNING]
> Se si usa la sovrimpressione nuova, le personalizzazioni eseguite con la versione precedente saranno sovrascritte.
> 
> 

### <a name="upgrade-from-older-versions"></a>Eseguire l'aggiornamento da versioni precedenti
Vedere [Upgrade Procedures](mobile-engagement-windows-store-upgrade-procedure.md)

