---
title: "Comprendere il livello di compatibilità per i processi Analitica di flusso di Azure. | Microsoft Docs"
description: "Informazioni su come impostare un livello di compatibilità per un processo Analitica di flusso di Azure e le modifiche principali nel livello di compatibilità più recente"
keywords: "Livello di compatibilità, flusso di dati"
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
ms.openlocfilehash: 70aa82f5d8a909121c42742bb5261d6c9b8570de
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi Analitica di flusso di Azure
 
Livello di compatibilità fa riferimento ai comportamenti specifici di rilascio di un servizio di Azure flusso Analitica. Analitica di flusso di Azure è un servizio gestito, con funzionalità regolari aggiornamenti e miglioramenti delle prestazioni. In genere gli aggiornamenti sono automaticamente disponibili agli utenti finali. Tuttavia, alcune nuove funzionalità potrebbe introdurre modifiche principali di questo tipo di modifiche nel comportamento di un processo esistente, modificare i processi che utilizzano i dati da questi processi e così via. Un livello di compatibilità viene utilizzato per rappresentare una modifica importante introdotta in flusso Analitica. Le modifiche principali vengono sempre introdotti con un nuovo livello di compatibilità. 

Livello di compatibilità garantisce che i processi esistenti eseguito senza alcun errore. Quando si crea un nuovo processo di flusso Analitica, è consigliabile crearlo utilizzando il livello di compatibilità più recente disponibile per l'utente. 
 
## <a name="set-a-compatibility-level"></a>Impostare un livello di compatibilità 

Livello di compatibilità controlla il comportamento di runtime di un processo di flusso analitica. È possibile impostare il livello di compatibilità per un processo di flusso Analitica tramite portale o tramite il [creare chiamata all'API REST processo](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job). Analitica di flusso di Azure attualmente supporta due compatibilità livelli "1.0" e "1.1". Per impostazione predefinita, il livello di compatibilità è impostato su "1.0", che è stata introdotta durante la disponibilità generale di Analitica di flusso di Azure. Per aggiornare il valore predefinito, passare al processo di flusso Analitica esistente > selezionare il **livello di compatibilità** opzione **configura** sezione e modificare il valore. 

Assicurarsi arrestare il processo prima di aggiornare il livello di compatibilità. Non è possibile aggiornare il livello di compatibilità del processo è in esecuzione. 

![Livello di compatibilità nel portale](media\stream-analytics-compatibility-level/image1.png)

 
Quando si aggiorna il livello di compatibilità, il compilatore di T-SQL convalida il processo con la sintassi che corrisponde al livello di compatibilità selezionato. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Principali modifiche introdotte per il livello di compatibilità più recente (1.1)

Livello di compatibilità 1.1, vengono introdotte le seguenti modifiche principali:

* **Formato XML di Bus di servizio**  

  * **le versioni precedenti:** Analitica di flusso di Azure usato DataContractSerializer, pertanto il contenuto del messaggio inclusi tag XML. Ad esempio: 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{"SensorId": "1", "temperatura": 64\}\u0001 

  * **versione corrente:** il contenuto del messaggio contiene il flusso direttamente con alcun tag aggiuntivi. Ad esempio: 
  
   {"SensorId": "1", "temperatura": 64} 
 
* **Persistenza distinzione maiuscole/minuscole per nomi di campo**  

  * **le versioni precedenti:** i nomi dei campi sono stati modificati in lettere minuscole quando elaborato dal motore di Analitica di flusso di Azure. 

  * **versione corrente:** distinzione maiuscole/minuscole viene mantenuta per i nomi dei campi quando vengono elaborati dal motore di Analitica di flusso di Azure. 
 
* **FloatNaNDeserializationDisabled**  

  * **le versioni precedenti:** comando CREATE TABLE non filtra gli eventi con NaN (Not a Number. Ad esempio, Infinity, - infinito) in una colonna di tipo FLOAT tipo perché sono raggiungibili documentate per i numeri.

  * **versione corrente:** CREATE TABLE consente di specificare uno schema sicuro. Il motore flusso Analitica convalida che i dati sono conformi a questo schema. Con questo modello, il comando può filtrare gli eventi con i valori NaN. 

* **Disabilitare l'upcast automatica per le stringhe di data/ora in JSON.**  

  * **le versioni precedenti:** parser JSON il sarebbe upcast automaticamente i valori con le informazioni di data/ora/fuso per DateTime tipo string e convertono in formato UTC. Questo ha comportato perdere le informazioni del fuso orario.

  * **versione corrente:** vi è nessun automaticamente upcast di valori di stringa con le informazioni di data/ora/fuso al tipo DateTime. Di conseguenza, informazioni sul fuso orario viene mantenute. 

## <a name="next-steps"></a>Passaggi successivi
* [Guida alla risoluzione dei problemi per Analitica di flusso di Azure](stream-analytics-troubleshooting-guide.md)
* [Pannello integrità risorsa Analitica di flusso](stream-analytics-resource-health.md)