---
title: Creare un servizio di Ricerca di Azure nel portale | Documentazione Microsoft
description: Eseguire il provisioning di un servizio Ricerca di Azure nel portale.
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/16/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4d7c0afe5e43e0c119f534cdedaaa03a16ed5303
ms.openlocfilehash: 0d5198c74fa81358dcdfec514e1fe422d17f368f
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-azure-search-service-in-the-portal"></a>Creare un servizio di Ricerca di Azure nel portale

Questo articolo spiega come creare o eseguire il provisioning di un servizio Ricerca di Azure nel portale. Per istruzioni su PowerShell vedere [Gestire il servizio Ricerca di Azure con PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Sottoscrizione gratuita o a pagamento

[Aprire un account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e usare i crediti per provare i servizi di Azure a pagamento. Dopo avere consumato i crediti, mantenere l'account e continuare a usare i servizi di Azure gratuiti, ad esempio Siti Web. Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito.

In alternativa, [attivare i benefici della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento. 

## <a name="find-azure-search"></a>Trovare Ricerca di Azure
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic sul segno più ("+") nell'angolo superiore sinistro.
3. Selezionare **Web e dispositivi mobili** > **Ricerca di Azure**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Assegnare un nome all'endpoint URL e al servizio

Il nome del servizio fa parte dell'endpoint dell'URL in cui vengono eseguite le chiamate API. Digitare il nome del servizio nel campo **URL** . 

Requisiti per i nomi di servizio:
   * lunghezza compresa tra 2 e 60 caratteri
   * uso di lettere minuscole, cifre o trattini ("-")
   * nessun trattino ("-") nei primi 2 caratteri o nell'ultimo carattere
   * nessun trattino consecutivo ("--")

## <a name="select-a-subscription"></a>Selezionare una sottoscrizione
Se sono disponibili più sottoscrizioni, sceglierne una che includa anche i servizi di archiviazione file o dati. Ricerca di Azure può rilevare automaticamente archiviazione BLOB e archiviazione tabelle di Azure, il database SQL e DocumentDB per l'indicizzazione tramite *indicizzatori*, ma solo per i servizi nella stessa sottoscrizione.

## <a name="select-a-resource-group"></a>Selezionare un gruppo di risorse
Un gruppo di risorse è una raccolta di servizi e risorse di Azure usati insieme. Se ad esempio si usa Ricerca di Azure per l'indicizzazione di un database SQL, entrambi i servizi devono far parte dello stesso gruppo di risorse.

> [!TIP]
> L'eliminazione di un gruppo di risorse elimina anche i servizi in esso contenuti. Per i progetti prototipo che usano più servizi, l'inserimento di tutti gli elementi nello stesso gruppo di risorse ne semplifica l'eliminazione al termine del progetto. 

## <a name="select-a-hosting-location"></a>Selezionare un percorso di hosting 
Ricerca di Azure, in qualità di servizio di Azure, può essere ospitato nei data center di tutto il mondo. Tenere presente che i [prezzi possono variare](https://azure.microsoft.com/pricing/details/search/) in funzione dell'area geografica.

## <a name="select-a-pricing-tier-sku"></a>Selezionare un piano tariffario (SKU)
[Ricerca di Azure attualmente è disponibile con vari piani tariffari](https://azure.microsoft.com/pricing/details/search/): Gratuito, Basic o Standard. Ogni piano tariffario prevede una specifica [capacità e limiti](search-limits-quotas-capacity.md). Per indicazioni, vedere [Scegliere uno SKU o un piano tariffario per Ricerca di Azure](search-sku-tier.md) .

In questa procedura dettagliata è stato scelto il piano tariffario Standard per il servizio.

## <a name="create-your-service"></a>Creare il servizio

Ricordarsi di aggiungere il servizio al dashboard per semplificare l'accesso.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Ridimensionare il servizio
La creazione di un servizio può richiedere&15; minuti o più, a seconda del livello. Al termine del provisioning del servizio, è possibile ridimensionare il servizio per adattarlo alle proprie esigenze. Poiché è stato scelto il piano tariffario Standard per il servizio Ricerca di Azure, è possibile ridimensionare il servizio in due dimensioni, ovvero partizioni e repliche. Se fosse stato scelto il piano Basic, sarebbe stato possibile aggiungere solo le repliche. Se è stato effettuato il provisioning del servizio Gratuito, la scalabilità non è disponibile.

Le ***partizioni*** consentono di archiviare e di eseguire ricerche in un numero maggiore di documenti nel servizio.

***Repliche*** consentire al servizio gestire un carico superiore di query di ricerca.

> [!Important]
> Un servizio deve disporre di [2 repliche per ogni contratto di servizio di sola lettura e 3 repliche per ogni contratto di servizio di lettura/scrittura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Passare al pannello del servizio di ricerca nel portale di Azure.
2. Nel riquadro di navigazione a sinistra selezionare **Impostazioni** > **Scalabilità**.
3. Usare la barra di scorrimento per aggiungere repliche o partizioni.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Ogni livello presenta [limiti](search-limits-quotas-capacity.md) diversi per il numero totale di unità di ricerca consentite in un singolo servizio (repliche * partizioni = unità di ricerca totali).

## <a name="next-steps"></a>Passaggi successivi
Dopo il provisioning di un servizio di Ricerca di Azure è possibile [definire un indice](search-what-is-an-index.md) per caricare e cercare i dati.

Per accedere al servizio dal codice o dallo script, specificare l'URL (*nome-servizio*.search.windows.net) e una chiave. Le chiavi di amministrazione concedono l'accesso completo; le chiavi di query concedono l'accesso in sola lettura. [Come usare Ricerca di Azure in .NET](search-howto-dotnet-sdk.md) per iniziare.

Vedere [Compilare ed eseguire una query del primo indice](search-get-started-portal.md) per una rapida esercitazione basata sul portale.


