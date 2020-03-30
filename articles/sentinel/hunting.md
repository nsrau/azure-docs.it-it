---
title: Capacità di caccia in Azure Sentinel Documenti Microsoft
description: Questo articolo descrive come usare le funzionalità di caccia di Azure Sentinel.This article describes how to use the Azure Sentinel hunting capabilities.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: yelevin
ms.openlocfilehash: 54ddf6818b95a4037188ab222501ddfa69b28149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587899"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Caccia alle minacce con Azure Sentinel

Se sei un investigatore che vuole essere proattivo sulla ricerca di minacce alla sicurezza, Azure Sentinel potente ricerca e strumenti di query di ricerca per la ricerca di minacce alla sicurezza tra le origini dati dell'organizzazione. Ma i sistemi e gli apparecchi di sicurezza generano montagne di dati che possono essere difficili da analizzare e filtrare in eventi significativi. Per consentire agli analisti della sicurezza di cercare in modo proattivo nuove anomalie che non sono state rilevate dalle app di sicurezza, le query di caccia integrate di Azure Sentinel guidano l'utente a porre le domande giuste per individuare i problemi nei dati già presenti nella rete. 

Ad esempio, una query incorporata fornisce dati sui processi più non comuni in esecuzione sull'infrastruttura: non si desidera un avviso su ogni volta che vengono eseguiti, potrebbero essere completamente innocenti, ma è possibile dare un'occhiata alla query in occasione di c'è qualcosa di insolito. 



Con la caccia ad Azure Sentinel, è possibile sfruttare le funzionalità seguenti:With Azure Sentinel hunting, you can take advantage of the following capabilities:

- Query predefinite: per iniziare, una pagina iniziale fornisce esempi di query precaricate progettati per iniziare e acquisire familiarità con le tabelle e il linguaggio di query. Queste query di caccia integrate sono sviluppate dai ricercatori di sicurezza Microsoft in modo continuo, aggiungendo nuove query e perfezionando le query esistenti per fornire un punto di ingresso per cercare nuovi rilevamenti e capire da dove iniziare la caccia per il nuovi attacchi. 

- Potente linguaggio di query con IntelliSense: costruito su un linguaggio di query che offre la flessibilità necessaria per portare la caccia al livello successivo.

- Crea i tuoi segnalibri: durante il processo di caccia, potresti imbatterti in partite o risultati, dashboard o attività che sembrano insolite o sospette. Per contrassegnare tali elementi in modo da potertornare ad essi in futuro, utilizzare la funzionalità dei segnalibri. I segnalibri consentono di salvare gli elementi per un secondo momento, da utilizzare per creare un evento imprevisto per l'analisi. Per ulteriori informazioni sui segnalibri, consultate [Usare i segnalibri nella caccia.](hunting.md)
- Usa i blocchi appunti per automatizzare l'indagine: i notebook sono come playbook passo-passo che puoi creare per eseguire i passaggi di un'indagine e di una caccia.  I blocchi appunti incapsulano tutti i passaggi di caccia in un playbook riutilizzabile che può essere condiviso con altri utenti dell'organizzazione. 
- Interrogare i dati memorizzati: i dati sono accessibili nelle tabelle per l'esecuzione di query. Ad esempio, è possibile eseguire query sulla creazione di processi, sugli eventi DNS e su molti altri tipi di eventi.

- Collegamenti alla community: sfrutta la potenza della community per trovare query e origini dati aggiuntive.
 
## <a name="get-started-hunting"></a>Inizia a cacciare

1. Nel portale di Azure Sentinel fare clic su **Caccia**.
  ![Azure Sentinel inizia la caccia](media/tutorial-hunting/hunting-start.png)

2. Quando si apre la pagina **di caccia,** tutte le query di caccia vengono visualizzate in un'unica tabella. Nella tabella sono elencate tutte le query scritte dal team di analisti della sicurezza di Microsoft, nonché tutte le query aggiuntive create o modificate. Ogni query fornisce una descrizione di ciò che si esegue per e il tipo di dati che viene eseguito su. Questi modelli sono raggruppati per le loro varie tattiche - le icone a destra categorizzano il tipo di minaccia, come l'accesso iniziale, la persistenza e l'esfiltrazione. È possibile filtrare questi modelli di query di ricerca utilizzando uno qualsiasi dei campi. È possibile salvare qualsiasi query nei preferiti. Salvando una query nei preferiti, la query viene eseguita automaticamente ogni volta che si accede alla pagina **di caccia.** È possibile creare una query di caccia personalizzata o clonare e personalizzare un modello di query di caccia esistente. 
 
2. Fare clic su **Esegui query** nella pagina dei dettagli della query di ricerca per eseguire qualsiasi query senza uscire dalla pagina di caccia.  Il numero di corrispondenze viene visualizzato all'interno della tabella. Esaminare l'elenco delle query di caccia e le relative corrispondenze. Scopri a quale fase della catena di uccisioni è associata la corrispondenza.

