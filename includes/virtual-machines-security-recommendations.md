---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048584"
---
Questo articolo contiene consigli sulla sicurezza per macchine virtuali di Azure.This article contains security recommendations for Azure Virtual Machines. Segui questi consigli per adempiere agli obblighi di sicurezza descritti nel nostro modello per la responsabilità condivisa. I consigli ti aiuteranno anche a migliorare la sicurezza complessiva per le soluzioni delle tue app web. Per ulteriori informazioni sulle operazioni eseguite da Microsoft per adempiere alle responsabilità del provider di servizi, vedere [Responsabilità condivise per](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)il cloud computing .

Alcuni dei consigli di questo articolo possono essere risolti automaticamente dal Centro sicurezza di Azure.Some of s this article's recommendations's can be automatically addressed by Azure Security Center. Azure Security Center is the first line of defense for your resources in Azure. Analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità della sicurezza. Si consiglia quindi come risolvere le vulnerabilità. Per altre informazioni, vedere Consigli sulla sicurezza nel Centro sicurezza di Azure.For more information, see [Security recommendations in Azure Security Center](../articles/security-center/security-center-recommendations.md).

Per informazioni generali sul Centro sicurezza di Azure, vedere [Che cos'è](../articles/security-center/security-center-intro.md)il Centro sicurezza di Azure? .

## <a name="general"></a>Generale

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Quando si compilano immagini di macchine virtuali personalizzate, applicare gli aggiornamenti più recenti. | Prima di creare immagini, installare gli aggiornamenti più recenti per il sistema operativo e per tutte le applicazioni che faranno parte dell'immagine.  | - |
| mantenere aggiornate le VM. | È possibile usare la soluzione di gestione degli aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per i computer Windows e Linux in Azure.You can use the [Update Management](../articles/automation/automation-update-management.md) solution in Azure Automation to manage operating system updates for your Windows and Linux computers in Azure. | [Sì](../articles/security-center/security-center-apply-system-updates.md) |
| Eseguire il backup delle macchine virtuali. | [Backup di Azure](../articles/backup/backup-overview.md) consente di proteggere i dati dell'applicazione e ha costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Azure Backup protects your VMs that run Windows and Linux. | - |
| Usare più macchine virtuali per una maggiore resilienza e disponibilità. | Se la macchina virtuale esegue applicazioni che devono essere a disponibilità elevata, usare più macchine virtuali o set di [disponibilità.](../articles/virtual-machines/windows/manage-availability.md) | - |
| Adottare una strategia di continuità aziendale e ripristino di emergenza (BCDR). | Azure Site Recovery consente di scegliere tra diverse opzioni progettate per supportare la continuità aziendale. Supporta scenari di replica e failover diversi. Per ulteriori informazioni, vedere [Informazioni su Site Recovery](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Sicurezza dei dati

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Crittografare i dischi del sistema operativo. | [Crittografia disco di Azure](../articles/security/azure-security-disk-encryption-overview.md) consente di crittografare i dischi delle macchine virtuali Windows e IaaS di Windows.Azure Disk Encryption helps you encrypt your Windows and Linux IaaS VM disks. Senza le chiavi necessarie, il contenuto dei dischi crittografati è illeggibile. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco venisse copiato.| [Sì](../articles/security-center/security-center-apply-disk-encryption.md) |
| Crittografare i dischi dati. | [Crittografia disco di Azure](../articles/security/azure-security-disk-encryption-overview.md) consente di crittografare i dischi delle macchine virtuali Windows e IaaS di Windows.Azure Disk Encryption helps you encrypt your Windows and Linux IaaS VM disks. Senza le chiavi necessarie, il contenuto dei dischi crittografati è illeggibile. La crittografia del disco protegge i dati archiviati da accessi non autorizzati che altrimenti sarebbero possibili se il disco venisse copiato.| -  |
| Limitare il software installato. | Limitare il software installato a quello necessario per applicare correttamente la soluzione. Questa linea guida consente di ridurre la superficie di attacco della soluzione. | - |
| Utilizzare antivirus o antimalware. | In Azure è possibile usare software antimalware di fornitori di sicurezza come Microsoft, Symantec, Trend Micro e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce. È possibile distribuire Microsoft Antimalware in base ai carichi di lavoro dell'applicazione. Utilizzare la configurazione predefinita di base o la configurazione personalizzata avanzata. Per altre informazioni, vedere [Microsoft Antimalware per i servizi cloud di Azure e le macchine virtuali.](../articles/security/azure-security-antimalware.md) | - |
| Memorizza in modo sicuro chiavi e segreti. | Semplifica la gestione dei tuoi segreti e delle tue chiavi fornendo ai proprietari delle tue applicazioni un'opzione sicura e gestita centralmente. Questa gestione riduce il rischio di un compromesso o di una perdita accidentale. L'insieme di credenziali delle chiavi di Azure può archiviare in modo sicuro le chiavi nei moduli di sicurezza hardware (HSM) certificati per FIPS 140-2 Livello 2.Azure Key Vault can securely store your keys in hardware security modules (HSMs) that are certified to FIPS 140-2 Level 2. Se è necessario usare FIP 140.2 Livello 3 per archiviare le chiavi e i segreti, è possibile usare [Azure dedicato HSM](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso 

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Centralizzare l'autenticazione della macchina virtuale. | È possibile centralizzare l'autenticazione delle macchine virtuali Windows e Linux utilizzando l'autenticazione di [Azure Active Directory.](../articles/active-directory/develop/authentication-scenarios.md) | - |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Monitorare le macchine virtuali. | È possibile usare [Monitoraggio di Azure per le macchine virtuali per](../articles/azure-monitor/insights/vminsights-overview.md) monitorare lo stato delle macchine virtuali di Azure e dei set di scalabilità delle macchine virtuali. I problemi di prestazioni di una VM possono causare interruzioni del servizio, il che viola il principio di disponibilità della sicurezza. | - |

## <a name="networking"></a>Rete

| Recommendation | Commenti | Centro sicurezza |
|-|----|--|
| Limitare l'accesso alle porte di gestione. | Gli aggressori analizzano gli intervalli IP del cloud pubblico per le porte di gestione aperte e tentano attacchi "facili" come password comuni e vulnerabilità senza patch note. È possibile usare [l'accesso](../articles/security-center/security-center-just-in-time.md) alle macchine virtuali JIT per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e fornendo facilmente connessioni alle macchine virtuali quando sono necessarie. | - |
| Limitare l'accesso alla rete. | I gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e di controllare il numero di endpoint esposti.Network security groups allow you to restrict network access and control the number of exposed endpoints. Per ulteriori informazioni, vedere [Creare, modificare o eliminare un gruppo](../articles/virtual-network/manage-network-security-group.md)di sicurezza di rete. | - |

## <a name="next-steps"></a>Passaggi successivi

Rivolgersi al provider dell'applicazione per informazioni sui requisiti di sicurezza aggiuntivi. Per ulteriori informazioni sullo sviluppo di applicazioni protette, vedere [Documentazione relativa](../articles/security/fundamentals/abstract-develop-secure-apps.md)allo sviluppo protetto .
