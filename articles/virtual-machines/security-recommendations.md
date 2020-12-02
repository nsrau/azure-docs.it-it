---
title: Raccomandazioni sulla sicurezza per le macchine virtuali in Azure
description: Applicare queste indicazioni per le macchine virtuali in Azure per soddisfare gli obblighi di sicurezza descritti nel modello di responsabilità condivisa e per migliorare la sicurezza complessiva delle distribuzioni.
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 95ba93c4a5d71e6813c1bee144c983e1dfd8c89d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500326"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Raccomandazioni sulla sicurezza per le macchine virtuali in Azure


Questo articolo contiene raccomandazioni sulla sicurezza per le macchine virtuali di Azure. Seguire queste indicazioni per soddisfare gli obblighi di sicurezza descritti nel modello per la responsabilità condivisa. I consigli consentono inoltre di migliorare la sicurezza complessiva per le soluzioni di app Web. Per ulteriori informazioni sulle attività svolte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Alcune raccomandazioni di questo articolo possono essere automaticamente risolte dal centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per le risorse in Azure. analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza Viene quindi consigliata la modalità di risoluzione delle vulnerabilità. Per altre informazioni, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../security-center/security-center-recommendations.md).

Per informazioni generali sul centro sicurezza di Azure, vedere informazioni sul [Centro sicurezza di Azure](../security-center/security-center-introduction.md).

## <a name="general"></a>Generale

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Quando si compilano immagini di VM personalizzate, applicare gli aggiornamenti più recenti. | Prima di creare immagini, installare gli aggiornamenti più recenti per il sistema operativo e per tutte le applicazioni che faranno parte dell'immagine.  | - |
| mantenere aggiornate le VM. | È possibile usare la soluzione [Gestione aggiornamenti](../automation/update-management/overview.md) in automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure. | [Sì](../security-center/asset-inventory.md) |
| Eseguire il backup delle macchine virtuali. | [Backup di Azure](../backup/backup-overview.md) consente di proteggere i dati dell'applicazione e prevede costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Backup di Azure protegge le macchine virtuali che eseguono Windows e Linux. | - |
| Usare più macchine virtuali per una maggiore resilienza e disponibilità. | Se la macchina virtuale esegue applicazioni che devono essere a disponibilità elevata, usare più macchine virtuali o [set di disponibilità](./manage-availability.md). | - |
| Adottare una strategia di continuità aziendale e ripristino di emergenza (BCDR). | Azure Site Recovery consente di scegliere tra diverse opzioni progettate per supportare la continuità aziendale. Supporta diversi scenari di replica e failover. Per ulteriori informazioni, vedere  [informazioni su Site Recovery](../site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Sicurezza dei dati

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Crittografare i dischi del sistema operativo. | [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Senza le chiavi necessarie, il contenuto dei dischi crittografati non è leggibile. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco è stato copiato.| [Sì](../security-center/asset-inventory.md) |
| Crittografare i dischi dati. | [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. Senza le chiavi necessarie, il contenuto dei dischi crittografati non è leggibile. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco è stato copiato.| -  |
| Limitare il software installato. | Limitare il software installato ai requisiti necessari per applicare correttamente la soluzione. Questa guida consente di ridurre la superficie di attacco della soluzione. | - |
| Usare antivirus o antimalware. | In Azure è possibile usare software antimalware di fornitori di servizi di sicurezza come Microsoft, Symantec, Trend Micro e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce. È possibile distribuire Microsoft antimalware in base ai carichi di lavoro dell'applicazione. Microsoft antimalware è disponibile solo per computer Windows. Utilizzare la configurazione di base protetta per impostazione predefinita o avanzata. Per ulteriori informazioni, vedere [Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../security/fundamentals/antimalware.md). | - |
| Archiviare in modo sicuro le chiavi e i segreti. | Semplifica la gestione dei segreti e delle chiavi offrendo ai proprietari dell'applicazione un'opzione sicura e gestita centralmente. Questa gestione riduce il rischio di compromissione accidentale o perdita. Azure Key Vault possibile archiviare in modo sicuro le chiavi in moduli di protezione hardware (HSM) certificati per FIPS 140-2 livello 2. Se è necessario usare FIPs 140,2 livello 3 per archiviare le chiavi e i segreti, è possibile usare il modulo di protezione [hardware dedicato di Azure](../dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso 

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Centralizzare l'autenticazione della macchina virtuale. | È possibile centralizzare l'autenticazione delle VM Windows e Linux usando [l'autenticazione Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md). | - |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Monitorare le VM. | È possibile usare [monitoraggio di Azure per le macchine virtuali](../azure-monitor/insights/vminsights-overview.md) per monitorare lo stato delle VM di Azure e dei set di scalabilità di macchine virtuali. I problemi di prestazioni di una VM possono causare interruzioni del servizio, il che viola il principio di disponibilità della sicurezza. | - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Limitare l'accesso alle porte di gestione. | Gli utenti malintenzionati analizzano gli intervalli IP del cloud pubblico per le porte di gestione aperte e tentano attacchi semplici come le password comuni e le vulnerabilità note senza patch. È possibile usare [l'accesso JIT alle VM](../security-center/security-center-just-in-time.md) per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e fornendo connessioni semplici alle VM quando sono necessarie. | - |
| Limitare l'accesso alla rete. | I gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e di controllare il numero di endpoint esposti. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](../virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider di applicazioni per informazioni sui requisiti di sicurezza aggiuntivi. Per ulteriori informazioni sullo sviluppo di applicazioni sicure, vedere la [documentazione sullo sviluppo protetto](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).