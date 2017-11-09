---
title: Azure Mobile Engagement - Panortamica dell'SDK per Azure Mobile Engagement | Documentazione Microsoft
description: Panoramica di Azure Mobile Engagement SDK per Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Panoramica di Azure Mobile Engagement SDK per Windows Phone Silverlight
Iniziare da qui per ottenere i dettagli su come integrare Azure Mobile Engagement in un'app per Windows Phone Silverlight. Se si vuole fare prima una prova, completare l' [esercitazione di 15 minuti](mobile-engagement-windows-phone-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procedure di integrazione
1. Iniziare da qui: [Come integrare Mobile Engagement in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Per le notifiche: [Come integrare il servizio Reach (di notifica) in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app per Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Note sulla versione
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Parte del pacchetto Nuget *MicrosoftAzure.MobileEngagement* **v3.4.1**

* Miglioramenti della stabilità.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-windows-phone-upgrade-procedure.md)complete. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

### <a name="from-200-to-330"></a>Dalla versione 2.0.0 alla 3.3.0
#### <a name="test-logs"></a>Log di test
I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Eseguire l'aggiornamento da versioni precedenti
Vedere [Upgrade Procedures](mobile-engagement-windows-phone-upgrade-procedure.md)

