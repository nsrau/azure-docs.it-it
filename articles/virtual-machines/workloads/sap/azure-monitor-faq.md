---
title: Domande frequenti-monitoraggio di Azure per le soluzioni SAP | Microsoft Docs
description: In questo articolo vengono fornite le risposte alle domande frequenti su monitoraggio di Azure per le soluzioni SAP.
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
ms.openlocfilehash: f70aa684a089e0f21cb50932e212b71f77df0e01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987249"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Domande frequenti su monitoraggio di Azure per soluzioni SAP (anteprima)
## <a name="frequently-asked-questions"></a>Domande frequenti

Questo articolo fornisce le risposte alle domande frequenti su monitoraggio di Azure per le soluzioni SAP.  

 - **È necessario pagare per monitoraggio di Azure per le soluzioni SAP?**  
Non sono previsti costi di licenza per il monitoraggio di Azure per le soluzioni SAP.  
Tuttavia, i clienti sono tenuti a sostenere il costo dei componenti del gruppo di risorse gestite.  

 - **In quali aree il servizio è disponibile per l'anteprima pubblica?**  
Per la versione di anteprima pubblica, questo servizio sarà disponibile negli Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti orientali ed Europa occidentale.  

 - **È necessario fornire le autorizzazioni per consentire la distribuzione del gruppo di risorse gestite nella sottoscrizione?**  
No, non sono necessarie autorizzazioni esplicite.  

 - **Dove si trova la macchina virtuale dell'agente di raccolta?**  
Al momento della distribuzione di monitoraggio di Azure per la risorsa soluzioni SAP, è consigliabile che i clienti scelgano le stesse VNET per il monitoraggio delle risorse come server SAP HANA. Pertanto, è consigliabile che la macchina virtuale dell'agente di raccolta si trovi nella stessa VNET di SAP HANA server. Se i clienti usano un database non HANA, la macchina virtuale dell'agente di raccolta si troverà nella stessa VNET del database non HANA.  

 - **Quali versioni di HANA sono supportate?**  
HANA 1,0 SPS 12 (Rev. 120 o versione successiva) e HANA 2,0 SPS03 o versione successiva  

 - **Quali configurazioni di distribuzione HANA sono supportate?**  
Le configurazioni seguenti sono supportate:
   - Nodo singolo (scalabilità verticale) e multinodo (scalabilità orizzontale)  
   - Contenitore di database singolo (HANA 1,0 SPS 12) e più contenitori di database (HANA 1,0 SPS 12 o HANA 2,0)  
   - Failover host automatico (n + 1) e HSR  

 - **Quali versioni di SQL Server sono supportate?**  
SQL Server 2012 SP4 o versione successiva.  

 - **Quali configurazioni di SQL Server sono supportate?**  
Le configurazioni seguenti sono supportate:
   - Istanze autonome predefinite o denominate in una macchina virtuale  
   - Istanze o istanze in cluster in una configurazione AlwaysOn Quando si usa il nome virtuale della risorsa cluster o il nome del listener AlwaysOn. Al momento non vengono raccolte metriche specifiche del cluster o AlwaysOn    
   - Il database SQL di Azure (PAAS) non è attualmente supportato  

 - **Cosa accade se si elimina accidentalmente il gruppo di risorse gestite?**  
Il gruppo di risorse gestite è bloccato per impostazione predefinita. Pertanto, le probabilità di un'eliminazione accidentale del gruppo di risorse gestite da parte dei clienti sono minuscole.  
Se un cliente Elimina il gruppo di risorse gestite, il monitoraggio di Azure per le soluzioni SAP smetterà di funzionare. Il cliente dovrà distribuire una nuova risorsa di monitoraggio di Azure per le soluzioni SAP e ricominciare.  

 - **Quali ruoli sono necessari nella sottoscrizione di Azure per distribuire monitoraggio di Azure per la risorsa soluzioni SAP?**  
Ruolo Collaboratore.  

 - **Qual è il contratto di servizio per questo prodotto?**  
Le anteprime sono escluse dai contratti di servizio. Leggi il termine di licenza completo tramite monitoraggio di Azure per SAP Solutions Marketplace image.  

 - **È possibile monitorare l'intero panorama tramite questa soluzione?**  
Attualmente è possibile monitorare il database HANA, l'infrastruttura sottostante, il cluster a disponibilità elevata e Microsoft SQL Server in versione di anteprima pubblica.  

 - **Il servizio sostituisce SAP Solution Manager?**  
No. I clienti possono comunque usare SAP Solution Manager per il monitoraggio dei processi di business.  

 - **Qual è il valore di questo servizio rispetto a soluzioni tradizionali come SAP HANA Cockpit/Studio?**  
Il monitoraggio di Azure per le soluzioni SAP non è specifico del database HANA. Il monitoraggio di Azure per le soluzioni SAP supporta anche AnyDB.  

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima risorsa di monitoraggio di Azure per le soluzioni SAP.