---
title: "Servizio app di Azure: scalabilità delle applicazioni nel servizio app"
description: "Informazioni su vantaggi e svantaggi della scalabilità dell&quot;applicazione nel servizio app."
keywords: servizio app, servizio app di azure, scala, scalabile, piano di servizio app, costo del servizio app
services: app-service
documentationcenter: 
author: btardif
manager: wpickett
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Servizio app di Azure: scalabilità delle applicazioni nel servizio app
Le applicazioni ospitate nel servizio app di Azure possono [raggiungere un livello di scalabilità elevato](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Tuttavia, la scalabilità di un'applicazione è un problema complesso senza una soluzione valida per tutti i casi. Per ridimensionare correttamente l'applicazione occorre considerare 3 aree chiave che contribuiranno al corretto funzionamento delle applicazioni:

1. Comprendere l'architettura dell'applicazione e i relativi punti deboli.
   * Si tratta di un'applicazione con stato o senza stato?
   * Quali sono i componenti dell'applicazione?
     * Dove sono i colli di bottiglia nell'applicazione?
   * Quando si applica il carico all'app, quali elementi vengono interrotti per primi?
2. Comprendere i requisiti di carico e prestazioni previsti.
   * L'applicazione deve servire 1000 utenti o un milione?
   * Il traffico proviene da un'unica area geografica o a livello globale?
   * Sono presenti variazioni stagionali o picchi di traffico?
   * Con quale velocità deve rispondere l'applicazione? Un secondo? Un millisecondo?
3. Comprendere e sfruttare correttamente la piattaforma di hosting dell'app.
   * Quali funzionalità è necessario sfruttare per raggiungere gli obiettivi di scalabilità?

Questa sezione consente di comprendere tutti i fattori e definire una strategia che sfrutti le funzionalità del servizio app necessarie per raggiungere i propri obiettivi di scalabilità.

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]




<!--HONumber=Nov16_HO3-->