3. Eseguire una revisione rapida della query sottostante nel riquadro dei dettagli della query oppure fare clic su **Visualizza risultato query** per aprire la query in Log Analytics. Nella parte inferiore, esaminare le corrispondenze per la query.

4.  Fare clic sulla riga e selezionare **Aggiungi segnalibro** per aggiungere le righe da analizzare - è possibile farlo per tutto ciò che sembra sospetto. 

5. Quindi, tornare alla pagina principale **di caccia** e fare clic sulla scheda **Segnalibri** per visualizzare tutte le attività sospette. 

6. Selezionare un segnalibro e quindi fare clic su **Analizza** per aprire l'esperienza di indagine. È possibile filtrare i segnalibri. Ad esempio, se stai immaciando una campagna, puoi creare un tag per la campagna e quindi filtrare tutti i segnalibri in base alla campagna.

1. Dopo aver individuato quale query di ricerca fornisce informazioni dettagliate di alto valore nei possibili attacchi, è anche possibile creare regole di rilevamento personalizzate in base alla query e visualizzare tali informazioni come avvisi per i risponditori di eventi imprevisti di sicurezza.

 

## <a name="query-language"></a>Linguaggio di query 

La caccia in Azure Sentinel è basata sul linguaggio di query Kusto.Hunting in Azure Sentinel is based on Kusto query language. Per ulteriori informazioni sul linguaggio di query e sugli operatori supportati, vedere Guida di riferimento al [linguaggio di query](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Ricerca pubblica repository GitHub

Estrarre il [repository di query di Hunting](https://github.com/Azure/Orion). Contribuisci e usa query di esempio condivise dai nostri clienti.

 

## <a name="sample-query"></a>Query di esempio

Una query tipica inizia con un nome di tabella seguito da una serie di operatori separati da \|.

Nell'esempio precedente, iniziare con il nome di tabella SecurityEvent e aggiungere gli elementi reindirizzati in base alle esigenze.

1. Definire un filtro temporale per esaminare solo i record dei sette giorni precedenti.

2. Aggiungere un filtro nella query per visualizzare solo l'ID evento 4688.

3. Aggiungere un filtro nella query nella riga di comando per contenere solo le istanze di cscript.exe.

4. Proiettare solo le colonne che si desidera esplorare e limitare i risultati a 1000 e fare clic su **Esegui query**.
5. Fare clic sul triangolo verde ed eseguire la query. È possibile testare la query ed eseguirla per cercare un comportamento anomalo.

## <a name="useful-operators"></a>Operatori utili

Il linguaggio di query è potente e ha molti operatori disponibili, alcuni operatori utili sono elencati qui:

**where** - Filtra una tabella in base al subset di righe che soddisfano un predicato.

**sum** - Produce una tabella che aggrega il contenuto della tabella di input.

**join** - Unisce le righe di due tabelle per formare una nuova tabella abbinando i valori delle colonne specificate da ogni tabella.

**count** - Restituisce il numero di record nel set di record di input.

**top** - Restituisce i primi N record ordinati in base alle colonne specificate.

**limit** - Restituisce fino al numero di righe specificato.

**project:** selezionare le colonne da includere, rinominare o eliminare e inserire nuove colonne calcolate.

**extend** - Crea colonne calcolate e aggiungile al set di risultati.

**makeset** - Restituisce una matrice dinamica (JSON) del set di valori distinti che Expr accetta nel gruppo

**find** - Trova le righe che corrispondono a un predicato in un set di tabelle.

## <a name="save-a-query"></a>Salvare una query

È possibile creare o modificare una query e salvarla come query personalizzata o condividerla con utenti che si trovano nello stesso tenant.

   ![Salvare la query](./media/tutorial-hunting/save-query.png)

Creare una nuova query di ricerca:

1. Fare clic su **Nuova query** e selezionare **Salva**.
2. Compilare tutti i campi vuoti e selezionare **Salva**.

   ![Nuova query](./media/tutorial-hunting/new-query.png)

Clonare e modificare una query di ricerca esistente:

1. Selezionare la query di ricerca nella tabella che si desidera modificare.
2. Selezionare i conivigli (...) nella riga della query che si desidera modificare e selezionare **Clona query**.

   ![query clone](./media/tutorial-hunting/clone-query.png)
 

3. Modificare la query e selezionare **Crea**.

   ![query personalizzata](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passaggi successivi
In this article, you learned how to run a hunting investigation with Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Utilizzare i blocchi appunti per eseguire campagne di caccia automatizzate](notebooks.md)
- [Utilizzare i segnalibri per salvare informazioni interessanti durante la caccia](bookmarks.md)
