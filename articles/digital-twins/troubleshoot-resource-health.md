---
title: Informazioni sull'integrità delle risorse
titleSuffix: Azure Digital Twins
description: Vedere come usare Integrità risorse di Azure per verificare l'integrità dell'istanza di Azure Digital gemelli.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616550"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Risoluzione dei problemi relativi a dispositivi gemelli digitali di Azure: integrità risorse

[Integrità risorse di Azure](../service-health/resource-health-overview.md) consente di diagnosticare e ottenere supporto per i problemi del servizio che interessano le risorse di Azure. Segnala l'integrità corrente e passata delle risorse.

Questo articolo illustra come ottenere informazioni sull' **integrità delle risorse** per le istanze di dispositivi gemelli digitali di Azure.

## <a name="use-azure-resource-health"></a>Usare Integrità risorse di Azure

Integrità risorse di Azure possono essere utili per monitorare se l'istanza di Azure Digital gemelli è attiva e in esecuzione. È anche possibile usarlo per capire se un'interruzione a livello di area influisca sull'integrità dell'istanza.

Per verificare l'integrità dell'istanza, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Dal menu dell'istanza selezionare _**integrità risorse**_ in supporto e *risoluzione dei problemi*. Verrà visualizzata la pagina per visualizzare la cronologia di integrità delle risorse. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Screenshot che mostra la pagina &quot;integrità risorse&quot;. È presente una sezione &quot;cronologia di integrità&quot; che mostra un report giornaliero degli ultimi nove giorni. Ogni giorno Visualizza lo stato &quot;disponibile&quot;.":::

Nell'immagine precedente, questa istanza viene visualizzata come *disponibile* ed è stata negli ultimi nove giorni. Per altre informazioni sullo stato *disponibile* e sugli altri tipi di stato che possono essere visualizzati, vedere [*Panoramica di integrità risorse di Azure*](../service-health/resource-health-overview.md).

È anche possibile ottenere altre informazioni sui diversi controlli che entrano in integrità risorse per diversi tipi di risorse di Azure in [*tipi di risorse e controlli di integrità in integrità risorse di Azure*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per monitorare l'istanza di Azure Digital Twins, vedere gli articoli seguenti:
* [*Risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md)
* [*Risoluzione dei problemi: configurare la diagnostica*](troubleshoot-diagnostics.md).
* [*Risoluzione dei problemi: configurare gli avvisi*](troubleshoot-alerts.md)
