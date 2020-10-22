---
title: 'Esercitazione: Iniziare a esplorare il Knowledge Center di Synapse'
description: Questa esercitazione descrive come usare il Knowledge Center di Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: dd055eecaef00a2f3b555034f44f3c04684a1722
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171863"
---
# <a name="explore-the-synapse-knowledge-center"></a>Esplorare il Knowledge Center di Synapse

Questa esercitazione descrive come usare il Knowledge Center di Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Accesso al Knowledge Center

È possibile trovare il Knowledge Center in Synapse Studio in due modi:

  1. Nell'hub Home, in Collegamenti utili fare clic sul primo collegamento denominato **Knowledge Center**.
  2. Nella barra dei menu in alto fare clic su **Guida** e quindi su **Knowledge Center**.

Scegliere uno dei due metodi e aprire il **Knowledge Center**.

## <a name="overview"></a>Panoramica

Nel **Knowledge Center** sono disponibili tre opzioni:
* **Use samples immediately** (Usa immediatamente gli esempi). Questa opzione è ideale per visualizzare l'esecuzione dell'analisi il più rapidamente possibile. Per un esempio rapido del funzionamento di Synapse, scegliere questa opzione.
* **Browse available samples** (Sfoglia gli esempi disponibili). Questa opzione consente di collegare i set di dati di esempio e di aggiungere il codice di esempio sotto forma di script SQL, notebook e pipeline.
* **Tour Synapse studio** (Esplora Synapse Studio). Questa opzione consente di esplorare rapidamente gli elementi di base di Synapse Studio. È utile se non si è mai usato Synapse Studio prima.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>Esplorazione dell'archivio BLOB con SQL su richiesta

1. Nel **Knowledge Center** fare clic su **Use samples immediately** (Usa immediatamente gli esempi)
1. Selezionare **Query data with SQL** (Esegui query sui dati con SQL) 
1. Fare clic su **Use samples immediately** (Usa immediatamente gli esempi)
1. Verrà creato un nuovo script di SQL.
1. Scorrere fino alla prima query (righe da 28 a 32) e selezionare il testo della query
1. Fare clic su Esegui. Il testo selezionato verrà eseguito.

## <a name="loading-more-nyc-taxi-data"></a>Caricamento di altri dati di NYC Taxi
1. Nel **Knowledge Center** fare clic su **Browse available samples** (Sfoglia gli esempi disponibili) 
1. Selezionare la scheda **Script SQL** nella parte superiore
1. Selezionare **Load the New York Taxicab dataset** (Carica il set di dati relativi ai taxi di New York)
1. In **Input** scegliere **Seleziona un pool esistente** e selezionare **SQLDB1**
1. Fare clic su **Apri script**
1. Verrà visualizzato un nuovo script SQL.
1. Fare clic su **Run**
1. Verranno create diverse tabelle per tutti i dati di NYC Taxi che verranno caricati usando il comando T-SQL COPY.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare SQL su richiesta](quickstart-sql-on-demand.md)
