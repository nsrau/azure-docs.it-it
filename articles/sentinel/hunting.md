---
title: Funzionalità di ricerca nell'anteprima di Azure Sentinel | Microsoft Docs
description: Questo articolo descrive come usare le funzionalità di ricerca di Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5e6ad3c0b415722349dc584434add1031b7c3cb1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780454"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Cercare le minacce con nell'anteprima di Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se sei un investigatore che vuole essere proattivo per la ricerca di minacce per la sicurezza, Azure Sentinel Cerca potenti strumenti di ricerca e query per cercare le minacce alla sicurezza nelle origini dati dell'organizzazione. Tuttavia, i sistemi e le appliance di sicurezza generano montagne di dati che possono risultare difficili da analizzare e filtrare in eventi significativi. Per aiutare gli analisti della sicurezza a cercare in modo proattivo nuove anomalie che non sono state rilevate dalle app per la sicurezza, le query di ricerca predefinite di Azure Sentinel consentono di porre le domande appropriate per individuare i problemi nei dati già presenti nella rete. 

Una query predefinita, ad esempio, fornisce dati relativi ai processi più comuni in esecuzione nell'infrastruttura. non è necessario un avviso per ogni volta che vengono eseguiti, ma possono essere completamente innocenti, ma è consigliabile esaminare la query in occasione per verificare se prima di tutto è insolito. 



Con Azure Sentinel Hunting è possibile sfruttare le funzionalità seguenti:

- Query predefinite: Per iniziare, una pagina iniziale fornisce esempi di query precaricati progettati per iniziare e acquisire familiarità con le tabelle e il linguaggio di query. Queste query di caccia predefinite sono sviluppate dai ricercatori della sicurezza Microsoft su base continua, aggiungendo nuove query e ottimizzando le query esistenti per fornire un punto di ingresso per cercare nuovi rilevamenti e scoprire dove iniziare a cercare il inizia con nuovi attacchi. 

- Linguaggio di query avanzato con IntelliSense: Basato su un linguaggio di query che ti offre la flessibilità necessaria per passare al livello successivo.

- Creare segnalibri personalizzati: Durante il processo di ricerca, è possibile che si trovino corrispondenze o risultati, dashboard o attività che hanno un aspetto insolito o sospetto. Per contrassegnare gli elementi in modo da poterli tornare in futuro, usare la funzionalità di segnalibro. I segnalibri consentono di salvare gli elementi per un momento successivo, da usare per creare un evento imprevisto per l'analisi. Per ulteriori informazioni sui segnalibri, vedere Use [Bookmarks in Hunting].

- Usare i notebook per automatizzare l'analisi: I notebook sono simili a schemi Step-by-step che è possibile compilare per esaminare i passaggi di un'indagine e una ricerca.  I notebook incapsulano tutti i passaggi di caccia in un PlayBook riutilizzabile che può essere condiviso con altri utenti dell'organizzazione. 
- Eseguire una query sui dati archiviati: I dati sono accessibili nelle tabelle in cui è possibile eseguire una query. Ad esempio, è possibile eseguire query su creazione di processi, eventi DNS e molti altri tipi di evento.

- Collegamenti alla community: Sfrutta la potenza della community maggiore per trovare query e origini dati aggiuntive.
 
## <a name="get-started-hunting"></a>Inizia a cercare

1. Nel portale di Azure Sentinel fare clicsu Hunting.
  ![Azure Sentinel avvia la ricerca](media/tutorial-hunting/hunting-start.png)

2. Quando si apre la pagina di **ricerca** , tutte le query di ricerca vengono visualizzate in un'unica tabella. La tabella elenca tutte le query scritte dal team Microsoft degli analisti di sicurezza, nonché qualsiasi query aggiuntiva creata o modificata. Ogni query fornisce una descrizione di ciò che cerca e del tipo di dati in cui viene eseguito. Questi modelli sono raggruppati in base alle varie tattiche, ovvero le icone a destra categorizzano il tipo di minaccia, ad esempio l'accesso iniziale, la persistenza e la exfiltration. È possibile filtrare i modelli di query di caccia usando uno dei campi. È possibile salvare qualsiasi query nei Preferiti. Salvando una query nei Preferiti, la query viene eseguita automaticamente ogni volta che si accede alla pagina di **ricerca** . È possibile creare una query di ricerca personalizzata o clonare e personalizzare un modello di query di ricerca esistente. 
 
2. Fare clic su **Esegui query** nella pagina dei dettagli della query di ricerca per eseguire qualsiasi query senza uscire dalla pagina di caccia.  Il numero di corrispondenze viene visualizzato all'interno della tabella. Esaminare l'elenco di query di ricerca e le relative corrispondenze. Verificare la fase della catena di Kill a cui è associata la corrispondenza.

