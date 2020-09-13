---
title: Panoramica e architettura di monitoraggio di Azure per le soluzioni SAP | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su monitoraggio di Azure per le soluzioni SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 8d97c713dfec574a8ef7f3f0dde4701ddea0e98b
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669016"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Monitoraggio di Azure per soluzioni SAP (anteprima)

## <a name="overview"></a>Panoramica

Monitoraggio di Azure per le soluzioni SAP è un prodotto di monitoraggio nativo di Azure per i clienti che esegue i loro paesaggi SAP in Azure. Il prodotto è compatibile con [SAP in macchine virtuali di Azure](./hana-get-started.md) e [SAP in istanze large di Azure](./hana-overview-architecture.md).
Con monitoraggio di Azure per le soluzioni SAP, i clienti possono raccogliere dati di telemetria dall'infrastruttura e dai database di Azure in una posizione centrale e correlare visivamente i dati di telemetria per una risoluzione dei problemi più rapida.

Il monitoraggio di Azure per le soluzioni SAP è disponibile tramite Azure Marketplace. Offre un'esperienza di installazione semplice e intuitiva e richiede solo pochi clic per distribuire la risorsa per monitoraggio di Azure per le soluzioni SAP, nota come **risorsa di monitoraggio SAP**.

I clienti possono monitorare diversi componenti di un panorama applicativo SAP, ad esempio macchine virtuali di Azure, cluster a disponibilità elevata, SAP HANA database e così via, aggiungendo il **provider** corrispondente per quel componente.

Infrastruttura supportata:

- Macchina virtuale di Azure
- Istanza large di Azure

Database supportati:
- Database SAP HANA
- Microsoft SQL Server

