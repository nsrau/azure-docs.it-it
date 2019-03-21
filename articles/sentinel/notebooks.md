---
title: Le funzionalità di ricerca usando i notebook in Azure Sentinel Preview | Microsoft Docs
description: Questo articolo descrive come usare i notebook con le funzionalità di ricerca Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107679"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>Usare i notebook di risposta per le anomalie

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sentinel Azure sfrutta la potenza di Jupyter notebook interattivi per fornire informazioni dettagliate e azioni per analizzare o di risposta per le anomalie di sicurezza all'interno dell'ambiente. Azure Sentinel proviene precaricata con i notebook che sono stati sviluppati da analisti della sicurezza di Microsoft. Ogni notebook viene creato con un flusso di lavoro indipendente per un caso d'uso specifico. Visualizzazioni sono inclusi in ogni blocco appunti per l'esplorazione dei dati più veloce e ricerca di minacce. Personalizzare i notebook incorporati per soddisfare le proprie esigenze, creare nuovi notebook da zero o importare i notebook da Sentinel di Azure "della community di GitHub. I notebook di Jupyter vengono importati come un progetto nella pagina di Azure Notebooks: in questo modo l'utente per accedere a tutti i notebook di Azure Sentinel in un'unica posizione. I notebook possono essere eseguiti con un clic di un pulsante o possono essere configurati dall'utente in modo che corrisponda il proprio ambiente.

L'esperienza completamente integrata consente notebook eseguire nel Cloud di calcolo e archiviazione con nessun overhead di manutenzione sottostante. La possibilità di sfruttare l'ecosistema di Jupyter notebook esistente consente ai modelli di origine di crowdsourcing, senza condividere i dati dei clienti. 


Ogni notebook è ospitato in Azure Notebooks (attualmente in anteprima), un ambiente di sviluppo nel cloud di Azure. I notebook sono sempre accessibile, sempre disponibile da qualsiasi browser, ovunque nel mondo.  Il Azure Sentinel' notebook predefinito per l'analisi e ricerca vengono clonati in un progetto che appartiene all'utente e che è possibile modificare e personalizzare in base all'ambiente. Trai i notebook più diffusi predefiniti sono:

- **Avviso di analisi e ricerca**: Questo notebook interattivo Abilita rapida valutazione delle diverse categorie di avvisi per il recupero di attività correlata e l'arricchimento di avviso con attività associata e i dati da cui è stato generato l'avviso.

- **Host endpoint guidato caccia**: Consente di cercare la presenza di una violazione della sicurezza eseguendo il drill-nelle attività relative alla sicurezza associate a un host dell'endpoint.  

- **Office accesso guidato caccia**: Consente di risposta per gli accessi sospetti dalla visualizzazione dei log di stato sospetto di aree geografiche, nonché la visualizzazione di modelli di accesso insoliti derivati dai dati di Office 365.

## <a name="run-a-notebook"></a>Eseguire un notebook
Nell'esempio seguente, eseguiamo il notebook predefinito per la ricerca approfondita, approfondire le anomalie di percorso per verificare se tutti gli utenti in un percorso imprevisto sta eseguendo un'operazione su rete.

1. Nel portale di Azure Sentinel, fare clic su **notebook** e quindi selezionare uno qualsiasi dei notebook incorporato.
  
   ![Selezionare i notebook](./media/notebooks/select-notebook.png)

3. Fare clic su **importazione** clonare il repository di GitHub nel progetto Azure Notebooks.
   ![Notebook di importazione](./media/notebooks/import1.png)
4. Ogni notebook illustra i passaggi per eseguire una ricerca o un'indagine. Modelli, librerie e altre dipendenze e configurazione per la connettività per Azure Sentinel viene importato automaticamente per consentire l'esecuzione di un solo clic. Tutto il codice e le librerie necessarie per eseguire un notebook sono precaricate. È possibile iniziare immediatamente in esecuzione un notebook per l'area di lavoro di Log Analitica senza alcuna configurazione.

   ![Importazione del repository](./media/notebooks/import2.png)

5. Esplorare, modificare ed eseguire tutti i notebook di esempio forniti. Possono essere usati come blocchi predefiniti per molti scenari diversi.

   ![Selezionare i notebook](./media/notebooks/import3.png)



## <a name="next-steps"></a>Passaggi successivi
In questo articolo si appreso come eseguire un'analisi di ricerca usando notebook in Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- [Cercare in modo proattivo le minacce](hunting.md)
- [Usare i segnalibri per salvare le informazioni interessante durante la ricerca](bookmarks.md)