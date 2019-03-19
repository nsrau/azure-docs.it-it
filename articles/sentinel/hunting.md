---
title: Funzionalità di ricerca di Azure Sentinel Preview | Microsoft Docs
description: Questo articolo descrive come usare le funzionalità di ricerca Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: adedc8bc1f574ae089f2a11033fab4f390c57a9a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087024"
---
# <a name="hunt-for-threats-with-in-azure-sentinel-preview"></a>Risposta per le minacce con nell'anteprima di Azure Sentinel

> [!IMPORTANT]
> Sentinel Azure è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se sei un responsabile dell'indagine che vogliono essere proattivi alla ricerca di minacce alla sicurezza, Azure Sentinel strumenti ricerca e query di ricerca potente cercare minacce per la sicurezza tra le origini dati dell'organizzazione. Ma i sistemi e dispositivi di sicurezza generano montagne di dati che possono essere difficili da analizzare e filtrare in eventi significativi. Sicurezza per gli analisti cercare in modo proattivo nuove anomalie che non venivano rilevate dall'App per la sicurezza, Azure Sentinel' le query di ricerca incorporate consentono a porre le domande corrette per individuare problemi nei dati è già nella rete. 

Ad esempio, una query predefinita fornisce i dati sui processi non più comuni eseguite sulla tua infrastruttura: è preferibile un avviso per ogni volta che vengono eseguiti, possono essere interamente inoffensivo, ma è possibile esaminare la query in alcuni casi per verificare se th ere's nulla di insolito. 



Con ricerca di Azure Sentinel, è possibile sfruttare le funzionalità seguenti:

- Query predefinite: Per iniziare a usare, una pagina inizia vengono forniti esempi di query precaricati pensati per iniziare avviata e acquisire familiarità con le tabelle e il linguaggio di query. Queste query di ricerca predefinite sono sviluppate da ricercatori Microsoft sulla sicurezza in modo continuo, aggiunta di nuove query, e ottimizzazione esistente esegue una query per fornire un punto di ingresso per cercare i rilevamenti nuovi e capire da dove iniziare la ricerca per il degli albori dei nuovi attacchi. 

- Linguaggio di query avanzato con IntelliSense: Basato su un linguaggio di query che ti offre la flessibilità che necessaria per rendere la caccia a un livello superiore.

- Creare i propri segnalibri: Durante il processo di ricerca, è possibile imbattersi in corrispondenze o i risultati, i dashboard o le attività con un aspetto sospetta o insoliti oppure elementi. Per contrassegnare gli elementi in modo che è possibile tornare ad essi in futuro, usare la funzionalità di segnalibro. I segnalibri consentono di salvare gli elementi per un momento successivo, per essere utilizzato per creare un caso per l'analisi. Per altre informazioni sui segnalibri, vedere usare [i segnalibri in caccia].

- Usare i notebook per automatizzare l'analisi: I notebook sono simili a Playbook dettagliate che è possibile compilare per eseguire la procedura di un'indagine e di risposta.  I notebook incapsulano tutti i passaggi di ricerca in un playbook riutilizzabile che possono essere condivisi con altri utenti nell'organizzazione. 
- Eseguire query sui dati archiviati: I dati sono accessibili nelle tabelle per poter eseguire query. Ad esempio, è possibile eseguire una query la creazione del processo, gli eventi di DNS e molti altri tipi di eventi.

- Collegamenti alla community: Sfruttare le potenzialità della community di maggiore sono state trovate origini dati e query aggiuntive.
 
## <a name="get-started-hunting"></a>Introduzione a ricerca di lavoro

1. Nel portale di Azure Sentinel, fare clic su **caccia**.
  ![Azure Sentinel Inizia ricerca](media/tutorial-hunting/hunting-start.png)

2. Quando si apre la **caccia** pagina, tutte le query di ricerca vengono visualizzate in una singola tabella. La tabella elenca tutte le query scritte dal team di Microsoft di analisti della sicurezza, nonché eventuali query aggiuntive è stato creato o modificato. Ogni query viene fornita una descrizione di ciò che ricerca per e dal tipo di dati cui è in esecuzione. Questi modelli sono raggruppati per le diverse tattiche: le icone nella parte destra classificare il tipo di minaccia, ad esempio l'accesso iniziale, la persistenza e l'esfiltrazione. È possibile filtrare questi modelli di query di ricerca usando uno dei campi. È possibile salvare qualsiasi query ai Preferiti. Salvando una query ai Preferiti, la query viene eseguita automaticamente ogni volta che il **caccia** accedere alla pagina. È possibile creare proprie query di ricerca o la clonazione e personalizzare un modello di query di ricerca esistente. 
 
