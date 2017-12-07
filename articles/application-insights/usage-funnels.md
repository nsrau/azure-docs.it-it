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
ms.openlocfilehash: bbb25af888f34737f6a61cf43890ff248c4cc4de
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Informazioni sulle modalità d'uso dell'applicazione da parte dei clienti attraverso gli imbuti di Application Insights

Per un'azienda è di primaria importanza comprendere l'esperienza dei clienti. Se l'applicazione implica più fasi, è necessario sapere se la maggior parte dei clienti prosegue lungo l'intero processo o se lo termina in un determinato punto. L'avanzamento attraverso una serie di passaggi in un'applicazione Web è noto come *imbuto*. È possibile usare lo strumento Imbuti di Azure Application Insights per ottenere informazioni approfondite sugli utenti e per monitorare i tassi di conversione a ogni passaggio. 

## <a name="create-your-funnel"></a>Creare il proprio imbuto
Prima di creare il proprio imbuto, decidere a quale domanda si intende rispondere. È possibile, ad esempio, che si voglia sapere quanti utenti stanno visitando la home page, quanti stanno visualizzando un profilo cliente e quanti stanno creando un ticket. In questo esempio, i proprietari della società Fabrikam Fiber vogliono conoscere la percentuale di clienti che ha creato correttamente un ticket.

Ecco i passaggi per creare l'imbuto.

1. Nello strumento Imbuti di Application Insights selezionare **Nuovo**.
1. Nel menu a discesa **Intervallo di tempo** selezionare **Ultimi 90 giorni**. Selezionare **Imbuti personali** o **Imbuti condivisi**.
1. Selezionare **Indice** nell'elenco a discesa **Passaggio 1**. 
1. Selezionare **Cliente** nell'elenco a discesa **Passaggio 2**.
1. Selezionare **Crea** nell'elenco a discesa **Passaggio 3**.
1. Aggiungere un nome per l'imbuto e selezionare **Salva**.

Lo screenshot seguente mostra un esempio del tipo di dati generato dallo strumento Imbuti. I proprietari di Fabrikam possono vedere che, negli ultimi 90 giorni, il 54,3% dei clienti che hanno visitato la home page ha creato un ticket cliente. Possono inoltre appurare che 2.700 clienti hanno raggiunto l'indice dalla home page. Ciò potrebbe indicare un problema di aggiornamento.


![Screenshot dello strumento Imbuti con dati](./media/app-insights-understand-usage-patterns/funnel1.png)

### <a name="funnels-features"></a>Funzionalità Imbuti
Lo screenshot precedente include cinque aree evidenziate. Queste sono funzionalità dello strumento Imbuti. L'elenco seguente spiega in maggiore dettaglio ogni area nello screenshot:
1. Se l'app è campionata, verrà visualizzato un banner di campionamento. Selezionando l'intestazione viene aperto un riquadro di contesto che descrive come disattivare il campionamento. 
2. È possibile esportare l'imbuto in [Power BI](app-insights-export-power-bi.md).
3. Selezionare un passaggio per visualizzare ulteriori dettagli sulla destra. 
4. Il grafico di conversione cronologica mostra i tassi di conversione negli ultimi 90 giorni. 
5. Accedendo allo strumento Utenti, è possibile comprendere meglio il comportamento degli utenti. È possibile usare i filtri in ogni passaggio. 

## <a name="next-steps"></a>Passaggi successivi
  * [Panoramica sull'uso](app-insights-usage-overview.md)
  * [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
  * [Conservazione](app-insights-usage-retention.md)
  * [Cartelle di lavoro](app-insights-usage-workbooks.md)
  * [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)
  * [Esportare in Power BI](app-insights-export-power-bi.md)

