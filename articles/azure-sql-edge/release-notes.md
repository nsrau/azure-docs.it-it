---
title: Note sulla versione di Azure SQL Edge
description: Note sulla versione in cui sono illustrate le novità o le modifiche apportate alle immagini Edge di Azure SQL
keywords: Note sulla versione di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: d5d60db2cc0c35b908454be3f00716db6d2da945
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361643"
---
# <a name="azure-sql-edge-release-notes"></a>Note sulla versione di Azure SQL Edge 

Questo articolo descrive le novità e le modifiche apportate a ogni nuova build di Azure SQL Edge.

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>Numero di build del motore SQL: 15.0.2000.1545
### <a name="fixes"></a>Correzioni
1. Correzione della stima con i modelli ONNX per gestire il problema di CPUID in ARM 
2. Correzione per migliorare la gestione del percorso di errore all'avvio del flusso TSQL 
3. Correggere il valore errato del ritardo della filigrana nelle metriche del processo quando non sono presenti dati. 
4. Risolvere il problema con l'adattatore di output quando l'adapter dispone di uno schema variabile tra i batch.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>Numero di build del motore SQL: 15.0.2000.1401
### <a name="whats-new"></a>Novità
1.  Nome prodotto aggiornato a' Azure SQL Edge '
1.  Funzione Date_bucket

    i.  Supporto per data, ora, tipo DateTime
3.  PREVEDERE con ONNX
    
    i.  Parametro di RUNTIME necessario per ONNX 
    
4.  Supporto dello streaming TSQL (anteprima limitata) 
 
### <a name="known-issues"></a>Problemi noti

1. <b>Problema:</b> Potenziali errori con l'applicazione di dacpac all'avvio a causa di un problema di temporizzazione.

    <b>Soluzione alternativa:</b> Il riavvio di SQL Server o il contenitore tenterà di applicare il dacpac e dovrebbe risolvere il problema
### <a name="request-support"></a>Richiedi supporto
1. È possibile richiedere supporto nella [pagina del supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Verificare che siano selezionati i campi seguenti: 
    * Tipo di problema-tecnico 
    * IoT Edge di servizio
    * Tipo di problema: il problema riguarda un modulo IoT Edge
    * Sottotipo di problema-Azure SQL Edge

   ![Ticket di supporto di esempio](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Numero di build del motore SQL: 15.0.2000.1331
### <a name="whats-new"></a>Novità
1. Funzione Date_bucket
    
    i. Supporto per il tipo DateTimeOffset
2. PREVEDERE con i modelli ONNX

    i. supporto nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Numero di build del motore SQL: 15.0.2000.1247
### <a name="whats-new"></a>Novità
1.  PREVEDERE con i modelli ONNX
 
    i.  Supporto varchar
    
    ii. Migrazione a ONNX Runtime versione 1,0 
2.  Supporto delle funzionalità: sono abilitate le funzionalità seguenti:

    i.   Supporto CDC

    ii.  Tabella di cronologia con compressione

    iii. Fattore di scala più elevato per Read-ahead di log

    iv.  Distribuzione di modalità batch ES filtro

    v.   Ottimizzazioni Read-Ahead
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Numero di build del motore SQL: 15.0.2000.1147
### <a name="whats-new"></a>Novità
1. Distribuzione del portale di Azure 

    i.   Supporto per la distribuzione di immagini AMD64 e ARM

    ii.  Supporto per la creazione di processi di streaming

    iii. Distribuzione di dacpac
2. PREVEDERE con i modelli ONNX

    i. Supporto per tipi numerici
3. Supporto delle funzionalità: sono abilitate le funzionalità seguenti:

    i.  Distribuzione aggregazione all'analisi dell'archivio colonne

    ii. Analisi di buon passo
4. Lavoro di riduzione dell'utilizzo di memoria e footprint