3. Eseguire una rapida verifica della query sottostante nel riquadro Dettagli query oppure fare clic su **Visualizza risultati query** per aprire la query in log Analytics. Nella parte inferiore esaminare le corrispondenze per la query.

4.  Fare clic sulla riga e selezionare **Aggiungi segnalibro** per aggiungere le righe da analizzare. è possibile eseguire questa operazione per qualsiasi elemento che sembra sospetto. 

5. Tornare quindi alla pagina principale di **Hunting** e fare clic sulla scheda **segnalibri** per visualizzare tutte le attività sospette. 

6. Selezionare un segnalibro e quindi fare clic su Cerca per aprire l'esperienza di analisi. È possibile filtrare i segnalibri. Ad esempio, se si sta esaminando una campagna, è possibile creare un tag per la campagna, quindi filtrare tutti i segnalibri in base alla campagna.

1. Dopo aver individuato la query di ricerca che fornisce informazioni dettagliate sul valore di possibili attacchi, è anche possibile creare regole di rilevamento personalizzate basate sulla query e far emergere tali informazioni come avvisi per i risponditori degli eventi imprevisti della sicurezza.

 

## <a name="query-language"></a>Linguaggio di query 

La ricerca in Sentinel di Azure è basata sul linguaggio di query di Azure Log Analytics. Per altre informazioni sul linguaggio di query e sugli operatori supportati, vedere [riferimento al linguaggio di query](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Repository GitHub della query di ricerca pubblica

Vedere il [repository di query di caccia](https://github.com/Azure/Orion). Collaborazione e utilizzo di query di esempio condivise dai clienti.

 

## <a name="sample-query"></a>Query di esempio

Una query tipica inizia con un nome di tabella seguito da una serie di operatori separati \|da.

Nell'esempio precedente, iniziare con il nome della tabella SecurityEvent e aggiungere gli elementi inviati tramite pipe in base alle esigenze.

1. Definire un filtro temporale per esaminare solo i record dei sette giorni precedenti.

2. Aggiungere un filtro nella query per visualizzare solo l'ID evento 4688.

3. Aggiungere un filtro nella query nella riga di comando per contenere solo le istanze di cscript. exe.

4. Proiettare solo le colonne a cui si è interessati a esplorare e limitare i risultati a 1000 e fare clic su **Esegui query**.
5. Fare clic sul triangolo verde ed eseguire la query. È possibile testare la query ed eseguirla per cercare un comportamento anomalo.

## <a name="useful-operators"></a>Operatori utili

Il linguaggio di query è potente ed è dotato di molti operatori disponibili. di seguito sono elencati alcuni operatori utili:

**dove** -filtrare una tabella per il subset di righe che soddisfano un predicato.

**riepilogare** : creare una tabella che aggrega il contenuto della tabella di input.

**join** : unisce le righe di due tabelle per formare una nuova tabella in base ai valori corrispondenti delle colonne specificate da ogni tabella.

**count** : restituisce il numero di record nel set di record di input.

**Top** : restituisce i primi N record ordinati in base alle colonne specificate.

**limit** -restituisce fino al numero di righe specificato.

**progetto** : selezionare le colonne da includere, rinominare o eliminare e inserire nuove colonne calcolate.

**extend** : consente di creare colonne calcolate e di aggiungerle al set di risultati.

**maket** : restituisce una matrice dinamica (JSON) del set di valori distinct che expr accetta nel gruppo

**Find** : trova le righe che corrispondono a un predicato in un set di tabelle.

## <a name="save-a-query"></a>Salvare una query

È possibile creare o modificare una query e salvarla come query o condividerla con utenti che si trovano nello stesso tenant.

   ![Salvare la query](./media/tutorial-hunting/save-query.png)

Creare una nuova query di ricerca:

1. Fare clic su **nuova query** e selezionare **Salva**.
2. Compilare tutti i campi vuoti e selezionare **Salva**.

   ![Nuova query](./media/tutorial-hunting/new-query.png)

Clonare e modificare una query di ricerca esistente:

1. Selezionare la query di ricerca nella tabella che si vuole modificare.
2. Selezionare i puntini di sospensione (...) nella riga della query che si desidera modificare e selezionare **clona query**.

   ![Clona query](./media/tutorial-hunting/clone-query.png)
 

3. Modificare la query e selezionare **Crea**.

   ![query personalizzata](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come eseguire un'indagine di caccia con Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Usare i notebook per eseguire campagne di caccia automatiche](notebooks.md)
- [Usare i segnalibri per salvare informazioni interessanti durante la ricerca](bookmarks.md)
