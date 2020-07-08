---
title: Versioni dell'API
titleSuffix: Azure Cognitive Search
description: Criteri di versione per le API REST di Azure ricerca cognitiva e la libreria client in .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830095"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versioni API in Azure ricerca cognitiva

Azure ricerca cognitiva esegue regolarmente il rollup degli aggiornamenti delle funzionalità. Talvolta, ma non sempre, questi aggiornamenti richiedono una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, il team di ricerca cognitiva di Azure pubblica le nuove versioni solo quando necessario, poiché può comportare un certo impegno nell'aggiornamento del codice per l'uso di una nuova versione dell'API. Una nuova versione è necessaria solo se alcuni aspetti dell'API sono stati modificati in modo da interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

La stessa regola vale per gli aggiornamenti all'SDK. Azure ricerca cognitiva SDK segue le regole di [controllo delle versioni semantiche](https://semver.org/) , il che significa che la versione include tre parti: principale, minore e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK viene rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per gli aggiornamenti di funzionalità che non comportano interruzioni di compatibilità viene incrementata la versione secondaria, mentre in caso di correzione di bug viene incrementata solo la versione build.

> [!NOTE]
> Un'istanza del servizio ricerca cognitiva di Azure supporta diverse versioni dell'API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

## <a name="rest-apis"></a>API REST

Questa tabella fornisce la cronologia delle versioni correnti e rilasciate in precedenza dell'API REST di servizio di ricerca. La documentazione è pubblicata per la versione stabile e di anteprima corrente.

| Versione&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Stato | Problema di compatibilità con le versioni precedenti |
|-------------|--------|------------------------------|
| [Gestione 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Disponibile a livello generale | Versione stabile più recente delle API REST di gestione, con miglioramenti in Endpoint Protection. Aggiunge l'endpoint privato, il supporto dei collegamenti privati e le regole di rete per i nuovi servizi. |
| [Gestione 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Anteprima  | Nonostante il numero di versione, si tratta ancora della versione di anteprima corrente delle API REST di gestione. Al momento non sono disponibili funzionalità di anteprima. Tutte le funzionalità in anteprima sono state recentemente passate alla disponibilità generale. |
| Gestione 2015-08-19  | Stable| Prima versione disponibile a livello generale delle API REST di gestione. Fornisce il provisioning del servizio, la scalabilità verticale e la gestione delle chiavi API. |
| Gestione 2015-08-19-anteprima | Anteprima| Prima versione di anteprima delle API REST di gestione. |
| [Ricerca 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | Versione stabile più recente delle API REST di ricerca di ricerca, con miglioramenti in merito al Punteggio di pertinenza. |
| [Ricerca 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Anteprima | Versione di anteprima associata alla versione stabile. |
| Ricerca 2019-05-06 | Stable | Aggiunge tipi complessi. |
| Ricerca 2019-05-06-Preview | Anteprima | Versione di anteprima associata alla versione stabile. |
| Cerca 2017-11-11 | Stable  | Aggiunge skillsets e l'arricchimento di intelligenza artificiale. |
| Cerca 2017-11-11-Anteprima | Anteprima | Versione di anteprima associata alla versione stabile. |
| Cerca 2016-09-01 |Stable | Aggiunge gli indicizzatori|
| Cerca 2016-09-01-anteprima | Anteprima | Versione di anteprima associata alla versione stabile.|
| Cerca 2015-02-28 | Stable  | Prima versione disponibile a livello generale.  |
| Cerca 2015-02-28-anteprima | Anteprima | Versione di anteprima associata alla versione stabile. |
| Cerca 2014-10-20-anteprima | Anteprima | Seconda anteprima pubblica. |
| Cerca 2014-07-31-anteprima | Anteprima | Prima anteprima pubblica. |

## <a name="azure-sdk-for-net"></a>Azure SDK per .NET

La cronologia delle versioni del pacchetto è disponibile in NuGet.org. In questa tabella vengono forniti i collegamenti a ogni pagina del pacchetto.

| Versione dell'SDK | Stato | Descrizione |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-Preview. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Anteprima | Nuova libreria client da Azure .NET SDK, rilasciata il 2020 maggio. Ha come destinazione la versione dell'API REST 2020-06-30|
| [**Microsoft. Azure. search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Disponibile a livello generale, rilasciato il 2019 maggio. Ha come destinazione la versione dell'API REST 2019-05-06.|
| [**Microsoft. Azure. search 8,0-Anteprima**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Anteprima, rilasciata il 2019 aprile. Ha come destinazione la versione dell'API REST 2019-05-06-Preview.|
| [**Microsoft. Azure. Management. Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Targets The Management REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-java"></a>Azure SDK per Java

| Versione dell'SDK | Stato | Descrizione  |
|-------------|--------|------------------------------|
| [**1.35.0 SearchManagementClient Java**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Targets The Management REST API-Version = 2015-08-19.|

## <a name="azure-sdk-for-python"></a>Azure SDK per Python

| Versione dell'SDK | Stato | Descrizione  |
|-------------|--------|------------------------------|
| [**Python Azure-Mgmt-ricerca 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | Targets The Management REST API-Version = 2015-08-19. |