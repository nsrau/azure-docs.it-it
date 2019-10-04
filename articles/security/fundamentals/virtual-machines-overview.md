---
title: Funzionalità di sicurezza usate con macchine virtuali di Azure-sicurezza di Azure | Microsoft Docs
description: Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure che possono essere usate con Macchine virtuali di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 4aca2a4495d4b6c3669982b2e7757b7252d70f6a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828568"
---
# <a name="azure-virtual-machines-security-overview"></a>Informazioni generali sulla sicurezza di Macchine virtuali di Azure
Questo articolo fornisce una panoramica delle funzionalità di sicurezza principali di Azure che possono essere usate con le macchine virtuali.

Con le Macchine virtuali di Azure è possibile distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. Il servizio supporta Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e servizi BizTalk di Azure. In questo modo è possibile distribuire qualsiasi carico di lavoro e implementare qualsiasi lingua su quasi tutti i sistemi operativi.

Una macchina virtuale di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale. È possibile compilare e distribuire le applicazioni con la certezza che i dati saranno protetti e al sicuro nei nostri data center che offrono livelli di sicurezza elevati.

Con Azure è possibile creare soluzioni conformi con sicurezza avanzata che:

* Proteggono le macchine virtuali da virus e malware.
* Applicano la crittografia ai dati sensibili.
* Proteggono il traffico di rete.
* Identificano e rilevano minacce.
* Soddisfano i requisiti di conformità.  

## <a name="antimalware"></a>Antimalware

Con Azure è possibile usare software antimalware a cura dei principali fornitori di soluzioni di sicurezza, come Microsoft, Symantec, Trend Micro e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce.

Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione in tempo reale che aiuta a identificare e rimuovere virus, spyware e altro software dannoso.  Microsoft Antimalware per Azure offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure.

Microsoft Antimalware per Azure è una soluzione con un agente singolo per applicazioni e ambienti tenant, progettata per l'esecuzione in background senza intervento da parte dell'utente. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro dell'applicazione, con una configurazione sicura per impostazione predefinita o avanzata personalizzata, incluso il monitoraggio antimalware.

Quando si distribuisce e si abilita Microsoft Antimalware per Azure sono disponibili le funzionalità di base seguenti:

* **Protezione in tempo reale**: monitora l'attività in Servizi cloud e Macchine virtuali per rilevare e bloccare l'esecuzione di malware.
* **Analisi pianificata**: esegue periodicamente un'analisi mirata per rilevare il malware, inclusi i programmi in esecuzione attiva.
* **Correzione del malware**: interviene automaticamente sul malware rilevato, ad esempio eliminando o mettendo in quarantena i file dannosi e pulendo le voci dannose del Registro di sistema.
* **Aggiornamenti delle firme**: installa automaticamente le firme di protezione più recenti (definizioni di virus) per garantire che la protezione sia aggiornata in base a una frequenza predeterminata.
* **Aggiornamenti del motore antimalware**: aggiorna automaticamente il motore di Microsoft Antimalware per Azure.
* **Aggiornamenti del motore antimalware**: aggiorna automaticamente la piattaforma di Microsoft Antimalware per Azure.
* **Protezione attiva**: segnala ad Azure i metadati di telemetria sulle minacce rilevate e sulle risorse sospette per garantire una risposta rapida. e abilitare la distribuzione di firme sincrone in tempo reale tramite Microsoft Active Protection System (MAPS).
* **Creazione di report di esempi**: raccoglie e segnala al servizio Microsoft Antimalware per Azure esempi che consentono di perfezionare il servizio e di abilitare la risoluzione dei problemi.
* **Esclusioni**: consente agli amministratori di applicazioni e del servizio di configurare alcuni file, processi e unità per escluderli dalla protezione e dall'analisi per motivi di prestazioni e/o di altro tipo.
* **Raccolta di eventi antimalware**: registra l'integrità del servizio antimalware, le attività sospette e le azioni di correzione eseguite nel registro eventi del sistema operativo e le raccoglie nell'account di archiviazione di Azure dell'utente.

Per altre informazioni sul software antimalware per la protezione delle macchine virtuali:

* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](antimalware.md)
* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Windows](/azure/virtual-machines/windows/classic/install-trend)
* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Soluzioni di sicurezza in Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Per una protezione ancora più potente, è consigliabile usare [Windows Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Windows Defender ATP offre i vantaggi seguenti:

* [Riduzione della superficie di attacco](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Protezione di nuova generazione](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Protezione di endpoint e azioni di risposta](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Indagini automatizzate e azioni di correzione](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Punteggio di sicurezza](/windows/security/threat-protection/windows-defender-atp/overview-secure-score-windows-defender-advanced-threat-protection)
* [Ricerca avanzata](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gestione e API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Protezione da minacce di Microsoft](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Altre informazioni:

* [Introduzione a Windows Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Panoramica delle funzionalità di Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modulo di protezione hardware

Le protezioni con crittografia e autenticazione possono essere migliorate aumentando la sicurezza delle chiavi. È possibile semplificare la gestione e la sicurezza di chiavi e segreti critici archiviandoli nell'insieme di credenziali delle chiavi di Azure.

L'insieme di credenziali delle chiavi consente di archiviare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 livello 2. Le chiavi di crittografia di SQL Server per backup o [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) possono essere tutte archiviate nell'insieme di credenziali delle chiavi con qualsiasi chiave o segreto delle applicazioni. Le autorizzazioni e l'accesso per questi elementi protetti vengono gestiti tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Altre informazioni:

* [Cos'è l'insieme di credenziali chiave di Azure?](/azure/key-vault/key-vault-overview)
* [Blog sull'insieme di credenziali delle chiavi di Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Crittografia dischi delle macchine virtuali

Crittografia dischi di Azure è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali Windows e Linux. Crittografia dischi di Azure usa la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e il sistema operativo.

La soluzione è integrata con Azure Key Vault per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Altre informazioni:

* [Crittografia dischi di Azure per macchine virtuali IaaS](/azure/security/azure-security-disk-encryption-overview)
* [Avvio rapido: crittografare una macchina virtuale IaaS Windows con Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Backup di una macchina virtuale

Backup di Azure è una soluzione scalabile che consente di proteggere i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

Altre informazioni:

* [Informazioni su Backup di Azure](/azure/backup/backup-introduction-to-azure-backup)
* [Servizio Backup di Azure: domande frequenti](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Una parte importante della strategia BCDR dell'organizzazione è capire come mantenere in esecuzione le app e i carichi di lavoro aziendali quando si verificano interruzioni pianificate e non pianificate. Azure Site Recovery consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una posizione secondaria in caso di inattività di quella primaria.

Site Recovery:

* **Semplifica la strategia BCDR**: Site Recovery consente di gestire facilmente la replica, il failover e il ripristino di più carichi di lavoro e app aziendali da un'unica posizione. Site Recovery orchestra la replica e il failover, ma non intercetta i dati dell'applicazione né raccoglie le relative informazioni.
* **Offre una modalità di replica flessibile**: con Site Recovery, è possibile replicare i carichi di lavoro in esecuzione in macchine virtuali Hyper-V, macchine virtuali VMware e server fisici Windows o Linux.
* **Supporta il failover e il ripristino**: Site Recovery offre failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È anche possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. Dopo il failover è possibile eseguire il failback nei siti primari. Site Recovery fornisce i piani di ripristino che possono includere script e cartelle di lavoro di automazione di Azure per personalizzare il failover e il ripristino di applicazioni multilivello.
* **Elimina i data center secondari**: è possibile eseguire la replica in un sito locale secondario o in Azure. L'uso di Azure come destinazione per il ripristino di emergenza elimina i costi e la complessità correlati alla gestione di un sito secondario. I dati replicati vengono archiviati nell'archiviazione di Azure.
* **Esegue l'integrazione con tecnologie BCDR esistenti**: Site Recovery interagisce con le funzionalità BCDR delle altre applicazioni. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali. È incluso il supporto nativo per SQL Server AlwaysOn, per la gestione del failover dei gruppi di disponibilità.

Altre informazioni:

* [Che cos'è Azure Site Recovery?](/azure/site-recovery/site-recovery-overview)
* [Come funziona Azure Site Recovery?](/azure/site-recovery/site-recovery-components)
* [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>Reti virtuali

La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure.

Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni rete virtuale di Azure logica è isolata da tutte le altre reti virtuali di Azure. L'isolamento garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

Altre informazioni:

* [Panoramica della sicurezza di rete di Azure](network-overview.md)
* [Panoramica della rete virtuale](/azure/virtual-network/virtual-networks-overview)
* [Funzionalità di rete e relazioni per gli scenari aziendali](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestione e reporting dei criteri di sicurezza

Il Centro sicurezza di Azure consente di prevenire, rilevare e gestire le minacce. Il Centro sicurezza offre maggiore visibilità e controllo sulla sicurezza delle risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate e opera con un ampio ecosistema di soluzioni per la sicurezza.

Il Centro sicurezza consente di ottimizzare e monitorare la sicurezza delle macchine virtuali:

* Fornendo [consigli sulla sicurezza](/azure/security-center/security-center-recommendations) per le macchine virtuali. I suggerimenti di esempio includono: applicare gli aggiornamenti del sistema, configurare gli endpoint ACL, abilitare antimalware, abilitare i gruppi di sicurezza di rete e applicare la crittografia del disco.
* Monitorando lo stato delle macchine virtuali.

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](/azure/security-center/security-center-intro)
* [Domande frequenti sul Centro sicurezza di Azure](/azure/security-center/security-center-faq)
* [Pianificazione e gestione del Centro sicurezza di Azure](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>Conformità

Macchine virtuali di Azure ha ottenuto le certificazioni per FISMA, FedRAMP, HIPAA, PCI DSS Livello 1 e altri importanti programmi di conformità. La certificazione consente alle applicazioni di Azure di soddisfare i requisiti di conformità e all'azienda di rispondere a un'ampia gamma di requisiti normativi locali e internazionali.

Altre informazioni:

* [Centro protezione Microsoft: conformità](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Cloud attendibile: sicurezza, privacy e conformità di Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential computing

Il confidential computing non fa tecnicamente parte dell'ambito della sicurezza delle macchine virtuali. L'argomento relativo alla sicurezza delle macchine virtuali appartiene tuttavia alla categoria di più alto livello della sicurezza di calcolo, in cui è incluso anche il confidential computing.

Con il confidential computing si ha la sicurezza che i dati "in chiaro", necessari per un'elaborazione efficiente, siano protetti all'interno di un ambiente di esecuzione attendibile (https://en.wikipedia.org/wiki/Trusted_execution_environment ), noto anche come enclave, di cui è illustrato un esempio di seguito.  

Gli ambienti di esecuzione attendibili impediscono qualsiasi tentativo di visualizzare operazioni o dati interni dall'esterno, anche con un debugger. Consentono inoltre l'accesso ai dati solo a codice autorizzato. Se il codice è stato modificato o manomesso, le operazioni vengono negate e l'ambiente viene disabilitato. Gli ambienti di esecuzione attendibili applicano queste protezioni a tutto il ciclo di esecuzione del codice in essi contenuto.

Altre informazioni:

* [Introducing Azure confidential computing](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/) (Introduzione al confidential computing di Azure)  
* [Azure confidential computing](https://azure.microsoft.com/blog/azure-confidential-computing/) (Confidential computing di Azure)  
