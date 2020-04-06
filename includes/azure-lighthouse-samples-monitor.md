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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421152"
---
Questi esempi illustrano come usare Monitoraggio di Azure per creare avvisi per le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate di Azure.

| **Modello** | **Descrizione** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Esegue una query sull'ultimo giorno di attività in un tenant di gestione e [segnala eventuali deleghe aggiunte o rimosse](../articles/lighthouse/how-to/monitor-delegation-changes.md) (o tentativi non riusciti).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Questo modello crea un avviso di Azure e si connette a un gruppo di azioni esistente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Crea più avvisi dei log basati su query Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Distribuisce un avviso in un tenant quando un utente delega una sottoscrizione a un tenant per la gestione.|
