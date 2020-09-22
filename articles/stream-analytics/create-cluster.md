---
title: 'Avvio rapido: Creare un cluster di Analisi di flusso di Azure'
description: Informazioni su come creare un cluster di Analisi di flusso di Azure.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: a7be204c30d242be991fb9a57d239b69342ace97
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944339"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Avvio rapido: Creare un cluster dedicato di Analisi di flusso di Azure tramite il portale di Azure

Usare il portale di Azure per creare un cluster di Analisi di flusso di Azure. Un [cluster di Analisi di flusso](cluster-overview.md) è una distribuzione a tenant singolo che è possibile usare per casi d'uso di streaming complessi e impegnativi. In un cluster di Analisi di flusso è possibile eseguire più processi di Analisi di flusso.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Completamento di [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Creare un cluster di Analisi di flusso

In questa sezione si crea una risorsa cluster di Analisi di flusso.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una risorsa**. Nella casella di ricerca *Cerca nel marketplace* digitare e selezionare **Cluster di Analisi di flusso**. Quindi selezionare **Aggiungi**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Risultato della ricerca del cluster di Analisi di flusso.":::

1. Nella pagina **Crea cluster di Analisi di flusso** immettere le impostazioni di base per il nuovo cluster.

   |Impostazione|Valore|Descrizione |
   |---|---|---|
   |Sottoscrizione|Nome sottoscrizione|Selezionare la sottoscrizione di Azure da usare per il cluster di Analisi di flusso. |
   |Gruppo di risorse|Nome del gruppo di risorse|Selezionare un gruppo di risorse oppure fare clic su **Crea nuovo**, quindi immettere un nome univoco per il nuovo gruppo di risorse. |
   |Cluster Name|Un nome univoco|Immettere un nome per identificare il cluster di Analisi di flusso.|
   |Location|L'area più vicina alle origini dati e ai sink|Selezionare una località geografica in cui ospitare il cluster di Analisi di flusso. Usare l'area più vicina alle origini dati e ai sink per un'analisi a bassa latenza.|
   |Capacità in unità di streaming|Da 36 a 216 |Determinare le dimensioni del cluster stimando il numero di processi di Analisi di flusso che si prevede di eseguire e il totale di unità di streaming necessarie. È possibile iniziare con 36 unità di streaming e successivamente aumentare o ridurre il numero secondo le esigenze.|

   ![Creare cluster](./media/create-cluster/create-cluster.png)

1. Selezionare **Rivedi e crea**. È possibile ignorare le sezioni **Tag**.

1. Esaminare le impostazioni del cluster e quindi selezionare **Crea**. La creazione del cluster è un'operazione a esecuzione prolungata che può richiedere circa 60 minuti. Attendere che la pagina del portale visualizzi **La distribuzione è stata completata**. Nel frattempo, è possibile creare e sviluppare i [processi di Analisi di flusso](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) da eseguire in questo cluster, se non è già stato fatto.

1. Selezionare **Vai alla risorsa** per passare alla pagina del cluster di Analisi di flusso.

## <a name="delete-your-cluster"></a>Eliminare il cluster

Se non si prevede di usarlo per eseguire processi di Analisi di flusso, è possibile eliminare il cluster di Analisi di flusso. Per eliminare il cluster, seguire questa procedura nel portale di Azure:

1. Passare a **Processi di Analisi di flusso** in **Impostazioni** e arrestare tutti i processi in esecuzione.

1. Passare alla pagina **Panoramica** del cluster. Selezionare **Elimina** e seguire le istruzioni per eliminare il cluster.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un cluster di Analisi di flusso. Passare all'articolo successivo per informazioni su come eseguire un processo di Analisi di flusso nel cluster:

> [!div class="nextstepaction"]
> [Gestire i processi di Analisi di flusso in un cluster di Analisi di flusso](manage-jobs-cluster.md)
