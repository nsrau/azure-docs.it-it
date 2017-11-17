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
ms.openlocfilehash: 5d55207784390a68a3b4d87f7a3d4773491d08b8
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="discover-how-customers-are-using-your-application-with-the-application-insights-funnels"></a>Informazioni sulle modalità di utilizzo dell'applicazione da parte dei clienti attraverso gli imbuti di Application Insights

Per un'azienda è di primaria importanza analizzare l'utilizzo del software da parte dei clienti. Se l'applicazione implica più fasi, è necessario sapere se la maggior parte dei clienti prosegue lungo l'intero processo o se lo termina in un determinato punto. L'avanzamento attraverso una serie di passaggi in un'applicazione Web è noto come "imbuto". È possibile usare gli imbuti di Application Insights per ottenere informazioni approfondite sugli utenti e per monitorare i tassi di conversione a ogni passaggio. 

## <a name="get-started-with-the-funnels-blade"></a>Introduzione al pannello Imbuti
Il modo più semplice per imparare a usare gli imbuti consiste nel seguire un esempio. Le illustrazioni seguenti mostrano i passaggi che i proprietari di un'azienda di e-commerce devono seguire per scoprire in che modo i clienti interagiscono con l'applicazione Web dell'azienda.  

### <a name="create-your-funnel"></a>Creare il proprio imbuto
Prima di creare il proprio imbuto, è necessario decidere a quale domanda si intende rispondere. Si potrebbe ad esempio voler scoprire quanti clienti tra quelli che visitano la home page del sito selezionano un annuncio. In questo esempio, i proprietari della società Fabrikam Fiber desiderano conoscere la percentuale di clienti che ha effettuato l'acquisto dopo avere aggiunto articoli al carrello acquisti nell'ultimo mese.

Ecco i passaggi per creare l'imbuto.

1. Fare clic sul pulsante Nuovo nel pannello Imbuti.
1. Selezionare l'intervallo di tempo "Ultimo mese" dal menu a discesa **Intervallo di tempo**. 
1. Selezionare l'evento **Product page** (Pagina prodotto) dall'elenco a discesa **Step 1** (Passaggio 1). 
1. Selezionare l'evento **Add to shopping cart** (Aggiungi al carrello acquisti) dall'elenco a discesa **Step 2** (Passaggio 2).
1. Selezionare l'evento **Click purchase** (Clic su acquisto) dall'elenco a discesa **Step 3** (Passaggio 3).
1. Aggiungere un nome per l'imbuto e fare clic su **Salva**.

La figura seguente mostra i dati generati dal pannello Imbuti. Da qui i proprietari di Fabrikam possono vedere che, durante l'ultima settimana, il 22,7% dei clienti che hanno aggiunto un articolo al carrello acquisti ha completato l'acquisto. Possono inoltre vedere che l'1% dei clienti ha fatto clic su un annuncio prima di visitare la pagina del prodotto e che il 20% dei clienti si è disconnesso dopo avere completato l'acquisto.


![Pannello Imbuti con dati](./media/app-insights-understand-usage-patterns/funnel1.png)

## <a name="next-steps"></a>Passaggi successivi
  * [Panoramica sull'uso](app-insights-usage-overview.md)
  * [Utenti, Sessioni ed Eventi](app-insights-usage-segmentation.md)
  * [Conservazione](app-insights-usage-retention.md)
  * [Cartelle di lavoro](app-insights-usage-workbooks.md)
  * [Aggiungere il contesto utente](app-insights-usage-send-user-context.md)
  * [Esportare in Power BI](app-insights-export-power-bi.md)

