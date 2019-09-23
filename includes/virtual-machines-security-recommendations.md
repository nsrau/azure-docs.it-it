---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: 60d4957f65c124de683c6d156561de3b52aaec94
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71163943"
---
Questo articolo contiene raccomandazioni sulla sicurezza per le macchine virtuali di Azure. L'implementazione di questi consigli consente di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa e di migliorare la sicurezza complessiva per le soluzioni di app Web. Per ulteriori informazioni sulle attività svolte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [responsabilità condivise per cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

Alcuni consigli inclusi in questo articolo possono essere monitorati automaticamente dal centro sicurezza di Azure. Il Centro sicurezza di Azure è la prima linea di difesa per la protezione delle risorse in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Fornisce quindi suggerimenti su come risolverli.

- Per altre informazioni sulle raccomandazioni del Centro sicurezza di Azure, vedere [raccomandazioni per la sicurezza nel centro sicurezza di Azure](../articles/security-center/security-center-recommendations.md).
- Per informazioni sul centro sicurezza di Azure, vedere [che cos'è il Centro sicurezza di Azure?](../articles/security-center/security-center-intro.md)

## <a name="recommendations"></a>Consigli

| Category | Raccomandazione | Commenti | Centro sicurezza |
|-|-|----|--|
| Generale | Quando si compilano immagini di VM personalizzate, usare gli aggiornamenti più recenti | Prima di creare immagini, assicurarsi che siano installati tutti gli aggiornamenti più recenti per il sistema operativo e tutte le applicazioni che faranno parte dell'immagine.  | - |
| Generale | Mantieni aggiornate le VM | È possibile usare la soluzione [Gestione aggiornamenti](../articles/automation/automation-update-management.md) in automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure | [Sì](../articles/security-center/security-center-apply-system-updates.md) |
| Generale | Eseguire il backup delle macchine virtuali | [Backup di Azure](../articles/backup/backup-overview.md) consente di proteggere i dati dell'applicazione con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette. | - |
| Generale | Usare più macchine virtuali per una maggiore resilienza e disponibilità | Se la macchina virtuale esegue applicazioni che devono avere disponibilità elevata, è necessario disporre di più macchine virtuali o [set di disponibilità](../articles/virtual-machines/windows/manage-availability.md) | - |
| Generale | Adottare una strategia di continuità aziendale e ripristino di emergenza (BCDR) | Azure Site Recovery consente di scegliere tra diverse opzioni progettate per supportare le esigenze di continuità aziendale. Supporta diversi scenari di replica e di failover. Per ulteriori informazioni, vedere [informazioni su Site Recovery](../articles/site-recovery/site-recovery-overview.md) | - |
| Gestione delle identità e dell'accesso | Centralizzare l'autenticazione della macchina virtuale | È possibile centralizzare l'autenticazione delle macchine virtuali Windows e Linux usando [l'autenticazione Azure ad](../articles/active-directory/develop/authentication-scenarios.md). | - |
| Sicurezza dei dati | Crittografare i dischi del sistema operativo | [Crittografia dischi di Azure](../articles/security/azure-security-disk-encryption-overview.md) consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. La crittografia dei dischi rende il contenuto illeggibile senza le chiavi necessarie. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco è stato copiato| [Sì](../articles/security-center/security-center-apply-disk-encryption.md) |
| Sicurezza dei dati | Crittografare i dischi dati | [Crittografia dischi di Azure](../articles/security/azure-security-disk-encryption-overview.md) consente di crittografare i dischi delle macchine virtuali IaaS Windows e Linux. La crittografia dei dischi rende il contenuto illeggibile senza le chiavi necessarie. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco è stato copiato| -  |
| Sicurezza dei dati | Limitare il software installato | La limitazione del software installato a ciò che è necessario per implementare correttamente la soluzione consente di ridurre la superficie di attacco della soluzione | - |
| Sicurezza dei dati | USA antivirus/antimalware | In Azure è possibile usare software antimalware di fornitori di sicurezza come Microsoft, Symantec, Trend Micro e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce. È possibile distribuire Microsoft Antimalware Protection in base ai carichi di lavoro dell'applicazione, con una configurazione di base protetta per impostazione predefinita o avanzata. Per altre informazioni su Microsoft antimalware per Azure, vedere [Microsoft antimalware per servizi cloud e macchine virtuali di Azure](../articles/security/azure-security-antimalware.md) | - |
| Sicurezza dei dati | Archiviare in modo sicuro le chiavi e i segreti | Semplificando la gestione dei segreti e delle chiavi, fornire ai proprietari dell'applicazione un'opzione sicura gestita a livello centralizzato consente di ridurre il rischio di compromissione o perdite accidentali. Azure Key Vault possibile archiviare in modo sicuro le chiavi in moduli di protezione hardware (HSM) certificati per FIPS 140-2 livello 2. Se è necessario archiviare le chiavi e i segreti usando FIPs 140,2 livello 3, è possibile usare il modulo di protezione [hardware dedicato di Azure](../articles/dedicated-hsm/overview.md) | - |
| Rete | Limitazione dell'accesso alle porte di gestione | Gli utenti malintenzionati analizzano gli intervalli IP del cloud pubblico per le porte di gestione aperte e tentano attacchi semplici come le password comuni e le vulnerabilità note senza patch. [L'accesso alle macchine virtuali JIT (just-in-Time)](../articles/security-center/security-center-just-in-time.md) può essere usato per bloccare il traffico in ingresso verso le macchine virtuali di Azure, riducendo l'esposizione agli attacchi offrendo un facile accesso per connettersi alle macchine virtuali quando necessario. | - |
| Rete | Limitare l'accesso alla rete | I gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e di controllare il numero di endpoint esposti. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](../articles/virtual-network/manage-network-security-group.md) | - |
| Monitoraggio | Monitorare le macchine virtuali | È possibile usare [monitoraggio di Azure per](../articles/azure-monitor/insights/vminsights-overview.md) le macchine virtuali per monitorare lo stato delle macchine virtuali di Azure e dei set di scalabilità di macchine virtuali. I problemi di prestazioni di una VM possono causare interruzioni del servizio, il che viola il principio di disponibilità della sicurezza. | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider di applicazioni per verificare se sono presenti requisiti di sicurezza aggiuntivi. Per ulteriori informazioni sullo sviluppo di applicazioni sicure, vedere la [documentazione sullo sviluppo protetto](../articles/security/fundamentals/abstract-develop-secure-apps.md).