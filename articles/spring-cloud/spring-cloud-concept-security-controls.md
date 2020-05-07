---
title: 'Concetto: controlli di sicurezza per il servizio cloud Spring di Azure'
description: Usare i controlli di sicurezza incorporati nel servizio cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594985"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Controlli di sicurezza per il servizio cloud Spring di Azure
I controlli di sicurezza sono incorporati nel servizio cloud Spring di Azure.

Un controllo di sicurezza è una qualità o una funzionalità di un servizio di Azure che contribuisce alla capacità del servizio di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.  Per ogni controllo viene usato *Yes* o no per indicare se è attualmente *disponibile* per il servizio.  Per un controllo non applicabile al servizio viene usato *N/A* . 

**Controlli di sicurezza per la protezione dei dati**

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|:-------------|:-------|:-------------------------------|:----------------------|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | L'origine e gli artefatti caricati dall'utente, le impostazioni del server di configurazione, le impostazioni dell'app e i dati nell'archivio permanente vengono archiviati in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo.<br><br>La cache del server di configurazione, i file binari di runtime compilati dall'origine caricata e i registri applicazioni durante la durata dell'applicazione vengono salvati nel disco gestito da Azure, che crittografa automaticamente il contenuto inattivo.<br><br>Le immagini del contenitore compilate dall'origine caricata dall'utente vengono salvate in Azure Container Registry, che crittografa automaticamente il contenuto dell'immagine inattivo. | [Crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Crittografia lato server di Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Archiviazione immagini del contenitore in Registro Azure Container](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| Crittografia temporanea | Sì | Per impostazione predefinita, gli endpoint pubblici delle app utente usano HTTPS per il traffico in ingresso. |  |
| Chiamate API crittografate | Sì | Le chiamate di gestione per configurare il servizio cloud Spring di Azure si verificano tramite Azure Resource Manager chiamate su HTTPS. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

