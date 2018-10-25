---
title: Aggiornamento del codice alla piattaforma più recente | Microsoft Docs
description: Questo argomento illustra come aggiornare la piattaforma Microsoft Dynamics 365 for Operations alla versione più recente
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808017"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Aggiornamento del codice alla piattaforma più recente

Questo articolo illustra come eseguire l'aggiornamento della piattaforma Microsoft Dynamics 365 for Operations alla versione più recente.

## <a name="overview"></a>Panoramica

La piattaforma Microsoft Dynamics 365 for Operations include i componenti seguenti:

I file binari della piattaforma Dynamics 365 for Operations, come Server oggetti applicativi (AOS), Data Management Framework, il framework di reporting e business intelligence (BI), gli strumenti di sviluppo e i servizi di analisi. I pacchetti Struttura ad oggetti applicativi (AOT) seguenti:

1. Piattaforma applicativa
2. Application Foundation
3. Test Essentials

**Importante**: per eseguire l'aggiornamento alla piattaforma Dynamics 365 for Operations più recente, l'implementazione di Dynamics 365 for Operations non può contenere alcuna personalizzazione (sovrapposizione) di alcun pacchetto AOT appartenente alla piattaforma. Questa restrizione è stata introdotta nell'update 3 della piattaforma per consentire aggiornamenti continui senza problemi della piattaforma. Se si esegue una piattaforma precedente all'update 3, vedere la sezione relativa all'aggiornamento all'update 3 della piattaforma da una build precedente in fondo a questo articolo.

Per altre informazioni sull'aggiornamento del codice, vedere [qui](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).