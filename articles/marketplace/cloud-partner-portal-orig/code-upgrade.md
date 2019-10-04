---
title: Aggiorna il codice per la piattaforma più recente | Azure Marketplace
description: Questo argomento illustra come aggiornare la piattaforma Microsoft Dynamics 365 for Operations alla versione più recente
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935291"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Aggiornamento del codice alla piattaforma più recente

Questo articolo illustra come eseguire l'aggiornamento della piattaforma Microsoft Dynamics 365 for Operations alla versione più recente.

## <a name="overview"></a>Panoramica

La piattaforma Microsoft Dynamics 365 for Operations include i componenti seguenti:

I file binari della piattaforma Dynamics 365 for Operations, come Server oggetti applicativi (AOS), Data Management Framework, il framework di reporting e business intelligence (BI), gli strumenti di sviluppo e i servizi di analisi. I pacchetti Struttura ad oggetti applicativi (AOT) seguenti:

1. Piattaforma applicativa
2. Application Foundation
3. Test Essentials

**Importante**: Per spostare il più recente di Dynamics 365 per la piattaforma di operazioni, di Dynamics 365 per l'implementazione di operazioni non può contenere tutte le personalizzazioni (sovrapposizione) di tutti i pacchetti di AOT che appartengono alla piattaforma. Questa restrizione è stata introdotta nell'update 3 della piattaforma per consentire aggiornamenti continui senza problemi della piattaforma. Se si esegue una piattaforma precedente all'update 3, vedere la sezione relativa all'aggiornamento all'update 3 della piattaforma da una build precedente in fondo a questo articolo.

Per altre informazioni sull'aggiornamento del codice, vedere [qui](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).