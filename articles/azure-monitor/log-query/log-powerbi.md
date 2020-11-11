---
title: Integrazione di Log Analytics con Power BI ed Excel
description: Come inviare risultati da Log Analytics a Power BI
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: a4e2faf87ac3dddf91e6945343a46b02df72db0a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507501"
---
# <a name="log-analytics-integration-with-power-bi"></a>Integrazione di Log Analytics con Power BI

Questo articolo è incentrato sui modi per inserire i dati da Log Analytics a Microsoft Power BI per creare report e dashboard più accattivanti visivamente. 

## <a name="background"></a>Sfondo 

Log di monitoraggio di Azure è una piattaforma che offre una soluzione end-to-end per l'inserimento dei log. Il [log Analytics di monitoraggio di Azure](../platform/data-platform.md#) è l'interfaccia per eseguire query su questi log. Per altre informazioni sull'intera piattaforma dati di monitoraggio di Azure, tra cui Log Analytics, vedere [piattaforma dati di monitoraggio](../platform/data-platform.md)di Azure. 

Microsoft Power BI è la piattaforma di visualizzazione dei dati di Microsoft. Per ulteriori informazioni su come iniziare, vedere [la Home page di Power bi](https://powerbi.microsoft.com/). 


In generale, è possibile usare le funzionalità gratuite di Power BI per integrare e creare report e dashboard visivamente accattivanti.

Funzionalità più avanzate possono richiedere l'acquisto di un account Power BI Pro o Premium. Queste funzionalità includono: 
 - condivisione del lavoro 
 - aggiornamenti pianificati
 - App di Power BI 
 - flussi di data e aggiornamento incrementale 

Per altre informazioni, vedere [altre informazioni sui prezzi e sulle funzionalità di Power bi](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Integrazione di query  

Power BI usa il [linguaggio di query M](/powerquery-m/power-query-m-language-specification/) come linguaggio di query principale. 

Log Analytics le query possono essere esportate in M e usate direttamente nella Power BI. Dopo aver eseguito una query con esito positivo, selezionare l' **esportazione in Power BI (query M)** dal pulsante **Esporta** nella barra delle azioni della log Analytics dell'interfaccia utente.

:::image type="content" source="media/log-powerbi-excel/export-query2.png" alt-text="Log Analytics query che mostra l'opzione di esportazione menu a discesa" border="true":::

Log Analytics crea un file con estensione txt contenente il codice M che può essere utilizzato direttamente nella Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Connessione dei log a un set di dati 

Un set di dati Power BI è un'origine di dati pronti per la creazione di report e la visualizzazione. Per connettere una query Log Analytics a un set di dati, copiare il codice M esportato da Log Analytics in una query vuota in Power BI. 

Per ulteriori informazioni, vedere informazioni sui set di dati [Power bi](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Raccogliere dati con Power BI flussi di dati 

Power BI flussi di dati consentono inoltre di raccogliere e archiviare i dati. Per altre informazioni, vedere [Power bi flussi](/power-bi/service-dataflows-overview)di dati.

Un flusso di dati è un tipo di "ETL cloud" progettato per facilitare la raccolta e la preparazione dei dati. Un set di dati è il "modello" progettato per consentire la connessione di entità diverse e per modellarle in base alle esigenze.

## <a name="incremental-refresh"></a>Aggiornamento incrementale 

Sia i set di impostazioni di Power BI che i flussi di Power BI hanno un'opzione di aggiornamento incrementale. Power BI i flussi di Power BI e i set di impostazioni di DataSet supportano questa funzionalità, ma è necessario Power BI Premium usarla.  


L'aggiornamento incrementale esegue query di piccole dimensioni e Aggiorna quantità minori di dati per esecuzione anziché inserire nuovamente tutti i dati e quando si esegue la query. È possibile salvare grandi quantità di dati, ma aggiungere un nuovo incremento dei dati ogni volta che si esegue la query. Questo comportamento è ideale per i report con esecuzione prolungata.

Power BI aggiornamento incrementale si basa sull'esistenza di un valore *DateTime* archiviato nel set di risultati. Prima di configurare l'aggiornamento incrementale, verificare che il set di risultati della query Log Analytics includa almeno un valore *DateTime* archiviato. 

Per altre informazioni e per informazioni su come configurare l'aggiornamento incrementale, vedere [Power bi DataSet e aggiornamento incrementale](/power-bi/service-premium-incremental-refresh) e [Power bi flussi di data e aggiornamento incrementale](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Report e dashboard

Dopo che i dati sono stati inviati a Power BI, è possibile continuare a usare Power BI per creare report e dashboard.

Per ulteriori informazioni, vedere [questa guida su come creare il primo modello di Power bi e il report](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>integrazione di Excel

Per l'integrazione con un foglio di calcolo di Excel, è possibile usare la stessa integrazione M usata in Power BI. Per ulteriori informazioni, vedere la [Guida all'integrazione con Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) e quindi incollare la query M esportata da log Analytics.

Ulteriori informazioni sono reperibili in [integrare log Analytics ed Excel](log-excel.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione alle [query log Analytics](log-query-overview.md).
