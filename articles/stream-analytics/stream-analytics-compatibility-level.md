---
title: "Comprendere il livello di compatibilità per i processi di Analisi di flusso di Azure | Microsoft Docs"
description: "Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente"
keywords: "Livello di compatibilità, dati di streaming"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: f354c39fc3b366795fe4ed8dbeeb961bb11d5420
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure
 
Il livello di compatibilità fa riferimento ai comportamenti specifici di versione di un servizio Analisi di flusso di Azure. Analisi di flusso di Azure è un servizio gestito con regolari aggiornamenti delle funzionalità e miglioramenti delle prestazioni. In genere, gli aggiornamenti vengono resi disponibili agli utenti finali in modo automatico. Alcune nuove funzionalità, tuttavia, possono introdurre modifiche sostanziali, ad esempio al comportamento di un processo esistente, ai processi che usano i dati di questi processi e così via. Un livello di compatibilità consente di rappresentare una modifica sostanziale introdotta in Analisi di flusso. Le modifiche sostanziali vengono sempre introdotte con un nuovo livello di compatibilità. 

Il livello di compatibilità garantisce che i processi esistenti vengano eseguiti senza errori. Quando si crea un nuovo processo di Analisi di flusso, è consigliabile crearlo usando il livello di compatibilità più recente attualmente disponibile. 
 
## <a name="set-a-compatibility-level"></a>Impostare un livello di compatibilità 

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. È possibile impostare il livello di compatibilità per un processo di Analisi di flusso usando il portale o la [chiamata all'API REST per la creazione di un processo](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Analisi di flusso di Azure supporta attualmente due livelli di compatibilità: "1.0" e "1.1". Per impostazione predefinita, il livello di compatibilità è impostato su "1.0", introdotto durante la disponibilità generale di Analisi di flusso di Azure. Per aggiornare il valore predefinito, passare al processo di Analisi di flusso esistente, selezionare l'opzione **Livello di compatibilità** nella sezione **Configura** e modificare il valore. 

Non dimenticare di arrestare il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione. 

![Livello di compatibilità nel portale](media\stream-analytics-compatibility-level/image1.png)

 
Quando si aggiorna il livello di compatibilità, il servizio di compilazione Transact-SQL convalida il processo con la sintassi corrispondente al livello di compatibilità selezionato. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Modifiche sostanziali nel livello di compatibilità più recente (1.1)

Nel livello di compatibilità 1.1 sono state introdotte le modifiche sostanziali seguenti:

* **Formato XML del bus di servizio**  

  * **versioni precedenti:** Analisi di flusso di Azure usava DataContractSerializer e nel contenuto del messaggio erano quindi inclusi i tag XML. Ad esempio: 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **versione corrente:** il contenuto del messaggio contiene direttamente il flusso, senza tag aggiuntivi. Ad esempio: 
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **Distinzione maiuscole/minuscole persistente nei nomi dei campi**  

  * **Versioni precedenti:** i nomi dei campi venivano modificati in lettere minuscole quando venivano elaborati dal motore di Analisi di flusso di Azure. 

  * **Versione corrente:** la distinzione maiuscole/minuscole viene mantenuta nei nomi dei campi elaborati dal motore di Analisi di flusso di Azure. 

  > [!NOTE] 
  > La distinzione tra maiuscole e minuscole persistente non è ancora disponibile per i processi di Analisi di flusso ospitati in un ambiente di dispositivo perimetrale. Se il processo è ospitato in un dispositivo perimetrale, quindi, tutti i nomi di campo vengono convertiti in caratteri minuscoli. 

* **FloatNaNDeserializationDisabled**  

  * **Versioni precedenti:** il comando CREATE TABLE non filtrava gli eventi contrassegnati con NaN (Not a Number, ad esempio: Infinity, -Infinity) in una colonna di tipo FLOAT, poiché non erano inclusi nell'intervallo documentato per questi numeri.

  * **Versione corrente:** CREATE TABLE consente di specificare uno schema sicuro. Il motore di Analisi di flusso convalida la conformità dei dati a questo schema e, con questo modello, il comando può filtrare anche eventi contrassegnati con valori NaN. 

* **Disattivazione dell'upcast automatico per le stringhe data/ora in JSON.**  

  * **Versioni precedenti:** il parser JSON eseguiva automaticamente l'upcast dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime e li convertiva in formato UTC. Questo comportava la perdita delle informazioni sul fuso orario.

  * **Versione corrente:** non viene più eseguito l'upcast automatico dei valori di stringa con le informazioni di data/ora/fuso orario su un tipo DateTime. Le informazioni sul fuso orario vengono quindi mantenute. 

## <a name="next-steps"></a>Passaggi successivi
* [Guida per la risoluzione dei problemi di Analisi di flusso di Azure](stream-analytics-troubleshooting-guide.md)
* [Pannello Integrità risorse della funzionalità di analisi di flusso](stream-analytics-resource-health.md)