Monitoraggio di Azure per le soluzioni SAP sfrutta la potenza delle funzionalità di [monitoraggio di Azure](../../../azure-monitor/overview.md) esistenti, ad esempio log Analytics e [cartelle di lavoro](../../../azure-monitor/platform/workbooks-overview.md) per fornire funzionalità di monitoraggio aggiuntive. I clienti possono creare [visualizzazioni personalizzate](../../../azure-monitor/platform/workbooks-overview.md#getting-started) modificando le cartelle di lavoro predefinite fornite da monitoraggio di Azure per le soluzioni SAP, scrivere [query personalizzate](../../../azure-monitor/log-query/get-started-portal.md) e creare [avvisi personalizzati](../../../azure-monitor/learn/tutorial-response.md) usando l'area di lavoro di Azure log Analytics, sfruttare il [periodo di conservazione flessibile](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) e connettere i dati di monitoraggio con il proprio sistema di ticket.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Quali dati vengono raccolti da monitoraggio di Azure per le soluzioni SAP?

La raccolta dei dati in monitoraggio di Azure per le soluzioni SAP dipende dai provider configurati dai clienti. Durante l'anteprima pubblica, vengono raccolti i dati seguenti.

Telemetria del cluster pacemaker a disponibilità elevata:
- Stato del dispositivo node, Resource e SBD
- Vincoli di posizione pacemaker
- Voti del quorum e stato dell'anello
- [Altro](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

Telemetria SAP HANA:
- Utilizzo della CPU, della memoria, del disco e della rete
- Replica di sistema HANA (HSR)
- Backup HANA
- Stato dell'host HANA
- Ruoli del server di indicizzazione e del server dei nomi

Telemetria di Microsoft SQL Server:
- CPU, memoria, utilizzo dischi
- Nome host, nome istanza SQL, ID sistema SAP
- Richieste batch, compilazioni e permanenza presunta delle pagine nel tempo
- Prime 10 istruzioni SQL più dispendiose nel tempo
- Top 12 tabella più grande nel sistema SAP
- Problemi registrati nei log degli errori di SQL Server
- Processi di blocco e statistiche di attesa SQL nel tempo

## <a name="data-sharing-with-microsoft"></a>Condivisione dei dati con Microsoft

Monitoraggio di Azure per le soluzioni SAP raccoglie i metadati di sistema per offrire un supporto migliorato per i clienti SAP in Azure. Non vengono raccolte informazioni personali/informazioni personali.
I clienti possono abilitare la condivisione dei dati con Microsoft al momento della creazione di monitoraggio di Azure per la risorsa soluzioni SAP scegliendo *Condividi* dall'elenco a discesa.
È consigliabile che i clienti consentano la condivisione dei dati, in quanto fornisce ai team di supporto tecnico Microsoft altre informazioni sull'ambiente dei clienti e offre un supporto migliorato per i clienti SAP cruciali per i clienti di Azure.

## <a name="architecture-overview"></a>Panoramica dell'architettura

A livello generale, il diagramma seguente illustra in che modo monitoraggio di Azure per le soluzioni SAP raccoglie i dati di telemetria dal database SAP HANA. L'architettura è indipendente dal fatto che SAP HANA sia distribuito in macchine virtuali di Azure o in istanze di grandi dimensioni di Azure.

![Architettura di monitoraggio di Azure per soluzioni SAP](./media/azure-monitor-sap/azure-monitor-architecture.png)

I componenti principali dell'architettura sono:
- Portale di Azure: il punto di partenza per i clienti. I clienti possono passare al Marketplace all'interno portale di Azure e individuare monitoraggio di Azure per le soluzioni SAP
- Monitoraggio di Azure per la risorsa soluzioni SAP: un posto di destinazione per i clienti per visualizzare i dati di telemetria del monitoraggio
- Gruppo di risorse gestite: distribuito automaticamente come parte del monitoraggio di Azure per la distribuzione delle risorse di soluzioni SAP. Le risorse distribuite nel gruppo di risorse gestite contribuiscono alla raccolta dei dati di telemetria. Le risorse chiave distribuite e il loro scopo sono:
   - Macchina virtuale di Azure: nota anche come macchina virtuale dell' *agente di raccolta*. Si tratta di una macchina virtuale Standard_B2ms. Lo scopo principale di questa macchina virtuale è ospitare il *payload di monitoraggio*. Il monitoraggio del payload si riferisce alla logica di raccolta dei dati di telemetria dai sistemi di origine e al trasferimento dei dati raccolti al Framework di monitoraggio. Nel diagramma precedente il payload di monitoraggio contiene la logica per connettersi al database SAP HANA sulla porta SQL.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): questa risorsa viene distribuita per contenere in modo sicuro SAP Hana le credenziali del database e per archiviare le informazioni sui [provider](./azure-monitor-providers.md).
   - Area di lavoro Log Analytics: destinazione in cui risiedono i dati di telemetria.
      - La visualizzazione è basata sui dati di telemetria in Log Analytics usando le [cartelle di lavoro di Azure](../../../azure-monitor/platform/workbooks-overview.md). I clienti possono personalizzare la visualizzazione. I clienti possono anche aggiungere le proprie cartelle di lavoro o una visualizzazione specifica all'interno delle cartelle di lavoro nel dashboard di Azure per la funzionalità di aggiornamento automatico con una granularità minima di 30 minuti.
      - I clienti possono usare l'area di lavoro esistente nella stessa sottoscrizione della risorsa di monitoraggio SAP scegliendo questa opzione al momento della distribuzione.
      - I clienti possono utilizzare kusto Query Language (KQL) per eseguire [query](../../../azure-monitor/log-query/log-query-overview.md) sulle tabelle non elaborate all'interno log Analytics area di lavoro. Esaminare i *log personalizzati*.

> [!Note]
> I clienti sono responsabili dell'applicazione di patch e della gestione della macchina virtuale, distribuita nel gruppo di risorse gestite.

> [!Tip]
> I clienti possono scegliere di usare un'area di lavoro Log Analytics esistente per la raccolta di dati di telemetria, se viene distribuita nella stessa sottoscrizione di Azure della risorsa per monitoraggio di Azure per le soluzioni SAP.

### <a name="architecture-highlights"></a>Caratteristiche principali dell'architettura

Di seguito sono riportate le principali caratteristiche dell'architettura:
 - **Multi-instance** : i clienti possono creare un monitoraggio per più istanze di un determinato tipo di componente (ad esempio, il database Hana, il cluster a disponibilità elevata, Microsoft SQL Server) tra più SID SAP all'interno di una VNET con una singola risorsa di monitoraggio di Azure per le soluzioni SAP.
 - **MultiProvider** : il diagramma dell'architettura precedente Mostra il provider di SAP Hana come esempio. Analogamente, i clienti possono configurare provider aggiuntivi per i componenti corrispondenti (ad esempio, il database HANA, il cluster a disponibilità elevata, Microsoft SQL Server) per raccogliere dati da tali componenti.
 - **Open Source** : il codice sorgente di monitoraggio di Azure per le soluzioni SAP è disponibile in [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). I clienti possono fare riferimento al codice del provider e ottenere altre informazioni sul prodotto, contribuire o condividere commenti e suggerimenti.
 - **Extensible query Framework** : le query SQL per raccogliere i dati di telemetria vengono scritte in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). È possibile aggiungere facilmente ulteriori query SQL per la raccolta di dati di telemetria. I clienti possono richiedere l'aggiunta di dati di telemetria specifici a monitoraggio di Azure per le soluzioni SAP, lasciando commenti e suggerimenti tramite il collegamento alla fine di questo documento o contattando il team di account.

## <a name="pricing"></a>Prezzi
Monitoraggio di Azure per le soluzioni SAP è un prodotto gratuito (nessuna tariffa di licenza). I clienti hanno la responsabilità di pagare il costo per i componenti sottostanti nel gruppo di risorse gestite.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui provider e creare la prima risorsa di monitoraggio di Azure per soluzioni SAP.
 - Altre informazioni sui [provider](./azure-monitor-providers.md)
 - [Distribuire monitoraggio di Azure per le soluzioni SAP con Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Hai domande su monitoraggio di Azure per le soluzioni SAP? Vedere la sezione [domande frequenti](./azure-monitor-faq.md)
