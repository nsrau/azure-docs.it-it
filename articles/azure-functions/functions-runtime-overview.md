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
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
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

* L'hosting del portale di gestione di Funzioni di Azure, corrispondente a quello nel [portale di Azure](https://portal.azure.com). Il portale consente di sviluppare funzioni come si farebbe nel portale di Azure.
* La distribuzione di funzioni tra più ruoli di lavoro di Funzioni.
* La garanzia di un endpoint di pubblicazione in modo che sia possibile pubblicare direttamente le funzioni da Microsoft Visual Studio tramite il download e l'importazione del profilo di pubblicazione.

## <a name="azure-functions-worker-role"></a>Ruolo di lavoro di Funzioni di Azure

I ruoli di lavoro di Funzioni di Azure vengono distribuiti nei contenitori Windows e qui viene eseguito il codice della funzione.  È possibile distribuire più ruoli di lavoro in tutta l'organizzazione e i clienti possono quindi sfruttare potenza di calcolo di riserva.  Un esempio di capacità di calcolo di riserva in molte organizzazioni è rappresentato da computer che sono costantemente accesi ma che rimangono inutilizzati per lunghi periodi di tempo.

## <a name="minimum-requirements"></a>Requisiti minimi

Per iniziare a usare il runtime di Funzioni di Azure è necessario disporre di un computer con Windows Server 2016 o Windows 10 Creators Update con accesso a un'istanza di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

Installare l'[anteprima del runtime di Funzioni di Azure](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
