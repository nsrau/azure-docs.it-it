---
title: Usare macchine virtuali di Data Science di Azure
description: Connettersi a un Data Science Virtual Machine di Azure (DSVM) per estendere la potenza di calcolo disponibile per l'Azure Notebooks.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: article
ms.date: 06/13/2019
ms.openlocfilehash: 0559442cb3f6c3937e6930082940e9354c43bc30
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277411"
---
# <a name="use-azure-data-science-virtual-machines"></a>Usare macchine virtuali di Data Science di Azure

Per impostazione predefinita, i progetti vengono eseguiti nel livello di **calcolo gratuito** , che è limitato a 4 GB di memoria e 1 GB di dati per evitare abusi. È possibile aggirare queste limitazioni usando un'altra macchina virtuale per cui è stato effettuato il provisioning in una sottoscrizione di Azure. A questo scopo, la scelta migliore è un Data Science Virtual Machine di Azure (DSVM) che usa l'immagine **Data Science Virtual Machine per Linux (Ubuntu)** . Un DSVM di questo tipo è preconfigurato con tutti gli elementi necessari per Azure Notebooks e viene visualizzato automaticamente nell'elenco a discesa **Esegui** in Azure Notebooks.

> [!Note]
> Azure Notebooks è supportato solo in Dsvm creato con l'immagine Ubuntu in Linux. I notebook non sono supportati nelle immagini CentOS di Windows 2012, Windows 2016 o Linux.

## <a name="create-a-dsvm-instance"></a>Creare un'istanza di DSVM

Per creare una nuova istanza di DSVM, seguire le istruzioni in [Creare una macchina virtuale data science Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Per altre informazioni, inclusi i dettagli sui prezzi, vedere [macchine virtuali di Data Science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Connettere la macchina virtuale data SCIENCE

Una volta creato il DSVM, selezionare l'elenco a discesa **Esegui** nel dashboard del progetto Azure Notebooks e selezionare l'istanza di DSVM appropriata. L'elenco a discesa Mostra le istanze di DSVM se sono soddisfatte le condizioni seguenti:

- Si è connessi ad Azure Notebooks con un account che usa Azure Active Directory (AAD), ad esempio un account aziendale.
- L'account è connesso a una sottoscrizione di Azure.
- Sono presenti una o più macchine virtuali in tale sottoscrizione, con almeno l'accesso in lettura, che usa l'immagine Data Science Virtual Machine per Linux (Ubuntu).

![Istanze di Data Science Virtual Machine nell'elenco a discesa nel dashboard del progetto](media/project-compute-tier-dsvm.png)

Quando si seleziona un'istanza di DSVM, Azure Notebooks può richiedere le credenziali specifiche del computer usate durante la creazione della macchina virtuale.

Se una qualsiasi delle condizioni non viene soddisfatta, è comunque possibile connettersi a DSVM. Nell'elenco a discesa selezionare l'opzione **elaborazione diretta** , che richiede un nome (da visualizzare nell'elenco), l'indirizzo IP e la porta della macchina virtuale (in genere 8000, la porta predefinita su cui JupyterHub è in ascolto) e le credenziali della macchina virtuale:

![Richiesta di raccolta informazioni sul server per l'opzione di calcolo diretto](media/project-compute-tier-direct.png)

È possibile ottenere questi valori dalla pagina DSVM nel portale di Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Accesso ai file di Azure Notebooks da DSVM

L'accesso al file System è supportato per le versioni DSVM 19.06.15 o versioni successive. Per controllare la versione, connettersi prima di tutto a DSVM tramite SSH, quindi eseguire il comando seguente: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (è necessario usare l'indirizzo IP esatto riportato qui). Il numero di versione viene visualizzato nell'output per "Version".

Per mantenere la parità dei percorsi di file con il livello di **calcolo gratuito** , è possibile aprire un solo progetto alla volta in un DSVM. Per aprire un nuovo progetto, è necessario arrestare prima il progetto aperto.

Quando un progetto viene eseguito in una macchina virtuale, i file vengono montati nella directory radice del server Jupyter (la directory visualizzata in JupyterHub), sostituendo i file Azure Notebooks predefiniti. Quando si arresta la macchina virtuale usando il pulsante **Arresta** nell'interfaccia utente del notebook, Azure Notebooks Ripristina i file predefiniti.

![Pulsante arresta in Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Crea nuovi utenti DSVM

Se più utenti condividono un DSVM, è possibile evitare di bloccarsi reciprocamente creando e usando un utente DSVM per ogni utente del notebook:

1. Nel [portale di Azure](https://portal.azure.com)passare alla macchina virtuale.
1. In **supporto e risoluzione dei problemi** nel margine sinistro selezionare **Reimposta password**.
1. Immettere un nuovo nome utente e una nuova password e selezionare **Aggiorna**. (I nomi utente esistenti non sono interessati).
1. Ripetere il passaggio precedente per tutti gli altri utenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Dsvm, vedere [Introduzione alle macchine virtuali di Data Science di Azure](/azure/machine-learning/data-science-virtual-machine/overview).
