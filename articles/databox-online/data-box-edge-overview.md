---
title: Panoramica di Microsoft Azure Data Box Edge | Microsoft Docs
description: Descrive Azure Data Box Edge, una soluzione di archiviazione che usa un dispositivo fisico per i trasferimenti basati su rete in Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305431"
---
# <a name="what-is-azure-data-box-edge"></a>Che cos'è Azure Data Box Edge? 

Azure Data Box Edge è un dispositivo di elaborazione perimetrale abilitato per l'intelligenza artificiale con funzionalità di trasferimento dei dati di rete. Questo articolo fornisce una panoramica della soluzione Data Box Edge e dei vantaggi, delle funzionalità chiave e degli scenari in cui distribuire questo dispositivo. 

Data Box Edge è una soluzione hardware come servizio. Microsoft fornisce un dispositivo gestito dal cloud con un FPGA (Field Programmable Gate Array) incorporato che consente l'inferenza per intelligenza artificiale accelerata e offre tutte le funzionalità di un gateway di archiviazione. 

## <a name="use-cases"></a>Casi d'uso

Di seguito sono riportati i diversi scenari in cui è possibile usare Data Box Edge per l'inferenza rapida di Machine Learning (ML) sul perimetro e la pre-elaborazione dei dati prima di inviarli ad Azure.

- **Inferenza con Azure Machine Learning** - Con Data Box Edge è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Data Box Edge, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati** - Trasformare i dati prima di inviarli ad Azure per creare un set di dati più utilizzabile. La pre-elaborazione può essere usata per: 

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT. 

- **Trasferimento dei dati sulla rete in Azure**: usare Data Box Edge per trasferire rapidamente e facilmente i dati in Azure per consentire ulteriori analisi ed elaborazioni o per scopi di archiviazione. 


## <a name="key-capabilities"></a>Funzionalità principali

Data Box Edge include le funzionalità seguenti:

|Funzionalità |DESCRIZIONE  |
|---------|---------|
|Inferenza per intelligenza artificiale accelerata| Abilitata dall'FPGA incorporato.|
|Calcolo       |Consente l'analisi, l'elaborazione e il filtraggio dei dati.|
|Prestazioni elevate | Calcolo e trasferimenti di dati con prestazioni elevate.|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Gestione dal cloud     |Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.  |
|Caricamento offline     | La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli supportati     | Supporto per protocolli SMB e NFS standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Data Box Edge](data-box-edge-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia    | Supporto BitLocker per crittografare i dati in locale e proteggere tramite *https* il trasferimento dei dati nel cloud.|
|Limitazione della larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|


## <a name="components"></a>Componenti

La soluzione Data Box Edge è costituita dalla risorsa Data Box Edge, dal dispositivo fisico Data Box Edge e da un'interfaccia utente Web locale.

* **Dispositivo fisico Data Box Edge**: un server montato su rack 1U fornito da Microsoft che può essere configurato per inviare dati ad Azure. 
    
* **Risorsa Data Box Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Data Box Edge da un'interfaccia Web accessibile da posizioni geografiche diverse. Usare la risorsa Data Box Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Per altre informazioni, consultare [Create an order for your Data Box Edge device (Creare un ordine per il dispositivo Data Box Edge)](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Interfaccia utente web locale di Data Box**: usare l'interfaccia utente Web locale per eseguire la diagnostica, arrestare e riavviare il dispositivo Data Box Edge, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Per informazioni sull'uso dell'interfaccia utente basata sul Web, vedere l'articolo relativo all'[uso dell'interfaccia utente basata sul Web per amministrare Data Box](data-box-edge-manage-access-power-connectivity-mode.md).


## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Data Box Edge, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Aree di disponibilità**: per un elenco delle aree in cui la risorsa Data Box Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). È possibile distribuire Data Box Edge anche nel cloud di Azure per enti pubblici. Per altre informazioni, vedere [What is Azure Government?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) (Informazioni su Azure per enti pubblici).
    
- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per garantire prestazioni ottimali, le aree in cui gli account archiviano i dati Data Box Edge devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte. 


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Edge](data-box-edge-system-requirements.md).
- Comprendere i [limiti di Data Box Edge](data-box-edge-limits.md).
- Distribuire rapidamente [Azure Data Box Edge](data-box-edge-deploy-prep.md) nel portale di Azure.




