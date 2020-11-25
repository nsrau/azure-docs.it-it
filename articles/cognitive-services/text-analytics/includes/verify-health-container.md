---
title: Verificare l'istanza del contenitore di integrità
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore di integrità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009878"
---
### <a name="verify-that-a-container-is-running"></a>Verificare che sia in esecuzione un contenitore

1. Selezionare la scheda **Panoramica** e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e immettere l'indirizzo IP. Ad esempio, immettere `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Viene visualizzato il home page del contenitore, che consente di verificare che il contenitore sia in esecuzione.

    ![Visualizzare il contenitore home page per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selezionare il collegamento **Descrizione API servizio** per passare alla pagina di spavalderia del contenitore.

1. Scegliere una delle API **post** , quindi fare clic **su prova**. Vengono visualizzati i parametri, che includono l'input di esempio.

Sono disponibili diversi URL che è possibile usare anche per verificare che il contenitore sia in esecuzione.

|Richiesta|Scopo|
|--|--|
|`http://localhost:5000/`|Il contenitore fornisce un home page.|
|`http://localhost:5000/ready`|Richiesto con GET, in questo modo viene verificata la disponibilità del contenitore per l'accettazione di una query sul modello. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes.|
|`http://localhost:5000/status`|Richiesto con GET, ad esempio l'endpoint/Ready, in questo modo viene convalidato l'esecuzione del contenitore senza incorrere in una query sul modello. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes.|
|`http://localhost:5000/swagger`|Tramite questo URL, il contenitore fornisce un set completo di documentazione per gli endpoint e una `Try it now` funzionalità. Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |
|`http://localhost:5000/demo`| Richiesta tramite un browser, questa funzionalità fornisce una visualizzazione interattiva dei risultati delle query degli esempi di testo di input o di uno fornito dall'utente.  |

Usare questo URL della richiesta: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` per inviare una query al contenitore.
