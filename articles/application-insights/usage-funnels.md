---
title: Imbuti di Azure Application Insights
description: Informazioni su come usare gli imbuti per scoprire in che modo i clienti interagiscono con l'applicazione.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: mbullwin
ms.openlocfilehash: 0396c59d9d95ab71f0af04029d87afbb6e47dc35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Informazioni sulle modalità di utilizzo dell'applicazione da parte dei clienti attraverso gli imbuti di Application Insights

Per un'azienda è di primaria importanza analizzare l'utilizzo del software da parte dei clienti. Se l'applicazione implica più fasi, è necessario sapere se la maggior parte dei clienti prosegue lungo l'intero processo o se lo termina in un determinato punto. L'avanzamento attraverso una serie di passaggi in un'applicazione Web è noto come "imbuto". È possibile usare gli imbuti di Application Insights per ottenere informazioni approfondite sugli utenti e per monitorare i tassi di conversione a ogni passaggio. 

## <a name="create-your-funnel"></a>Creare il proprio imbuto
Prima di creare il proprio imbuto, è necessario decidere a quale domanda si intende rispondere. È possibile, ad esempio, che si voglia sapere quanti utenti stanno visitando la home page, quanti stanno visualizzando un profilo cliente e quanti stanno creando un ticket. In questo esempio, i proprietari della società Fabrikam Fiber vogliono conoscere la percentuale di clienti che ha creato correttamente un ticket.

Ecco i passaggi per creare l'imbuto.

1. Fare clic sul pulsante Nuovo nello strumento Imbuti.
1. Selezionare l'intervallo di tempo "Ultimi 90 giorni" dal menu a discesa **Intervallo di tempo**. Selezionare "Imbuti personali" o "Imbuti condivisi"
1. Selezionare l'evento **Indice** dall'elenco a discesa **Passaggio 1**. 
1. Selezionare l'evento **Cliente** dall'elenco a discesa **Passaggio 2**.
1. Selezionare l'evento **Crea** dall'elenco a discesa **Passaggio 3**.
1. Aggiungere un nome per l'imbuto e fare clic su **Salva**.

La figura seguente illustra i dati generati dallo strumento Imbuti. Da questa immagine i proprietari di Fabrikam possono vedere che, negli ultimi 90 giorni, il 54,3% dei clienti che hanno visitato la home page ha creato un ticket cliente. Possono vedere anche che 2700 clienti sono giunti all'indice dalla home page e questo può indicare un problema di aggiornamento.


![Strumento Imbuti con dati](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnel-features"></a>Funzionalità degli imbuti
1. Se l'app è campionata, verrà visualizzato un banner di campionamento. Facendo clic sul banner verrà visualizzato un riquadro di contesto che indica come disattivare il campionamento. 
2. È possibile esportare l'imbuto in [Power BI](app-insights-export-power-bi.md).
3. Fare clic su un passaggio per ottenere informazioni dettagliate sulla destra. 
4. La conversione cronologica mostra la conversione negli ultimi 90 giorni. 
5. Accedendo allo strumento Utenti da Imbuti, è possibile comprendere meglio il comportamento degli utenti. Ogni passaggio fornisce filtri utente dettagliati. 

## <a name="next-steps"></a>Passaggi successivi
  * [Panoramica sull'uso](app-insights-usage-overview.md)
  * [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
  * [Conservazione](app-insights-usage-retention.md)
  * [Cartelle di lavoro](app-insights-usage-workbooks.md)
  * [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)
  * [Esportare in Power BI](app-insights-export-power-bi.md)

