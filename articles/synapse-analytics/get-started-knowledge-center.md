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
ms.date: 11/16/2020
ms.openlocfilehash: 0314f5e55a476cd1cd1f8a31bd918e0ebb64c75f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254239"
---
# <a name="explore-the-synapse-knowledge-center"></a>Esplorare il Knowledge Center di Synapse

Questa esercitazione descrive come usare il Knowledge Center di Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Accesso al Knowledge Center

È possibile trovare il Knowledge Center in Synapse Studio in due modi:

  1. Nell'hub Home, nell'angolo in alto a destra della pagina, fare clic su **Learn**.
  2. Nella barra dei menu in alto fare clic su **Guida** e quindi su **Knowledge Center**.

Scegliere uno dei due metodi e aprire il **Knowledge Center**.

## <a name="overview"></a>Panoramica

Il **Knowledge Center** consente di eseguire tre operazioni:
* **Use samples immediately** (Usa immediatamente gli esempi). Per un esempio rapido del funzionamento di Synapse, scegliere questa opzione.
* **Esplora raccolta**. Questa opzione consente di collegare i set di dati di esempio e di aggiungere il codice di esempio sotto forma di script SQL, notebook e pipeline.
* **Tour Synapse studio** (Esplora Synapse Studio). Questa opzione consente di esplorare rapidamente gli elementi di base di Synapse Studio. È utile se non si è mai usato Synapse Studio prima.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Esplorare l'archivio BLOB con il pool SQL serverless

1. Nel **Knowledge Center** fare clic su **Use samples immediately** (Usa immediatamente gli esempi)
1. Selezionare **Query data with SQL** (Esegui query sui dati con SQL) 
1. Fare clic su **Use samples immediately** (Usa immediatamente gli esempi)
1. Verrà creato un nuovo script di SQL.
1. Scorrere fino alla prima query (righe da 28 a 32) e selezionare il testo della query
1. Fare clic su Esegui. Il testo selezionato verrà eseguito.

## <a name="loading-more-nyc-taxi-data"></a>Caricamento di altri dati di NYC Taxi
1. Passare a **Centro informazioni** e fare clic su **Esplora raccolta** 
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
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
