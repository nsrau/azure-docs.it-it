---
title: Panoramica di Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Informazioni sui dispositivi Azure Stack Edge Pro R, una soluzione di archiviazione per applicazioni militari che usa un dispositivo fisico per i trasferimenti in Azure basati su rete.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: d95c4bb0345051cc79dadca5fa7cc11932a9512a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454034"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>Che cos'è Azure Stack Edge Pro R?

Azure Stack Edge Pro R è un dispositivo di edge computing resistente progettato per l'uso in ambienti difficili. Azure Stack Edge Pro R è una soluzione di hardware come servizio. Microsoft fornisce un dispositivo gestito dal cloud che funge da gateway di archiviazione di rete e include una GPU (Graphical Processing Unit) incorporata che abilita l'inferenza per intelligenza artificiale accelerata.

Questo articolo contiene una panoramica della soluzione Azure Stack Edge Pro R, con le principali funzionalità e gli scenari in cui è possibile distribuire questo dispositivo.


## <a name="key-capabilities"></a>Funzionalità principali

Azure Stack Edge Pro R include le funzionalità seguenti:

|Funzionalità |Descrizione  |
|---------|---------|
|Hardware resistente| Hardware resistente di classe server progettato per ambienti rigidi. Dispositivo contenuto in una custodia per il trasporto. |
|Gestione dal cloud     |Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.|
|Carichi di lavoro per l'edge computing   |Consente l'analisi, l'elaborazione e il filtraggio dei dati. Supporta le macchine virtuali e i carichi di lavoro in contenitori.|
|Inferenza per intelligenza artificiale accelerata| Abilitata da una GPU Nvidia T4.|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Modalità disconnessa| Il dispositivo e il servizio possono essere gestiti facoltativamente tramite l'hub di Azure Stack. È possibile distribuire, eseguire e gestire le applicazioni in modalità offline. <br> La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli FTP supportati     |Supporto per protocolli SMB, NFS e REST standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere [Requisiti di sistema di Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia doppia    | L'uso di unità con crittografia automatica offre il primo livello di crittografia. La VPN fornisce il secondo livello di crittografia. Supporto di BitLocker per crittografare i dati in locale e proteggerne il trasferimento nel cloud tramite *HTTPS*.|
|Limitazione larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Casi d'uso

Ecco i vari scenari in cui è possibile usare Azure Stack Edge Pro R per l'inferenza rapida di Machine Learning (ML) al perimetro e la pre-elaborazione dei dati prima dell'invio ad Azure.

- **Inferenza con Azure Machine Learning**: con Azure Stack Edge Pro R è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Azure Stack Edge Pro R, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Azure Stack Edge Pro R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati** - Trasformare i dati prima di inviarli ad Azure per creare un set di dati più utilizzabile. La pre-elaborazione può essere usata per:

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT.

- **Trasferire i dati tramite rete in Azure**: usare Azure Stack Edge Pro R per trasferire rapidamente i dati in Azure e consentire ulteriori attività di analisi e calcolo o a scopo di archiviazione.

## <a name="components"></a>Componenti

La soluzione Azure Stack Edge Pro R è costituita da una risorsa Azure Stack Edge, dal dispositivo fisico resistente Azure Stack Edge Pro R e da un'interfaccia utente Web locale.

- **Dispositivo fisico Azure Stack Edge Pro R**: dispositivo di calcolo e archiviazione a 1 nodo in una resistente custodia per il trasporto. È disponibile anche un gruppo di continuità opzionale.

    ![Dispositivo Azure Stack Edge Pro R a 1 nodo](media/azure-stack-edge-j-series-overview/device-image-1.png)

- **Risorsa Azure Stack Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Azure Stack Edge Pro R resistente da un'interfaccia Web accessibile da aree geografiche diverse. Usare la risorsa Azure Stack Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

- **Interfaccia utente Web locale di Azure Stack Edge Pro R**: interfaccia utente locale basata su browser nel dispositivo Azure Stack Edge Pro R destinata principalmente alla configurazione iniziale del dispositivo. Usare l'interfaccia utente Web locale anche per eseguire la diagnostica, arrestare e riavviare il dispositivo Azure Stack Edge Pro, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.


## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Azure Stack Edge Pro R, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Aree di disponibilità**: per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Disponibilità del dispositivo**: per un elenco di tutti i paesi in cui il dispositivo Azure Stack Edge Pro R è disponibile, passare alla sezione **Disponibilità** nella scheda **Azure Stack Edge Pro R** in [Prezzi di Azure Stack Edge Pro R](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per prestazioni ottimali, le aree in cui gli account archiviano i dati di Azure Stack Edge Pro R devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i [requisiti di sistema di Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->