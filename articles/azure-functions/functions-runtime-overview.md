---
title: Panoramica del runtime di Funzioni di Azure | Documentazione Microsoft
description: Panoramica di anteprima del runtime di Funzioni di Azure
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d9e655b6ebbdb02bae21fa7a8fa7ee5b46d6e63b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="azure-functions-runtime-overview"></a>Panoramica di Runtime di Funzioni di Azure

Il runtime di Funzioni di Azure offre un nuovo modo per sfruttare la semplicità e la flessibilità del modello di programmazione di Funzioni di Azure in locale. Con la stessa base open source di Funzioni di Azure, il runtime di Funzioni di Azure viene distribuito in locale per fornire un'esperienza di sviluppo quasi identica al servizio cloud.

![Anteprima del runtime di Funzioni di Azure - Portale][1]

Il runtime di Funzioni di Azure offre un modo per sfruttare Funzioni di Azure prima di eseguire il commit nel cloud. In questo modo, è possibile portare gli asset di codice compilati nel cloud quando si esegue la migrazione.  Il runtime offre anche nuove opzioni, ad esempio l'uso di potenza di calcolo di riserva dei computer locali per l'esecuzione di processi batch durante la notte. È possibile anche usare i dispositivi all'interno dell'organizzazione per inviare dati in modo condizionale ad altri sistemi, sia in locale sia nel cloud.

Il runtime di Funzioni di Azure è composto da due parti:
* Il ruolo di gestione del runtime di Funzioni di Azure
* Il ruolo di lavoro del runtime di Funzioni di Azure

## <a name="azure-functions-management-role"></a>Il ruolo di gestione di Funzioni di Azure

Il ruolo di gestione di Funzioni di Azure fornisce un host per la gestione di Funzioni in locale. Questo ruolo esegue le attività seguenti:

* L'hosting del portale di gestione di Funzioni di Azure, corrispondente a quello nel [portale di Azure](https://portal.azure.com). Ciò consente di sviluppare funzioni come si farebbe nel portale di Azure.
* La distribuzione di funzioni tra più ruoli di lavoro di Funzioni.
* La garanzia di un endpoint di pubblicazione in modo che sia possibile pubblicare direttamente le funzioni da Microsoft Visual Studio.

## <a name="azure-functions-worker-role"></a>Ruolo di lavoro di Funzioni di Azure

I ruoli di lavoro di Funzioni di Azure vengono distribuiti nei contenitori Windows e qui viene eseguito il codice della funzione.  È possibile distribuire più ruoli di lavoro in tutta l'organizzazione e i clienti possono quindi sfruttare potenza di calcolo di riserva.

## <a name="minimum-requirements"></a>Requisiti minimi

Per iniziare a usare il runtime di Funzioni di Azure è necessario disporre di un computer con **Windows Server 2016 o Windows 10 Creators Update** con accesso a un'istanza di **SQL Server**.

## <a name="next-steps"></a>Passaggi successivi

Installare l'[anteprima del runtime di Funzioni di Azure](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png

