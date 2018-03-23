---
title: Procedura dettagliata per un'infrastruttura di esempio di Azure | Microsoft Docs
description: Informazioni sulle principali linee guida di progettazione e implementazione per la distribuzione di un'infrastruttura di esempio in Azure.
documentationcenter: ''
services: virtual-machines-windows
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c532657951d6d0241a5d8d25a56bb237ad481567
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Procedura dettagliata per un'infrastruttura di esempio di Azure per macchine virtuali Windows
Questo articolo illustra le modalità di compilazione di un'infrastruttura di applicazione di esempio. Sarà trattata la progettazione di un'infrastruttura per un semplice negozio online che riunisce tutte le linee guida e le decisioni sulle convenzioni di denominazione, i set di disponibilità, le reti virtuali e i servizi di bilanciamento del carico e l'effettiva distribuzione delle macchine virtuali.

## <a name="example-workload"></a>Carico di lavoro di esempio
Adventure Works Cycles desidera compilare un'applicazione per un negozio online in Azure, che è costituita da:

* Due server IIS che eseguono il client front-end in un livello Web
* Due server IIS che elaborano i dati e gli ordini in un livello applicazione
* Due istanze di Microsoft SQL Server con gruppi di disponibilità AlwaysOn (due SQL Server e un server di controllo del nodo di maggioranza) per archiviare dati sui prodotti e ordini in un livello di database
* Due controller di dominio di Active Directory per gli account dei clienti e dei fornitori in un livello di autenticazione
* Tutti i server si trovano in due subnet:
  * una subnet front-end per i server Web 
  * una subnet back-end per i server applicazioni, i cluster SQL e i controller di dominio

![Diagramma dei diversi livelli di infrastruttura dell'applicazione](./media/infrastructure-example/example-tiers.png)

Il traffico Web protetto in ingresso deve essere sottoposto al bilanciamento del carico tra i server Web mentre i clienti visitano il negozio online. Il traffico di elaborazione degli ordini sotto forma di richieste HTTP provenienti dai server Web deve essere bilanciato tra i server applicazioni. Inoltre, l'infrastruttura deve essere progettata per l'elevata disponibilità.

La progettazione risultante deve includere:

* Una sottoscrizione e un account di Azure
* Un unico gruppo di risorse
* Azure Managed Disks
* Una rete virtuale con due subnet
* Set di disponibilità per le macchine virtuali con ruoli simili
* Macchine virtuali

Tutti gli elementi devono rispettare le convenzioni di denominazione seguenti:

* Adventure Works Cycles usa come prefisso **[carico di lavoro IT]-[località]-[risorsa di Azure]**
  * In questo esempio, "**azos**" (Azure online Store) è il nome del carico di lavoro IT e "**use**" (Stati Uniti orientali 2) è la località
* Le reti virtuali usano AZOS-USE-VN**[numero]**
* I set di disponibilità usano azos-use-as-**[ruolo]**
* I nomi delle macchine virtuali usano azos-use-vm-**[nomevm]**

## <a name="azure-subscriptions-and-accounts"></a>Sottoscrizioni e account di Azure
Adventure Works Cycles usa la propria sottoscrizione aziendale, denominata Adventure Works Enterprise Subscription, per fornire la fatturazione per questo carico di lavoro IT.

## <a name="storage"></a>Archiviazione
Adventure Works Cycles ha determinato che è necessario usare Managed Disks di Azure. Durante la creazione di macchine virtuali, vengono usati entrambi i livelli di archiviazione disponibili:

* **Archiviazione Standard** per i server Web, i server applicazioni e i controller di dominio e relativi dischi dati.
* **Archiviazione Premium** per le VM SQL Server e relativi dischi dati.

## <a name="virtual-network-and-subnets"></a>Rete virtuale e subnet
Poiché la rete virtuale non necessita di connettività costante alla rete locale Adventure Work Cycles, l’azienda ha optato per una rete virtuale solo cloud.

Contoso ha creato una rete virtuale solo cloud con le impostazioni seguenti tramite il portale di Azure:

* Nome: AZOS-USE-VN01
* Sede: Stati Uniti orientali 2
* Spazio degli indirizzi della rete virtuale: 10.0.0.0/8
* Prima subnet:
  * Nome: FrontEnd
  * Spazio degli indirizzi: 10.0.1.0/24
* Seconda subnet:
  * Nome: BackEnd
  * Spazio degli indirizzi: 10.0.2.0/24

## <a name="availability-sets"></a>Set di disponibilità
Per mantenere elevata la disponibilità dei quattro livelli del negozio online, Adventure Works Cycles ha optato per quattro set di disponibilità:

* **azos-use-as-web** per i server Web
* **azos-use-as-app** per i server applicazioni
* **azos-use-as-sql** per SQL Server
* **azos-use-as-dc** per i controller di dominio

## <a name="virtual-machines"></a>Macchine virtuali
Adventure Works Cycles ha optato per i seguenti nomi per le macchine virtuali di Azure:

* **azos-use-vm-web01** per il primo server Web
* **azos-use-vm-web02** per il secondo server Web
* **azos-use-vm-app01** per il primo server applicazioni
* **azos-use-vm-app02** , per il secondo server applicazioni
* **azos-use-vm-sql01** per il primo SQL Server del cluster
* **azos-use-vm-sql02** per il secondo SQL Server del cluster
* **azos-use-vm-dc01** per il primo controller di dominio
* **azos-use-vm-dc02** per il secondo controller di dominio

Di seguito è riportata la configurazione risultante.

![Infrastruttura dell'applicazione finale distribuita in Azure](./media/infrastructure-example/example-config.png)

Questa configurazione include:

* Una rete virtuale solo cloud con due subnet (front-end e back-end)
* Managed Disks di Azure con dischi Standard e Premium
* Quattro set di disponibilità, uno per ciascun livello del negozio online
* Le macchine virtuali per i quattro livelli
* Un set esterno con bilanciamento del carico per il traffico Web basato su HTTPS da Internet ai server Web
* Un set interno con bilanciamento del carico per il traffico Web crittografato dai server Web ai server applicazioni
* Un unico gruppo di risorse
