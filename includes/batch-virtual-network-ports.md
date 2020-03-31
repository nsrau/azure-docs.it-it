---
title: File di inclusione
description: File di inclusione
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086271"
---
### <a name="general-requirements"></a>Requisiti generali

* La rete virtuale deve essere nella stessa sottoscrizione e area dell'account Batch usato per creare il pool.

* Il pool che usa la rete virtuale può avere un massimo di 4096 nodi.

* La subnet specificata per il pool deve disporre di indirizzi IP non assegnati sufficienti per contenere il numero di macchine virtuali usate come destinazione per il pool; questo valore corrisponde alla somma delle proprietà `targetDedicatedNodes` e `targetLowPriorityNodes` del pool. Se la subnet non dispone di sufficienti indirizzi IP non assegnati, il pool alloca parzialmente i nodi di calcolo e si verifica un errore di ridimensionamento.

* L'endpoint di Archiviazione di Azure deve essere risolto da qualsiasi server DNS personalizzato che fornisce informazioni alla rete virtuale. In particolare, gli URL con formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` e `<account>.blob.core.windows.net` devono essere risolvibili.

Altri requisiti della rete virtuale possono essere diversi a seconda che il pool di Batch sia nella configurazione della macchina virtuale o la configurazione di Servizi cloud. Per le nuove distribuzioni di pool in una rete virtuale, è consigliabile la configurazione della macchina virtuale.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pool nella configurazione della macchina virtuale

**Reti virtuali supportate** - Solo reti virtuali basate su Azure Resource Manager

**ID subnet** - Quando si specifica la subnet usando le API Batch, usare l'*identificatore di risorsa* della subnet. L'identificatore della subnet è nel formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Autorizzazioni** - Controllare se i criteri di sicurezza o i blocchi nel gruppo di risorse o nella sottoscrizione della rete virtuale limitano le autorizzazioni dell'utente per gestire la rete virtuale.

**Ulteriori risorse di rete** - Batch alloca automaticamente le risorse di rete aggiuntive nel gruppo di risorse contenente la rete virtuale.

> [!IMPORTANT]
>Per ogni 50 nodi dedicati (o ogni 20 nodi con priorità bassa), Batch alloca: un gruppo di sicurezza di rete (NSG), un indirizzo IP pubblico e un servizio di bilanciamento del carico. Queste risorse sono limitate in base alle [quote delle risorse](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) della sottoscrizione. Per i pool di grandi dimensioni, potrebbe essere necessario richiedere un aumento della quota per una o più di queste risorse.

#### <a name="network-security-groups-batch-default"></a>Gruppi di sicurezza di rete: batch predefinitoNetwork security groups: Batch default

La subnet deve consentire la comunicazione in ingresso dal servizio Batch per poter pianificare le attività nei nodi di calcolo e le comunicazioni in uscita per comunicare con Archiviazione di Azure o altre risorse in base alle esigenze del carico di lavoro. Per i pool nella configurazione della macchina virtuale, Batch aggiunge gruppi di sicurezza di rete a livello di interfacce di rete (NIC) collegate ai nodi di calcolo. Questi gruppi di sicurezza di rete sono configurati con le regole aggiuntive seguenti:These NSGs are configured with the following additional rules:

* Traffico TCP in ingresso sulle porte 29876 e 29877 `BatchNodeManagement` dagli indirizzi IP del servizio Batch che corrispondono al tag del servizio.
* Traffico TCP in ingresso sulla porta 22 (nodi di Linux) o sulla porta 3389 (nodi di Windows) per consentire l'accesso remoto. Per alcuni tipi di attività a più istanze in Linux (ad esempio MPI), è necessario consentire anche il traffico della porta SSH 22 per gli indirizzi IP nella subnet contenente i nodi di calcolo Batch.For certain types of multi-instance tasks on Linux (such as MPI), you will also need to allow SSH port 22 traffic for Ip in the subnet containing the Batch compute nodes. Questo può essere bloccato per le regole del gruppo di sicurezza di rete a livello di subnet (vedere di seguito).
* Traffico in uscita su qualsiasi porta verso la rete virtuale. Questo può essere modificato per le regole del gruppo di sicurezza di rete a livello di subnet (vedere di seguito).
* Traffico in uscita su qualsiasi porta verso Internet. Questo può essere modificato per le regole del gruppo di sicurezza di rete a livello di subnet (vedere di seguito).

