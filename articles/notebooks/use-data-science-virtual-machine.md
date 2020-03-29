---
title: Usare macchine virtuali di analisi scientifica dei dati di AzureUse Azure Data Science Virtual Machines
description: Informazioni su come connettersi a una macchina virtuale (DSVM) di Azure Data Science per estendere la potenza di calcolo disponibile per l'anteprima dei blocchi appunti di Azure.Learn how to connect to an Azure Data Science Virtual Machine (DSVM) to extend the compute power available to Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898413"
---
# <a name="use-azure-data-science-virtual-machines"></a>Usare macchine virtuali di analisi scientifica dei dati di AzureUse Azure Data Science Virtual Machines

Per impostazione predefinita, i progetti vengono eseguiti nel livello **Calcolo gratuito,** che è limitato a 4 GB di memoria e 1 GB di dati per evitare abusi. È possibile aggirare queste limitazioni usando un'altra macchina virtuale per cui è stato effettuato il provisioning in una sottoscrizione di Azure. A tale scopo, la scelta migliore è un'immagine DSVM (Data Science Virtual Machine) di Azure Data Science usando l'immagine **Data Science Virtual Machine for Linux (Ubuntu).** Una dSVM di questo tipo viene preconfigurata con tutto il necessario per i blocchi appunti di Azure e viene visualizzata automaticamente nell'elenco a discesa Esegui in Blocchi appunti di Azure.Such a DSVM comes pre-configured with all you need for Azure Notebooks and appears automatically on the **Run** drop-down list in Azure Notebooks.

> [!Note]
> I blocchi appunti di Azure sono supportati solo nelle DSVM create con l'immagine Ubuntu in Linux.Azure Notebooks is supported only on DSVMs created with the on Linux Ubuntu image. I blocchi appunti non sono supportati nelle immagini Windows 2012, Windows 2016 o Linux CentOS.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Creare un'istanza DSVMCreate a DSVM instance

Per creare una nuova istanza di DSVM, seguire le istruzioni in [Creare una macchina virtuale data science Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Per ulteriori informazioni sui prezzi, vedere [Macchine virtuali di data science](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Connettersi alla DSVM

Dopo aver creato dSVM, selezionare l'elenco a discesa **Esegui** nel dashboard del progetto Blocchi appunti di Azure e selezionare l'istanza DSVM appropriata. L'elenco a discesa mostra le istanze DSVM se sono vere le seguenti condizioni:

- Si è connessi ad Azure Notebooks con un account che usa Azure Active Directory (AAD), ad esempio un account aziendale.
- L'account è connesso a una sottoscrizione di Azure.
- Si dispone di una o più macchine virtuali nella sottoscrizione, con almeno l'accesso in lettura, che utilizza l'immagine Data Science Virtual Machine for Linux (Ubuntu).

![Istanze di Data Science Virtual Machine nell'elenco a discesa nel dashboard del progetto](media/project-compute-tier-dsvm.png)

Quando si seleziona un'istanza di DSVM, Azure Notebooks può richiedere le credenziali specifiche del computer usate durante la creazione della macchina virtuale.

> [!Important]
> Il nome utente deve essere in minuscolo per poterlo utilizzare con JupyterHub.

Se una delle condizioni non viene soddisfatta, è comunque possibile connettersi a DSVM. Nell'elenco a discesa selezionare l'opzione **Calcolo** diretto, che richiede un nome (da visualizzare nell'elenco), l'indirizzo IP e la porta della macchina virtuale (in genere 8000, la porta predefinita su cui JupyterHub è in ascolto) e le credenziali della macchina virtuale:

![Richiesta di raccolta informazioni sul server per l'opzione di calcolo diretto](media/project-compute-tier-direct.png)

Ottenere questi valori dalla pagina DSVM nel portale di Azure.You obtain these values from the DSVM page in the Azure portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Accesso ai file dei blocchi appunti di Azure dalla macchina virtuale DS

L'accesso al file system è supportato per DSVM versioni 19.06.15 o successive. Per verificare la versione, prima connettersi alla DSVM tramite SSH, quindi eseguire il seguente comando: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (è necessario utilizzare l'indirizzo IP esatto mostrato qui). Il numero di versione viene visualizzato nell'output per "version".

Per mantenere la parità di percorsi di file con il livello **di calcolo gratuito,** è possibile aprire un solo progetto alla volta in una dSVM. Per aprire un nuovo progetto, è necessario prima arrestare il progetto aperto.

Quando un progetto viene eseguito in una macchina virtuale, i file vengono montati nella directory radice del server Jupyter (la directory mostrata in JupyterHub), sostituendo i file predefiniti dei blocchi appunti di Azure. Quando si arresta la macchina virtuale usando il pulsante **Arresta** nell'interfaccia utente del blocco appunti, Blocchi appunti di Azure ripristina i file predefiniti.

![Pulsante Arresta nei blocchi appunti di AzureShutdown button in Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Creare nuovi utenti DSVMCreate new DSVM users

Se più utenti condividono una DSVM, è possibile evitare di bloccarsi reciprocamente creando e utilizzando un utente DSVM per ogni utente del notebook:If multiple users share a DSVM, you can avoid avoid blocking each other by creating and using a DSVM user for each notebook user:

1. Nel [portale di Azure](https://portal.azure.com)passare alla macchina virtuale.
1. In **Supporto e risoluzione dei problemi** nel margine sinistro selezionare **Reimposta password**.
1. Immettere un nuovo **nome utente**. Il nome utente deve essere in minuscolo per poterlo utilizzare con JupyterHub. Immettere una password. Quindi selezionare **Aggiorna**. (I nomi utente esistenti non sono interessati.)
1. Ripetere il passaggio precedente per tutti gli altri utenti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle DSVM, vedere [Introduzione alle macchine virtuali di analisi scientifica dei dati](/azure/machine-learning/data-science-virtual-machine/overview)di Azure.Learn more about DSVMs on Introduction to Azure Data Science Virtual Machines .