2. Fare clic su **eseguire query** in ricerca di pagina dei dettagli di query per eseguire qualsiasi query senza uscire dalla pagina di ricerca.  Il numero di corrispondenze viene visualizzato all'interno della tabella. Esaminare l'elenco delle query di ricerca e le corrispondenze. Scopri quale fase della kill chain la corrispondenza è associata.

3. Eseguire un'analisi rapida di query sottostante nel riquadro dei dettagli query oppure fare clic su **visualizzare i risultati della query** per aprire la query in Log Analitica. Nella parte inferiore, esaminare le corrispondenze per la query.

4.  Fare clic sulla riga e selezionare **Aggiungi segnalibro** per aggiungere le righe di essere esaminato - è possibile farlo per tutto ciò che risulta sospetto. 

5. Quindi, tornare alla finestra principale **caccia** e fare clic sui **segnalibri** scheda per visualizzare tutte le attività sospette. 

6. Selezionare un segnalibro e quindi fare clic su **ricerca causa** per aprire l'esperienza di analisi. È possibile filtrare i segnalibri. Ad esempio, se si sta esaminando una campagna, è possibile creare un tag per la campagna e quindi filtrare tutti i segnalibri in base alla campagna.

1. Dopo che è stato individuato la query di ricerca fornisce informazioni approfondite di valore elevato di possibili attacchi, è possibile creare anche rilevamento personalizzata regole in base alla query e questi approfondimenti sotto forma di avvisi e i risponditori agli eventi imprevisti di sicurezza.

 

## <a name="query-language"></a>Linguaggio di query 

Ricerca di Azure Sentinel si basa sul linguaggio di query Analitica di Log di Azure. Per altre informazioni sul linguaggio di query e gli operatori supportati, vedere [riferimenti al linguaggio di Query](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Repository di GitHub pubblico caccia query

Consultare il [repository di query di ricerca](https://github.com/Azure/Orion). Contribuire e usare le query di esempio viene condivise da parte dei clienti.

 

## <a name="sample-query"></a>Query di esempio

Una query tipica inizia con un nome di tabella seguito da una serie di operatori separati dal \|.

Nell'esempio precedente, iniziare con la tabella nome SecurityEvent e aggiungere inoltrato tramite pipe gli elementi in base alle esigenze.

1. Definire un filtro temporale per visualizzare solo i record da sette giorni precedenti.

2. Aggiungere un filtro nella query per mostrare solo l'evento ID 4688.

3. Aggiungere un filtro nella query nella riga di comando per contenere solo istanze di cscript.exe.

4. Progetto solo le colonne è interessati a esplorare e limitare i risultati a 1000 e fare clic su **eseguire query**.
5. Fare clic sul triangolo verde ed eseguire la query. È possibile testare la query ed eseguire la ricerca di comportamenti anomali.

## <a name="useful-operators"></a>Operatori utili

Il linguaggio di query è potente e è disponibili molti operatori disponibili, alcune utili gli operatori sono elencati di seguito:

**in cui** -filtrare una tabella per il subset di righe che soddisfano un predicato.

**riepilogare** -produrre una tabella che aggrega il contenuto della tabella di input.

**join** -unire le righe di due tabelle in modo da formare una nuova tabella facendo corrispondere i valori delle colonne specificate da ogni tabella.

**conteggio** -restituisce il numero di record nel set di record di input.

**inizio** -restituire i primi N record ordinati in base alle colonne specificate.

**limite** -restituiscono fino al numero specificato di righe.

**progetto** : selezionare le colonne da includere, rinominare o rimuovere e inserire nuove colonne calcolate.

**estendere** : creare colonne calcolate e li aggiunge al set di risultati.

**makeset** -restituire una matrice dinamica (JSON) del set di valori distinct che accetta Expr nel gruppo

**trovare** -trovare righe che soddisfano un predicato in un set di tabelle.

## <a name="save-a-query"></a>Salvare una query

È possibile creare o modificare una query e salvarla come una query o condividerlo con gli utenti che sono nello stesso tenant.

   ![Salvare la query](./media/tutorial-hunting/save-query.png)

Creare una nuova query di ricerca:

1. Fare clic su **nuova query** e selezionare **salvare**.
2. Compilare tutti i campi vuoti e selezionare **salvare**.

   ![Nuova query](./media/tutorial-hunting/new-query.png)

Clonare e modificare una query di ricerca esistente:

1. Selezionare la query di ricerca nella tabella che si desidera modificare.
2. Selezionare i puntini di sospensione (...) nella riga della query si desidera modificare e quindi selezionare **Clona query**.

   ![Clona query](./media/tutorial-hunting/clone-query.png)
 

3. Modificare la query e selezionare **Create**.

   ![query personalizzata](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si appreso come eseguire un'analisi di ricerca con Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:


- [Usare i notebook per eseguire campagne caccia automatizzati](notebooks.md)
- [Usare i segnalibri per salvare le informazioni interessante durante la ricerca](bookmarks.md)