> [!IMPORTANT]
> Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se la comunicazione ai nodi di calcolo nella subnet specificata viene negata da un gruppo di sicurezza di rete, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**. Inoltre, nessun blocco delle risorse deve essere applicato a qualsiasi risorsa creata da Batch, altrimenti ciò può comportare la pulizia delle risorse come risultato di azioni avviate dall'utente, ad esempio l'eliminazione di un pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Gruppi di sicurezza di rete: specifica delle regole a livello di subnetNetwork security groups: Specifying subnet-level rules

Non è necessario specificare i gruppi di sicurezza di rete a livello di subnet di rete virtuale perché Batch configura i propri gruppi di sicurezza di rete (vedere sopra). Se si dispone di un gruppo di sicurezza di rete associato alla subnet in cui vengono distribuiti i nodi di calcolo Batch o si desidera applicare regole del gruppo di sicurezza di rete personalizzate per ignorare i valori predefiniti applicati, è necessario configurare questo gruppo di sicurezza di rete con almeno le regole di sicurezza in ingresso e in uscita, come illustrato negli elementi seguenti Tabelle.

Configurare il traffico in ingresso sulla porta 3389 (Windows) o 22 (Linux) solo se è necessario consentire l'accesso remoto ai nodi di calcolo da origini esterne. Potrebbe essere necessario abilitare le regole della porta 22 in Linux se è necessario il supporto per le attività a più istanze con determinati runtime MPI. Consentire il traffico su queste porte non è strettamente necessario per i nodi di calcolo del pool per essere utilizzabili.

**Regole di sicurezza in ingresso**

| Indirizzi IP di origine | Tag del servizio di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[Tag di servizio](../articles/virtual-network/security-overview.md#service-tags) (se si utilizza la variante regionale, nella stessa area dell'account Batch) | * | Qualsiasi | 29876-29877 | TCP | Allow |
| IP di origine utente per l'accesso remoto ai nodi di calcolo e/o alla subnet dei nodi di calcolo per le attività multi-istanza Linux, se necessario. | N/D | * | Qualsiasi | 3389 (Windows), 22 (Linux) | TCP | Allow |

**Regole di sicurezza in uscita**

| Source (Sorgente) | Porte di origine | Destination | Tag del servizio di destinazione | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- | --- |
| Qualsiasi | * | [Tag di servizio](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(se si utilizza la variante regionale, nella stessa regione del tuo account Batch) | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>Pool nella configurazione di Servizi cloud

**Reti virtuali supportate** - Solo reti virtuali classiche

**ID subnet** - Quando si specifica la subnet usando le API Batch, usare l'*identificatore di risorsa* della subnet. L'identificatore della subnet è nel formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Autorizzazioni** - L'entità servizio `Microsoft Azure Batch` deve avere il ruolo di controllo degli accessi in base al ruolo (RBAC) `Classic Virtual Machine Contributor` per la rete virtuale specificata.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

La subnet deve consentire le comunicazioni in ingresso dal servizio Batch per essere in grado di pianificare le attività nei nodi di calcolo e le comunicazioni in uscita per comunicare con Archiviazione di Azure o altre risorse.

Non è necessario specificare un gruppo di sicurezza di rete, perché Batch configura le comunicazioni in ingresso solo da indirizzi IP di Batch verso i nodi del pool. Tuttavia, se alla subnet specificata sono associati gruppi di sicurezza di rete (NSG) e/o un firewall, configurare le regole di sicurezza in ingresso e in uscita, come illustrato nelle tabelle seguenti. Se la comunicazione ai nodi di calcolo nella subnet specificata viene negata da un gruppo di sicurezza di rete, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**.

Configurare il traffico in ingresso sulla porta 3389 per Windows se è necessario consentire l'accesso RDP ai nodi del pool. Non è necessario per rendere utilizzabili i nodi del pool.

**Regole di sicurezza in ingresso**

| Indirizzi IP di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
Qualsiasi <br /><br />Anche se per questa operazione sono richieste in effetti autorizzazioni di tipo "Consenti tutto", il servizio Batch applica una regola ACL a livello di ogni nodo per escludere tutti gli indirizzi IP di servizio non Batch. | * | Qualsiasi | 10100, 20100, 30100 | TCP | Allow |
| Facoltativo, per consentire l'accesso RDP ai nodi di calcolo. | * | Qualsiasi | 3389 | TCP | Allow |

**Regole di sicurezza in uscita**

| Source (Sorgente) | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
| Qualsiasi | * | Qualsiasi | 443  | Qualsiasi | Allow |
