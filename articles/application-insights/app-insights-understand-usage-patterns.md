---
title: Imbuti di Azure Application Insights
description: Informazioni su come usare gli imbuti per scoprire in che modo i clienti interagiscono con l'applicazione.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 85f47daaaff8967eb83c330bab839023f128b486
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

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
- Altre informazioni sull'[analisi dell'utilizzo](app-insights-usage-overview.md). 

