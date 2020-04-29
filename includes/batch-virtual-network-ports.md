---
title: includere il file
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
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: dc08dcded6418208751edbffcb5d263db059ec01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80657491"
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
>Per ogni nodo dedicato 50 (o ogni 20 nodi con priorità bassa), batch alloca: un gruppo di sicurezza di rete (NSG), un indirizzo IP pubblico e un servizio di bilanciamento del carico. Queste risorse sono limitate in base alle [quote delle risorse](../articles/azure-resource-manager/management/azure-subscription-service-limits.md) della sottoscrizione. Per i pool di grandi dimensioni, potrebbe essere necessario richiedere un aumento della quota per una o più di queste risorse.

#### <a name="network-security-groups-batch-default"></a>Gruppi di sicurezza di rete: batch predefinito

La subnet deve consentire la comunicazione in ingresso dal servizio batch per poter pianificare le attività nei nodi di calcolo e le comunicazioni in uscita per comunicare con archiviazione di Azure o altre risorse in base alle esigenze del carico di lavoro. Per i pool nella configurazione della macchina virtuale, batch aggiunge gruppi a livello di interfacce di rete (NIC) collegato ai nodi di calcolo. Questi gruppi vengono configurati con le regole aggiuntive seguenti:

* Traffico TCP in ingresso sulle porte 29876 e 29877 dagli indirizzi IP del `BatchNodeManagement` servizio batch che corrispondono al tag di servizio.
* Traffico TCP in ingresso sulla porta 22 (nodi di Linux) o sulla porta 3389 (nodi di Windows) per consentire l'accesso remoto. Per alcuni tipi di attività a istanza multipla in Linux (ad esempio MPI), è necessario consentire anche il traffico della porta SSH 22 per gli indirizzi IP nella subnet che contiene i nodi di calcolo batch. Questo può essere bloccato per ogni regola di NSG a livello di subnet (vedere di seguito).
* Traffico in uscita su qualsiasi porta verso la rete virtuale. Questo può essere modificato per ogni regola di NSG a livello di subnet (vedere di seguito).
* Traffico in uscita su qualsiasi porta verso Internet. Questo può essere modificato per ogni regola di NSG a livello di subnet (vedere di seguito).

> [!IMPORTANT]
> Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se la comunicazione ai nodi di calcolo nella subnet specificata viene negata da un gruppo di sicurezza di rete, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**. Inoltre, non deve essere applicato alcun blocco di risorsa a qualsiasi risorsa creata da batch. in caso contrario, ciò può impedire la pulizia delle risorse in seguito a azioni avviate dall'utente, ad esempio l'eliminazione di un pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Gruppi di sicurezza di rete: specifica di regole a livello di subnet

Non è necessario specificare gruppi a livello di subnet della rete virtuale perché batch configura il proprio gruppi (vedere sopra). Se si dispone di un NSG associato alla subnet in cui vengono distribuiti i nodi di calcolo batch o si desidera applicare regole NSG personalizzate per sostituire le impostazioni predefinite applicate, è necessario configurare questo NSG con almeno le regole di sicurezza in ingresso e in uscita, come illustrato nelle tabelle seguenti.

Configurare il traffico in ingresso sulla porta 3389 (Windows) o 22 (Linux) solo se è necessario consentire l'accesso remoto ai nodi di calcolo da origini esterne. Potrebbe essere necessario abilitare le regole della porta 22 in Linux se è necessario il supporto per le attività a istanze diverse con determinati Runtime MPI. Consentire il traffico su queste porte non è strettamente necessario per l'utilizzo dei nodi di calcolo del pool.

**Regole di sicurezza in ingresso**

| Indirizzi IP di origine | Tag del servizio di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Action |
| --- | --- | --- | --- | --- | --- | --- |
| N/D | `BatchNodeManagement`[Tag del servizio](../articles/virtual-network/security-overview.md#service-tags) (se si usa una variante regionale, nella stessa area dell'account batch) | * | Qualsiasi | 29876-29877 | TCP | Allow |
| Indirizzi IP di origine utente per accedere in remoto ai nodi di calcolo e/o alla subnet del nodo di calcolo per le attività a istanze diverse di Linux, se necessario. | N/D | * | Qualsiasi | 3389 (Windows), 22 (Linux) | TCP | Allow |

> [!WARNING]
> Gli indirizzi IP del servizio batch possono cambiare nel tempo. È pertanto consigliabile utilizzare il `BatchNodeManagement` tag di servizio (o variante di area) per le regole NSG. Non è consigliabile popolare direttamente le regole NSG con indirizzi IP del servizio batch.

**Regole di sicurezza in uscita**

| Source (Sorgente) | Porte di origine | Destination | Tag del servizio di destinazione | Porte di destinazione | Protocollo | Action |
| --- | --- | --- | --- | --- | --- | --- |
| Qualsiasi | * | [Tag servizio](../articles/virtual-network/security-overview.md#service-tags) | `Storage`(se si usa una variante regionale, nella stessa area dell'account batch) | 443 | TCP | Allow |

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

| Indirizzi IP di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Action |
| --- | --- | --- | --- | --- | --- |
Qualsiasi <br /><br />Anche se per questa operazione sono richieste in effetti autorizzazioni di tipo "Consenti tutto", il servizio Batch applica una regola ACL a livello di ogni nodo per escludere tutti gli indirizzi IP di servizio non Batch. | * | Qualsiasi | 10100, 20100, 30100 | TCP | Allow |
| Facoltativo, per consentire l'accesso RDP ai nodi di calcolo. | * | Qualsiasi | 3389 | TCP | Allow |

**Regole di sicurezza in uscita**

| Source (Sorgente) | Porte di origine | Destination | Porte di destinazione | Protocollo | Action |
| --- | --- | --- | --- | --- | --- |
| Qualsiasi | * | Qualsiasi | 443  | Qualsiasi | Allow |
