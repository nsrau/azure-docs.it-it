---
title: Panoramica di Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Informazioni su Azure Stack Edge Pro, una soluzione di archiviazione che usa un dispositivo fisico per i trasferimenti basati su rete in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 7030030699668b3d316743955dabfb2cc175f6e1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90893884"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>Che cos'è Azure Stack Edge Pro con FPGA?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

Azure Stack Edge Pro con FPGA è un dispositivo di edge computing abilitato per l'intelligenza artificiale con funzionalità di trasferimento dei dati in rete. Questo articolo fornisce una panoramica della soluzione Azure Stack Edge Pro con FPGA, con i vantaggi, le principali funzionalità e gli scenari in cui è possibile distribuire questo dispositivo.

Azure Stack Edge Pro con FPGA è una soluzione Hardware-as-a-Service. Microsoft offre un dispositivo gestito dal cloud con un FPGA (Field Programmable Gate Array) incorporato che consente l'inferenza per intelligenza artificiale accelerata e offre tutte le funzionalità di un gateway di archiviazione di rete. 

## <a name="use-cases"></a>Casi d'uso

Di seguito vengono indicati i diversi scenari in cui è possibile usare Azure Stack Edge Pro per l'inferenza rapida di Machine Learning (ML) al perimetro e la pre-elaborazione dei dati prima dell'invio ad Azure.

- **Inferenza con Azure Machine Learning**: con Azure Stack Edge Pro è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Azure Stack Edge Pro, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Azure Stack Edge Pro](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati** - Trasformare i dati prima di inviarli ad Azure per creare un set di dati più utilizzabile. La pre-elaborazione può essere usata per: 

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT. 

- **Trasferire i dati tramite rete in Azure**: usare Azure Stack Edge Pro per trasferire rapidamente i dati in Azure e consentire ulteriori attività di analisi e calcolo o a scopo di archiviazione. 

## <a name="key-capabilities"></a>Funzionalità principali

In Azure Stack Edge Pro sono disponibili le funzionalità seguenti:

|Funzionalità |Descrizione  |
|---------|---------|
|Inferenza per intelligenza artificiale accelerata| Abilitata dall'FPGA incorporato.|
|Calcolo       |Consente l'analisi, l'elaborazione e il filtraggio dei dati.|
|Prestazioni elevate | Calcolo e trasferimenti di dati con prestazioni elevate.|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Gestione dal cloud     |Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.  |
|Caricamento offline     | La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli supportati     | Supporto per protocolli SMB e NFS standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia    | Supporto BitLocker per crittografare i dati in locale e proteggere tramite *https* il trasferimento dei dati nel cloud.|
|Limitazione della larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|
|ExpressRoute | Maggiore sicurezza tramite ExpressRoute. Usare la configurazione in peering se il traffico tra i dispositivi locali e gli endpoint di archiviazione cloud viaggia su ExpressRoute. Per altre informazioni, vedere la [panoramica di ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componenti

La soluzione Azure Stack Edge Pro è costituita dalla risorsa e dal dispositivo fisico Azure Stack Edge Pro, nonché da un'interfaccia utente Web locale.

* **Dispositivo fisico Azure Stack Edge Pro**: un server montato su rack 1U fornito da Microsoft che può essere configurato per inviare dati ad Azure.
    
* **Risorsa Azure Stack Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Azure Stack Edge Pro da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Azure Stack Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Per altre informazioni, vedere [Creare un ordine per il dispositivo Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **Interfaccia utente web locale di Azure Stack Edge Pro**: usare l'interfaccia utente Web locale per eseguire la diagnostica, arrestare e riavviare il dispositivo Azure Stack Edge Pro, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Per informazioni sull'uso dell'interfaccia utente basata sul Web, vedere l'articolo [Usare l'interfaccia utente basata sul Web per amministrare Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Azure Stack Edge Pro, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Aree di disponibilità**: per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). È possibile distribuire Azure Stack Edge Pro anche nel cloud di Azure per enti pubblici. Per altre informazioni, vedere [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Informazioni su Azure per enti pubblici).
    
- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per prestazioni ottimali, le aree in cui gli account archiviano i dati di Azure Stack Edge Pro devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare i [requisiti di sistema di Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).
- Ottenere informazioni sui [limiti di Azure Stack Edge Pro](azure-stack-edge-limits.md).
- Distribuire [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) nel portale di Azure.
