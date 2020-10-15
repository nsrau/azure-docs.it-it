---
title: 'Concetto: controlli di sicurezza per il servizio cloud Spring di Azure'
description: Usare i controlli di sicurezza incorporati nel servizio cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 102b8f4099c93637779743b9c08347266c1d044f
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094025"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlli di sicurezza per il servizio Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C#

I controlli di sicurezza sono incorporati nel servizio cloud Spring di Azure.

Un controllo di sicurezza è una qualità o una funzionalità di un servizio di Azure che contribuisce alla capacità del servizio di impedire, rilevare e rispondere alle vulnerabilità della sicurezza.  Per ogni controllo viene usato *Yes* o no per indicare se è attualmente *disponibile* per il servizio.  Per un controllo non applicabile al servizio viene usato *N/A* . 

**Controlli di sicurezza per la protezione dei dati**

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|:-------------|:-------|:-------------------------------|:----------------------|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | L'origine e gli artefatti caricati dall'utente, le impostazioni del server di configurazione, le impostazioni dell'app e i dati nell'archivio permanente vengono archiviati in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo.<br><br>La cache del server di configurazione, i file binari di runtime compilati dall'origine caricata e i registri delle applicazioni durante la durata dell'applicazione vengono salvati nel disco gestito di Azure, che crittografa automaticamente il contenuto inattivo.<br><br>Le immagini del contenitore compilate dall'origine caricata dall'utente vengono salvate in Azure Container Registry, che crittografa automaticamente il contenuto dell'immagine inattivo. | [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)<br><br>[Crittografia lato server dei dischi gestiti di Azure](../virtual-machines/linux/disk-encryption.md)<br><br>[Archiviazione immagini del contenitore in Registro Azure Container](../container-registry/container-registry-storage.md) |
| Crittografia temporanea | Sì | Per impostazione predefinita, gli endpoint pubblici delle app utente usano HTTPS per il traffico in ingresso. |  |
| Chiamate API crittografate | Sì | Le chiamate di gestione per configurare il servizio cloud Spring di Azure si verificano tramite Azure Resource Manager chiamate su HTTPS. | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**Controlli di sicurezza per l'accesso alla rete**

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|:-------------|:-------|:-------------------------------|:----------------------|
| Tag del servizio | Sì | Usare il tag del servizio **AzureSpringCloud** per definire i controlli di accesso alla rete in uscita nei [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md)per consentire il traffico verso le applicazioni cloud Spring di Azure.<br><br>*Nota:* Attualmente solo la nuova istanza del servizio cloud Spring di Azure creata dopo 2020/07/14 supporta il tag del servizio **AzureSpringCloud** . | [Tag di servizio](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud](spring-cloud-quickstart.md)