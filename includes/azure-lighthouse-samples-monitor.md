---
title: File di inclusione
description: File di inclusione
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80986728"
---
Questi esempi illustrano come usare Monitoraggio di Azure per creare avvisi per le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate di Azure.

| **Modello** | **Descrizione** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Esegue una query sull'ultimo giorno di attività in un tenant di gestione e [segnala eventuali deleghe aggiunte o rimosse](../articles/lighthouse/how-to/monitor-delegation-changes.md) (o tentativi non riusciti).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Questo modello crea un avviso di Azure e si connette a un gruppo di azioni esistente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Crea più avvisi dei log basati su query Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Distribuisce un avviso in un tenant quando un utente delega una sottoscrizione a un tenant per la gestione.|
