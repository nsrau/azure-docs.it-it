---
title: Imbuti di Azure Application Insights
description: Informazioni su come usare gli imbuti per scoprire in che modo i clienti interagiscono con l'applicazione.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: d6a6e34f9688747891ac91401941a96b6e85d76a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899464"
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


![Screenshot dello strumento Imbuti con dati](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funzionalità Imbuti
Lo screenshot precedente include cinque aree evidenziate. Queste sono funzionalità dello strumento Imbuti. L'elenco seguente spiega in maggiore dettaglio ogni area nello screenshot:
1. Se l'app è campionata, verrà visualizzato un banner di campionamento. Selezionando l'intestazione viene aperto un riquadro di contesto che descrive come disattivare il campionamento. 
2. È possibile esportare l'imbuto in [Power BI](../../azure-monitor/app/export-power-bi.md ).
3. Selezionare un passaggio per visualizzare ulteriori dettagli sulla destra. 
4. Il grafico di conversione cronologica mostra i tassi di conversione negli ultimi 90 giorni. 
5. Accedendo allo strumento Utenti, è possibile comprendere meglio il comportamento degli utenti. È possibile usare i filtri in ogni passaggio. 

## <a name="next-steps"></a>Passaggi successivi
  * [Panoramica sull'uso](usage-overview.md)
  * [Utenti, Sessioni ed Eventi](usage-segmentation.md)
  * [Conservazione](usage-retention.md)
  * [Cartelle di lavoro](../../azure-monitor/app/usage-workbooks.md)
  * [Aggiungere il contesto utente](usage-send-user-context.md)
  * [Esportare in Power BI](../../azure-monitor/app/export-power-bi.md )

