---
title: Destinazioni/ouput di esempio possibili con la preparazione dei dati di Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento fornisce un set di esempi di destinazioni/output di dati personalizzati con la preparazione dei dati di Microsoft Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: e85ab5d52c2265536f72fa7301c0a25fa3a112cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-destination-connections-python"></a>Esempio di connessioni di destinazione (Python) 
Prima di leggere questa appendice, leggere la [panoramica dell'estendibilità di Python](data-prep-python-extensibility-overview.md)


### <a name="write-to-excel"></a>Scrivere in Excel 


La scrittura in Excel richiede una raccolta aggiuntiva; l'aggiunta di nuove raccolte è descritta nella panoramica dell'estendibilità. `openpyxl` è la raccolta che è necessario aggiungere.

Prima della scrittura potrebbero essere necessarie alcune altre modifiche. Alcuni tipi di dati usati nella preparazione dei dati non sono supportati in alcuni formati di destinazione. Ad esempio se esistono oggetti "Error", questi non verranno serializzati correttamente in Excel. È quindi necessaria una trasformazione "Replace Error Values" (Sostituisci valori di errore) che rimuove gli errori da tutte le colonne prima di tentare la scrittura in Excel.

Presupponendo che tutto il lavoro precedente sia stato completato, la riga seguente scrive la tabella di dati in un unico foglio di un documento Excel. Aggiungere una trasformazione Write DataFlow - Script (Scrivi flusso di dati - Script) e immettere il codice seguente nella sezione di un'espressione:


#### <a name="on-windows"></a>In Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

#### <a name="on-macosos-x"></a>In macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
