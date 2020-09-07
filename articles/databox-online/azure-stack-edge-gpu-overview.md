---
title: Panoramica di Microsoft Azure Stack Edge con GPU | Microsoft Docs
description: Descrive Azure Stack Edge con GPU, una soluzione di archiviazione che usa un dispositivo fisico per i trasferimenti basati su rete in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: a8e1c83573de53962b3646304389023d91ab6dd3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256243"
---
# <a name="what-is-azure-stack-edge-with-gpu-preview"></a>Che cos'è Azure Stack Edge con GPU (anteprima)?

Azure Stack Edge con GPU è un dispositivo di edge computing abilitato per l'intelligenza artificiale con funzionalità di trasferimento dei dati di rete. Questo articolo contiene una panoramica della soluzione Azure Stack Edge, nonché dei vantaggi, delle funzionalità chiave e degli scenari in cui distribuire questo dispositivo.

Azure Stack Edge con GPU è una soluzione Hardware-as-a-Service. Microsoft fornisce un dispositivo gestito dal cloud che funge da gateway di archiviazione di rete e include una GPU (Graphical Processing Unit) incorporata che abilita l'inferenza per intelligenza artificiale accelerata. 

> [!IMPORTANT]
> Azure Stack Edge con GPU è attualmente disponibile in anteprima. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="use-cases"></a>Casi d'uso

Di seguito vengono indicati i diversi scenari in cui è possibile usare Azure Stack Edge per l'inferenza rapida di Machine Learning (ML) sul perimetro e la pre-elaborazione dei dati prima di inviarli ad Azure.

- **Inferenza con Azure Machine Learning** - Con Azure Stack Edge è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Azure Stack Edge, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Azure Stack Edge](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati** - Trasformare i dati prima di inviarli ad Azure per creare un set di dati più utilizzabile. La pre-elaborazione può essere usata per: 

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT. 

- **Trasferire i dati sulla rete in Azure**: usare Azure Stack Edge per trasferire rapidamente e facilmente i dati in Azure per consentire ulteriori analisi ed elaborazioni o per scopi di archiviazione. 

## <a name="key-capabilities"></a>Funzionalità principali

In Azure Stack Edge sono disponibili le funzionalità seguenti:

|Funzionalità |Descrizione  |
|---------|---------|
|Inferenza per intelligenza artificiale accelerata| Abilitata dalla GPU incorporata (una o due a seconda del modello).|
|Edge computing      |Consente l'analisi, l'elaborazione e il filtraggio dei dati. Supporta macchine virtuali e cluster Kubernetes.|
|Prestazioni elevate | Calcolo e trasferimenti di dati con prestazioni elevate.|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Gestione dal cloud     |Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.  |
|Caricamento offline     | La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli supportati     | Supporto per protocolli SMB, NFS e REST standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Azure Stack Edge](azure-stack-edge-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia    | Supporto BitLocker per crittografare i dati in locale e proteggere tramite *https* il trasferimento dei dati nel cloud.|
|Limitazione larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|
|ExpressRoute | Maggiore sicurezza tramite ExpressRoute. Usare la configurazione in peering se il traffico tra i dispositivi locali e gli endpoint di archiviazione cloud viaggia su ExpressRoute. Per altre informazioni, vedere [Panoramica di ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componenti

La soluzione Azure Stack Edge è costituita dalla risorsa e dal dispositivo fisico Azure Stack Edge e da un'interfaccia utente Web locale.

* **Dispositivo fisico Azure Stack Edge**: un server montato su rack 1U fornito da Microsoft che può essere configurato per inviare dati ad Azure.
    
* **Risorsa Azure Stack Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Azure Stack Edge da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Azure Stack Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

    Per altre informazioni, vedere [Creare un ordine per il dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Interfaccia utente web locale di Azure Stack Edge**: usare l'interfaccia utente Web locale per eseguire la diagnostica, arrestare e riavviare il dispositivo Azure Stack Edge, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    Per informazioni sull'uso dell'interfaccia utente basata sul Web, vedere l'articolo relativo all'[uso dell'interfaccia utente basata sul Web per amministrare Azure Stack Edge](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Azure Stack Edge, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Disponibilità delle risorse**: per questa versione di anteprima la risorsa è disponibile nelle aree Stati Uniti orientali, Europa occidentale e Asia sud-orientale.
    
- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per garantire prestazioni ottimali, le aree in cui gli account archiviano i dati di Azure Stack Edge devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i [requisiti di sistema di Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md).
- Informazioni sui [limiti di Azure Stack Edge](azure-stack-edge-limits.md).
- Distribuire [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) nel portale di Azure.
