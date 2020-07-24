---
title: 'Concetto: controlli di sicurezza per il servizio cloud Spring di Azure'
description: Usare i controlli di sicurezza incorporati nel servizio cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 2e001e5e927d9d4c5dc4c3eb74f7b5ad33617b99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037577"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlli di sicurezza per il servizio Azure Spring Cloud
I controlli di sicurezza sono incorporati nel servizio cloud Spring di Azure.

Un controllo di sicurezza è una qualità o una funzionalità di un servizio di Azure che contribuisce alla capacità del servizio di impedire, rilevare e rispondere alle vulnerabilità della sicurezza.  Per ogni controllo viene usato *Yes* o no per indicare se è attualmente *disponibile* per il servizio.  Per un controllo non applicabile al servizio viene usato *N/A* . 

**Controlli di sicurezza per la protezione dei dati**

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|:-------------|:-------|:-------------------------------|:----------------------|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | L'origine e gli artefatti caricati dall'utente, le impostazioni del server di configurazione, le impostazioni dell'app e i dati nell'archivio permanente vengono archiviati in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo.<br><br>La cache del server di configurazione, i file binari di runtime compilati dall'origine caricata e i registri applicazioni durante la durata dell'applicazione vengono salvati nel disco gestito da Azure, che crittografa automaticamente il contenuto inattivo.<br><br>Le immagini del contenitore compilate dall'origine caricata dall'utente vengono salvate in Azure Container Registry, che crittografa automaticamente il contenuto dell'immagine inattivo. | [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Crittografia lato server dei dischi gestiti di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Archiviazione immagini del contenitore in Registro Azure Container](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Crittografia temporanea | Sì | Per impostazione predefinita, gli endpoint pubblici delle app utente usano HTTPS per il traffico in ingresso. |  |
| Chiamate API crittografate | Sì | Le chiamate di gestione per configurare il servizio cloud Spring di Azure si verificano tramite Azure Resource Manager chiamate su HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

**Controlli di sicurezza per l'accesso alla rete**

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|:-------------|:-------|:-------------------------------|:----------------------|
| Tag del servizio | Sì | Usare il tag del servizio **AzureSpringCloud** per definire i controlli di accesso alla rete in uscita nei [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) o nel firewall di [Azure](https://docs.microsoft.com/azure/firewall/service-tags)per consentire il traffico verso le applicazioni cloud Spring di Azure.<br><br>*Nota:* Attualmente solo la nuova istanza del servizio cloud Spring di Azure creata dopo 2020/07/14 supporta il tag del servizio **AzureSpringCloud** . | [Tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) |
