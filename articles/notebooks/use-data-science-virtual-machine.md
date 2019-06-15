---
title: Usare le macchine virtuali di analisi scientifica dei dati di Azure
description: Connettersi a un Azure Data Science Virtual Machine (DSVM) per estendere la potenza di calcolo disponibile per notebook di Azure.
services: app-service
documentationcenter: ''
author: getroyer
manager: andneil
ms.assetid: 0ccc2529-e17f-4221-b7c7-9496d6a731cc
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: getroyer
ms.openlocfilehash: ab3b742d50cc141420f9bffa1961a6e170b99d2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234340"
---
# <a name="use-azure-data-science-virtual-machines"></a>Usare le macchine virtuali di analisi scientifica dei dati di Azure

Per impostazione predefinita, i progetti eseguiti **calcolo gratuito** livello, che è limitato a 4 GB di memoria e 1 GB di dati di evitare abusi. È possibile aggirare queste limitazioni usando un'altra macchina virtuale per cui è stato effettuato il provisioning in una sottoscrizione di Azure. A tale scopo, la scelta migliore è una Data Science Virtual Machine (DSVM) di Azure usando il **macchina virtuale Data Science per Linux (Ubuntu)** immagine. Questo tipo una DSVM è preconfigurata con tutto ciò che è necessario per i notebook di Azure e viene visualizzato automaticamente sul **eseguire** elenco a discesa nel notebook di Azure.

> [!Note]
> Notebook di Azure è supportata solo su create con l'immagine di Linux Ubuntu on DSVMs. I notebook non sono supportati nelle immagini di Windows 2012, Windows 2016 o CentOS di Linux.

## <a name="create-a-dsvm-instance"></a>Creare un'istanza di macchina virtuale data SCIENCE

Per creare una nuova istanza di DSVM, seguire le istruzioni in [Creare una macchina virtuale data science Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Per altre informazioni, inclusi i dettagli sui prezzi, vedere [macchine virtuali Data Science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Connettere la macchina virtuale data SCIENCE

Dopo aver creato la macchina virtuale data SCIENCE, selezionare la **eseguire** elenco a discesa nel notebook di Azure del dashboard del progetto e selezionare l'istanza di macchina virtuale data SCIENCE appropriata. L'elenco di riepilogo a discesa Mostra le istanze di macchina virtuale data SCIENCE se vengono soddisfatte le condizioni seguenti:

- Si è connessi ad Azure Notebooks con un account che usa Azure Active Directory (AAD), ad esempio un account aziendale.
- L'account è connesso a una sottoscrizione di Azure.
- Hai una o più macchine virtuali in tale sottoscrizione, con almeno accesso in lettura, che usa la macchina virtuale di Data Science per l'immagine Linux (Ubuntu).)

![Istanze di Data Science Virtual Machine nell'elenco a discesa nel dashboard del progetto](media/project-compute-tier-dsvm.png)

Quando si seleziona un'istanza di DSVM, Azure Notebooks può richiedere le credenziali specifiche del computer usate durante la creazione della macchina virtuale.

Se una delle condizioni non vengono soddisfatti, è comunque possibile connettersi a DSVM. Nell'elenco a discesa, selezionare la **calcolo diretta** opzione che richiede un nome (da visualizzare nell'elenco), indirizzo IP della macchina virtuale e porta (in genere 8000, la porta predefinita da cui è in ascolto JupyterHub) e le credenziali della macchina virtuale:

![Richiesta di raccolta informazioni sul server per l'opzione di calcolo diretto](media/project-compute-tier-direct.png)

Ottenere questi valori dalla pagina di macchina virtuale data SCIENCE nel portale di Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>L'accesso ai file di Azure Notebooks da DSVM

Per mantenere la parità dei percorsi di file con il **calcolo gratuito** livello, si è in grado di aprire solo un unico progetto contemporaneamente in una macchina virtuale data SCIENCE. Per aprire un nuovo progetto, è necessario chiudere il progetto aperto prima di tutto.

![Pulsante di arresto nel notebook di Azure](media/shutdown.png)

Quando un progetto viene eseguito in una macchina virtuale, i file vengono montati nella directory radice del server di Jupyter (la directory mostrato in JupyterHub), sostituire i file di Azure Notebooks predefinito. Quando si arresta la macchina virtuale usando il **arresto** pulsante del notebook dell'interfaccia utente, i notebook di Azure consente di ripristinare i file predefiniti.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su DSVMs presso [Introduzione a Data Science macchine virtuali di Azure](/azure/machine-learning/data-science-virtual-machine/overview).
