---
title: File di inclusione
description: File di inclusione
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 10/05/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9246dea7fa12e5ac9378203e96352e917679525b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312567"
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

**Ulteriori risorse di rete** - Batch alloca automaticamente le risorse di rete aggiuntive nel gruppo di risorse contenente la rete virtuale. Ogni 50 nodi dedicati (o ogni 20 nodi con priorità bassa), Batch alloca 1 gruppo di sicurezza di rete (NSG), 1 indirizzo IP pubblico e 1 bilanciamento del carico. Queste risorse sono limitate in base alle [quote delle risorse](../articles/azure-subscription-service-limits.md) della sottoscrizione. Per pool di grandi dimensioni potrebbe essere necessario richiedere un aumento della quota per una o più di queste risorse.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

La subnet deve consentire le comunicazioni in ingresso dal servizio Batch per essere in grado di pianificare le attività nei nodi di calcolo e le comunicazioni in uscita per comunicare con Archiviazione di Azure o altre risorse. Per i pool nella configurazione della macchina virtuale, Batch aggiunge i gruppi di sicurezza di rete al livello delle interfacce di rete (NIC) collegate alle macchine virtuali. Questi gruppi di sicurezza di rete configurano automaticamente le regole in ingresso e in uscita per consentire il traffico seguente:

* Traffico TCP in ingresso sulle porte 29876 e 29877 da indirizzi IP del ruolo del servizio Batch. 
* Traffico TCP in ingresso sulla porta 22 (nodi di Linux) o sulla porta 3389 (nodi di Windows) per consentire l'accesso remoto.
* Traffico in uscita su qualsiasi porta verso la rete virtuale.
* Traffico in uscita su qualsiasi porta verso Internet.

> [!IMPORTANT]
> Prestare attenzione se si modificano o aggiungono regole in ingresso o in uscita nei gruppi di sicurezza di rete configurati per Batch. Se la comunicazione ai nodi di calcolo nella subnet specificata viene negata da un gruppo di sicurezza di rete, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**.

Non occorre specificare i gruppi di sicurezza di rete a livello di subnet perché Batch configura gruppi di sicurezza di rete propri. Tuttavia, se la subnet specificata dispone di gruppi di sicurezza di rete (NSG) e/o di un firewall associati, configurare le regole di sicurezza in ingresso e in uscita, come illustrato nelle tabelle seguenti. Configurare il traffico in ingresso sulla porta 3389 (Windows) o 22 (Linux) solo se è necessario per consentire l'accesso remoto alle macchine virtuali del pool. Non è necessario per rendere utilizzabili le macchine virtuali del pool.

**Regole di sicurezza in ingresso**

| Indirizzi IP di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
Qualsiasi <br /><br />Anche se è necessario consentire l'accesso a tutti gli indirizzi IP, il servizio Batch applica un gruppo di sicurezza di rete a livello di interfaccia di rete a ogni macchina virtuale creata con una configurazione di macchina virtuale che esclude tutti gli indirizzi IP non associati al servizio Batch. | * | Qualsiasi | 29876-29877 | TCP | CONSENTI |
| Computer utente, usati per il debug, per accedere in remoto alle macchine virtuali del pool. | * | Qualsiasi |  3389 (Windows), 22 (Linux) | TCP | CONSENTI |

**Regole di sicurezza in uscita**

| Sorgente | Porte di origine | Destination | Tag del servizio di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
| Qualsiasi | 443 | [Tag di servizio](../articles/virtual-network/security-overview.md#service-tags) | Archiviazione (nella stessa area dell'account Batch e della rete virtuale)  | Qualsiasi | CONSENTI |

### <a name="pools-in-the-cloud-services-configuration"></a>Pool nella configurazione di Servizi cloud

**Reti virtuali supportate** - Solo reti virtuali classiche

**ID subnet** - Quando si specifica la subnet usando le API Batch, usare l'*identificatore di risorsa* della subnet. L'identificatore della subnet è nel formato:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Autorizzazioni** - L'entità servizio `MicrosoftAzureBatch` deve avere il ruolo di controllo degli accessi in base al ruolo (RBAC) `Classic Virtual Machine Contributor` per la rete virtuale specificata.

#### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

La subnet deve consentire le comunicazioni in ingresso dal servizio Batch per essere in grado di pianificare le attività nei nodi di calcolo e le comunicazioni in uscita per comunicare con Archiviazione di Azure o altre risorse.

Non è necessario specificare un gruppo di sicurezza di rete, perché Batch configura le comunicazioni in ingresso solo da indirizzi IP di Batch verso i nodi del pool. Tuttavia, se alla subnet specificata sono associati gruppi di sicurezza di rete (NSG) e/o un firewall, configurare le regole di sicurezza in ingresso e in uscita, come illustrato nelle tabelle seguenti. Se la comunicazione ai nodi di calcolo nella subnet specificata viene negata da un gruppo di sicurezza di rete, il servizio Batch imposta lo stato dei nodi di calcolo su **Non utilizzabile**.

 Configurare il traffico in ingresso sulla porta 3389 (Windows) o 22 (Linux) solo se è necessario per consentire l'accesso remoto ai nodi del pool. Non è necessario per rendere utilizzabili i nodi del pool.

**Regole di sicurezza in ingresso**

| Indirizzi IP di origine | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
Qualsiasi <br /><br />Anche se per questa operazione sono richieste in effetti autorizzazioni di tipo "Consenti tutto", il servizio Batch applica una regola ACL a livello di ogni nodo per escludere tutti gli indirizzi IP di servizio non Batch. | * | Qualsiasi | 10100, 20100, 30100 | TCP | CONSENTI |
| Computer utente, usati per il debug, per accedere in remoto alle macchine virtuali del pool. | * | Qualsiasi |  3389 (Windows), 22 (Linux) | TCP | CONSENTI |

**Regole di sicurezza in uscita**

| Sorgente | Porte di origine | Destination | Porte di destinazione | Protocollo | Azione |
| --- | --- | --- | --- | --- | --- |
| Qualsiasi | * | Qualsiasi | 443  | Qualsiasi | CONSENTI |