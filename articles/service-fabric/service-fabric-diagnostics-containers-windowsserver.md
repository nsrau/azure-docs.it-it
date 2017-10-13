---
title: Monitoraggio e diagnostica dei contenitori di Azure Service Fabric | Microsoft Docs
description: "Informazioni su come eseguire attività di monitoraggio e diagnostica per i contenitori orchestrati in Microsoft Azure Service Fabric con la soluzione Contenitori di OMS."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/10/2017
ms.author: dekapur
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Monitoraggio dei contenitori Windows Server con OMS

## <a name="oms-containers-solution"></a>Soluzione Contenitori di OMS

Il team di Operations Management Suite (OMS) ha pubblicato una soluzione Contenitori per la diagnostica e il monitoraggio dei contenitori. Insieme alla soluzione Service Fabric, questa soluzione è uno strumento straordinario per il monitoraggio delle distribuzioni di contenitori orchestrate in Service Fabric. Ecco un semplice esempio di dashboard della soluzione:

![Dashboard OMS di base](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Questo strumento raccoglie anche diversi tipi di log su cui è possibile eseguire query nello strumento Log Analytics di OMS e che è possibile usare per visualizzare le metriche o gli eventi generati. I tipi di log raccolti sono:

1. ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
2. ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
3. ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
4. ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
5. Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host

Questo articolo illustra i passaggi necessari per configurare il monitoraggio dei contenitori per il cluster. Per altre informazioni sulla soluzione Contenitori di OMS, vedere la relativa [documentazione](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Configurare un cluster di Service Fabric

Creare un cluster usando il modello di Azure Resource Manager disponibile [qui](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample). Assicurarsi di aggiungere un nome di area di lavoro di OMS univoco. Questo modello per impostazione predefinita distribuisce un cluster nella versione di anteprima di Service Fabric (v255.255), il che significa che non può essere usato nell'ambiente di produzione e non può essere aggiornato a una versione di Service Fabric diversa. Se si decide di usare questo modello a lungo termine o in produzione, modificare la versione impostando un numero di versione stabile.

Una volta configurato il cluster, verificare di avere installato il certificato appropriato e assicurarsi di potersi connettere al cluster.

Verificare che il gruppo di risorse sia configurato correttamente passando al [portale di Azure](https://portal.azure.com/) e individuando la distribuzione. Il gruppo di risorse deve contenere tutte le risorse di Service Fabric e anche una soluzione Log Analytics, oltre che la soluzione Service Fabric.

Per la modifica di un cluster di Service Fabric esistente:
* Verificare che la soluzione di diagnostica sia abilitata (in caso contrario, abilitarla tramite l'[aggiornamento del set di scalabilità di macchine virtuali](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Aggiungere un'area di lavoro di OMS creando una soluzione di "Analisi Service Fabric" tramite Azure Marketplace
* Modificare le origini dati della soluzione Service Fabric per prelevare dati dalle tabelle di Archiviazione di Azure appropriate (impostate da WAD) nel gruppo di risorse in cui si trova il cluster
* Aggiungere l'agente come [estensione del set di scalabilità di macchine virtuali](/powershell/module/azurerm.compute/add-azurermvmssextension) tramite PowerShell o aggiornando il set di scalabilità di macchine virtuali (stesso collegamento specificato sopra, per modificare il modello di Resource Manager)

## <a name="2-deploy-a-container"></a>2. Distribuire un contenitore

Una volta che il cluster è pronto e si è verificato di potervi accedere, distribuire un contenitore. Se si sceglie di usare la versione di anteprima impostata dal modello, è possibile esaminare anche la nuova funzionalità Docker Compose di Service Fabric. Tenere presente che la prima volta che un'immagine del contenitore viene distribuita in un cluster, sono necessari alcuni minuti per scaricare l'immagine, a seconda delle dimensioni.

## <a name="3-add-the-containers-solution"></a>3. Aggiungere la soluzione Contenitori

Nel portale di Azure creare una risorsa Contenitori (nella categoria Monitoraggio e gestione) tramite Azure Marketplace. 

![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

Nel passaggio di creazione viene richiesta un'area di lavoro di OMS. Selezionare l'area creata con la distribuzione precedente. Questo passaggio aggiunge una soluzione Contenitori nell'area di lavoro di OMS e viene rilevato automaticamente dall'agente OMS distribuito dal modello. L'agente inizierà a raccogliere dati nei processi dei contenitori nel cluster e, in circa 10-15 minuti, si dovrebbe vedere la soluzione con i dati come nell'immagine del dashboard precedente.

## <a name="4-next-steps"></a>4. Passaggi successivi

OMS offre diversi strumenti utili nell'area di lavoro. Esaminare le opzioni seguenti per personalizzare la soluzione in base alle esigenze:
- Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
- Configurare l'agente OMS per prelevare contatori delle prestazioni specifici (dalla home page dell'area di lavoro > Impostazioni > Dati > Contatori delle prestazioni di Windows)
- Configurare OMS per regole di [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica