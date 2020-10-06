---
title: Panoramica di Microsoft Azure Stack Edge Pro con GPU | Microsoft Docs
description: Descrive Azure Stack Edge Pro con GPU, una soluzione di archiviazione che usa un dispositivo fisico per i trasferimenti basati su rete in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/23/2020
ms.author: alkohli
ms.openlocfilehash: 34dc0717b13cb38c4477cc93f23d57d0a8023dae
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320745"
---
# <a name="what-is-azure-stack-edge-pro-with-gpu"></a>Che cos'è Azure Stack Edge Pro con GPU?

Azure Stack Edge Pro con GPU è un dispositivo di edge computing abilitato per l'intelligenza artificiale con funzionalità di trasferimento dei dati di rete. Questo articolo fornisce una panoramica della soluzione Azure Stack Edge Pro, nonché dei vantaggi, delle funzionalità chiave e degli scenari in cui è possibile distribuire questo dispositivo.

Azure Stack Edge Pro con GPU è una soluzione Hardware-as-a-Service. Microsoft fornisce un dispositivo gestito dal cloud che funge da gateway di archiviazione di rete e include una GPU (Graphical Processing Unit) incorporata che abilita l'inferenza per intelligenza artificiale accelerata. 

## <a name="use-cases"></a>Casi d'uso

Di seguito vengono indicati i diversi scenari in cui è possibile usare Azure Stack Edge Pro per l'inferenza rapida di Machine Learning (ML) sul perimetro e la pre-elaborazione dei dati prima dell'invio ad Azure.

- **Inferenza con Azure Machine Learning**: con Azure Stack Edge Pro è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Azure Stack Edge Pro, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Azure Stack Edge Pro](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati**: trasformare i dati prima di inviarli ad Azure tramite opzioni di calcolo, ad esempio carichi di lavoro in contenitori e macchine virtuali, per creare un set di dati su cui intervenire. La pre-elaborazione può essere usata per: 

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT. 

- **Trasferire i dati sulla rete in Azure**: usare Azure Stack Edge Pro per trasferire rapidamente e facilmente i dati in Azure per consentire ulteriori analisi ed elaborazioni o per scopi di archiviazione. 

## <a name="key-capabilities"></a>Funzionalità principali

In Azure Stack Edge Pro sono disponibili le funzionalità seguenti:

|Funzionalità |Descrizione  |
|---------|---------|
|Inferenza per intelligenza artificiale accelerata| Abilitata dalla GPU incorporata (una o due a seconda del modello).|
|Edge computing      |Supporta i carichi di lavoro in contenitori e macchine virtuali per consentire l'analisi, l'elaborazione e il filtraggio dei dati. |
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Gestione dal cloud     |Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.  |
|Caricamento offline     | La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli FTP supportati      | Supporto per protocolli SMB, NFS e REST standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia    | Supporto BitLocker per crittografare i dati in locale e proteggere tramite *https* il trasferimento dei dati nel cloud.|
|Limitazione larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|
<!--|ExpressRoute | Maggiore sicurezza tramite ExpressRoute. Usare la configurazione in peering se il traffico tra i dispositivi locali e gli endpoint di archiviazione cloud viaggia su ExpressRoute. Per altre informazioni, vedere la [panoramica di ExpressRoute](../expressroute/expressroute-introduction.md).-->

## <a name="components"></a>Componenti

La soluzione Azure Stack Edge Pro è costituita dalla risorsa e dal dispositivo fisico Azure Stack Edge Pro, nonché da un'interfaccia utente Web locale.

* **Dispositivo fisico Azure Stack Edge Pro**: un server montato su rack 1U fornito da Microsoft che può essere configurato per inviare dati ad Azure.
    
* **Risorsa Azure Stack Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Azure Stack Edge Pro da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Azure Stack Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

    Per altre informazioni, vedere [Creare un ordine per il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

* **Interfaccia utente Web locale di Azure Stack Edge Pro**: un'interfaccia utente locale basata su browser nel dispositivo Azure Stack Edge Pro destinata principalmente alla configurazione iniziale del dispositivo. Usare l'interfaccia utente Web locale anche per eseguire la diagnostica, arrestare e riavviare il dispositivo Azure Stack Edge Pro, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    Per informazioni sull'uso dell'interfaccia utente basata sul Web, vedere l'articolo [Usare l'interfaccia utente basata sul Web per amministrare Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Azure Stack Edge Pro, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Disponibilità delle risorse**: per questa versione di anteprima la risorsa è disponibile nelle aree Stati Uniti orientali, Europa occidentale e Asia sud-orientale.

- **Disponibilità del dispositivo**: per un elenco di tutti i paesi in cui il dispositivo Azure Stack Edge Pro è disponibile, passare alla sezione **Disponibilità** nella scheda **Azure Stack Edge Pro** in [Prezzi di Azure Stack Edge Pro](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro).
    
- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per prestazioni ottimali, le aree in cui gli account archiviano i dati di Azure Stack Edge Pro devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i [requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-gpu-system-requirements.md).

- Ottenere informazioni sui [limiti di Azure Stack Edge Pro](azure-stack-edge-limits.md).
- Distribuire [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) nel portale di Azure.
