---
title: "Usare le funzionalità di sicurezza di Azure con le macchine virtuali di Azure | Documentazione Microsoft"
description: " Panoramica delle funzionalità di sicurezza principali di Azure che possono essere usate con le macchine virtuali di Azure. Le macchine virtuali di Azure offrono la flessibilità della virtualizzazione senza la necessità di acquistare e gestire l'hardware fisico in cui è in esecuzione la VM. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9d10b9fde99867a218c4368f0be6bee00bc44dc1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Informazioni generali sulla sicurezza di Macchine virtuali di Azure
Macchine virtuali di Azure consente di distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. Grazie al supporto per Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Servizi BizTalk di Azure, è possibile distribuire qualsiasi carico di lavoro, in qualunque linguaggio, praticamente su tutti i sistemi operativi.

Una macchina virtuale di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale.  È possibile creare e distribuire le applicazioni con la certezza che i dati saranno protetti e al sicuro nei nostri data center che offrono livelli di sicurezza elevati.

Con Azure è possibile creare soluzioni conformi con sicurezza avanzata che:

* Proteggono le macchine virtuali da virus e malware
* Applicano la crittografia ai dati sensibili
* Proteggono il traffico di rete
* Identificano e rilevano minacce
* Soddisfano i requisiti di conformità

L'obiettivo di questo articolo è offrire informazioni generali sulle principali funzionalità di sicurezza di Azure per le macchine virtuali. Sono anche disponibili collegamenti ad articoli con informazioni dettagliate su ogni funzionalità.  

L'articolo descrive queste funzionalità di sicurezza principali di Macchine virtuali di Azure:

* Antimalware
* Modulo di protezione hardware
* Crittografia dischi delle macchine virtuali
* Backup di una macchina virtuale
* Azure Site Recovery
* Reti virtuali
* Gestione e reporting dei criteri di sicurezza
* Conformità

## <a name="antimalware"></a>Antimalware
Con Azure è possibile usare software antimalware di fornitori di soluzioni di sicurezza, come Microsoft, Symantec, Trend Micro e Kaspersky, per proteggere le macchine virtuali da file dannosi, adware e altre minacce. Vedere la sezione Altre informazioni di seguito per gli articoli sulle soluzioni dei partner.

Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione in tempo reale che aiuta a identificare e rimuovere virus, spyware e altro software dannoso.  Microsoft Antimalware offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure.

Microsoft Antimalware è una soluzione con un agente singolo per applicazioni e ambienti tenant, progettata per l'esecuzione in background senza intervento da parte dell'utente. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro dell'applicazione, con una configurazione sicura per impostazione predefinita o avanzata personalizzata, incluso il monitoraggio antimalware.

Quando si distribuisce e si abilita Microsoft Antimalware sono disponibili le funzionalità di base seguenti:

* Protezione in tempo reale: monitora l'attività nei servizi cloud e nelle macchine virtuali per rilevare e bloccare l'esecuzione di malware.
* Analisi pianificata: esegue periodicamente un'analisi mirata per rilevare il malware, inclusi i programmi in esecuzione attiva.
* Correzione del malware: interviene automaticamente sul malware rilevato, ad esempio eliminando o mettendo in quarantena i file dannosi e pulendo le voci dannose del Registro di sistema.
* Aggiornamenti delle firme: installa automaticamente le firme di protezione più recenti (definizioni di virus) per garantire che la protezione sia aggiornata in base a una frequenza predeterminata.
* Aggiornamenti di Antimalware Engine: aggiorna automaticamente Microsoft Antimalware Engine.
* Aggiornamenti della piattaforma antimalware: aggiorna automaticamente la piattaforma Microsoft Antimalware.
* Protezione attiva: segnala ad Azure i metadati di telemetria sulle minacce rilevate e sulle risorse sospette per garantire una risposta rapida e abilitare la distribuzione di firme sincrone in tempo reale tramite Microsoft Active Protection System (MAPS).
* Creazione di report di esempi: raccoglie e segnala al servizio Microsoft Antimalware esempi che consentono di perfezionare il servizio e di abilitare la risoluzione dei problemi.
* Esclusioni: consente agli amministratori di applicazioni e del servizio di configurare alcuni file, processi e unità per escluderli dalla protezione e dall'analisi per motivi di prestazioni e/o di altro tipo.
* Raccolta di eventi antimalware: registra l'integrità del servizio antimalware, le attività sospette e le azioni di correzione eseguite nel registro eventi del sistema operativo e le raccoglie nell'account di archiviazione di Azure del cliente.

Altre informazioni: per altre informazioni sul software antimalware per la protezione delle macchine virtuali, vedere le risorse seguenti.

* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](azure-security-antimalware.md)
* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Windows](../virtual-machines/windows/classic/install-trend.md)
* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Soluzioni di sicurezza in Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Modulo di protezione hardware
Le protezioni con crittografia e autenticazione possono essere migliorate aumentando la sicurezza delle chiavi. È possibile semplificare la gestione e la sicurezza di chiavi e segreti critici archiviandoli nell'insieme di credenziali delle chiavi di Azure. L'insieme di credenziali delle chiavi consente di archiviare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 livello 2. Le chiavi di crittografia di SQL Server per backup o [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) possono essere tutte archiviate nell'insieme di credenziali delle chiavi con qualsiasi chiave o segreto delle applicazioni. Le autorizzazioni e l'accesso per questi elementi protetti vengono gestiti tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Altre informazioni:

* [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/key-vault-whatis.md)
* [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md)
* [Blog sull'insieme di credenziali delle chiavi di Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Crittografia dischi delle macchine virtuali
Crittografia dischi di Azure è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali di Azure in Windows e Linux. Crittografia dischi di Azure usa la funzionalità standard di settore [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per i dischi dati e del sistema operativo.

La soluzione è integrata con l'insieme di credenziali delle chiavi di Azure per facilitare il controllo e la gestione dei segreti e delle chiavi di crittografa del disco nella sottoscrizione dell'insieme di credenziali delle chiavi, assicurando allo stesso tempo che tutti i dati inattivi sui dischi delle macchine virtuali siano crittografati nell'archiviazione di Azure.

Altre informazioni:

* [Azure Disk Encryption per le macchine virtuali IaaS Windows e Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Crittografia dischi di Azure per le macchine virtuali di Windows e Linux](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Crittografare una macchina virtuale](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Backup di una macchina virtuale
Backup di Azure è una soluzione scalabile che protegge i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

Altre informazioni:

* [Informazioni su Backup di Azure](../backup/backup-introduction-to-azure-backup.md)
* [Percorso di apprendimento di Backup di Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Servizio Backup di Azure: Domande frequenti](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Una parte importante della strategia BCDR dell'organizzazione è capire come mantenere in esecuzione le app e i carichi di lavoro aziendali quando si verificano interruzioni pianificate e non pianificate. Azure Site Recovery consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una località secondaria in caso di inattività di quella primaria.

Site Recovery:

* **Semplifica la strategia BCDR** : Site Recovery consente di gestire la replica, il failover e il ripristino di più carichi di lavoro e app aziendali da un'unica posizione. Site Recovery orchestra la replica e il failover, ma non intercetta i dati dell'applicazione né raccoglie le relative informazioni.
* **Offre una modalità di replica flessibile** : con Site Recovery è possibile replicare i carichi di lavoro in esecuzione in macchine virtuali Hyper-V, macchine virtuali VMware e server fisici Windows o Linux.
* **Supporta failover e ripristino** : Site Recovery consente il failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È anche possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. Dopo il failover è possibile eseguire il failback nei siti primari. Site Recovery fornisce i piani di ripristino che possono includere script e cartelle di lavoro di automazione di Azure per personalizzare il failover e il ripristino di applicazioni multilivello.
* **Elimina il data center secondario** : è possibile eseguire la replica in un sito secondario locale o in Azure. L'uso di Azure come destinazione per il ripristino di emergenza elimina i costi e la complessità correlati alla gestione di un sito secondario. I dati replicati vengono archiviati nell'archiviazione di Azure.
* **Si integra con le tecnologie BCDR esistenti** : Site Recovery interagisce con altre funzionalità BCDR dell'applicazione. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali. È incluso il supporto nativo per SQL Server AlwaysOn, per la gestione del failover dei gruppi di disponibilità.

Altre informazioni:

* [Che cos'è Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Funzionamento di Azure Site Recovery](../site-recovery/site-recovery-components.md)
* [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Reti virtuali
La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure. Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni rete virtuale di Azure logica è isolata da tutte le altre reti virtuali di Azure. L'isolamento garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

Altre informazioni:

* [Panoramica della sicurezza di rete di Azure](security-network-overview.md)
* [Panoramica di Rete virtuale.](../virtual-network/virtual-networks-overview.md)
* [Funzionalità di rete e relazioni per gli scenari aziendali](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestione e reporting dei criteri di sicurezza
Il Centro sicurezza di Azure aiuta a impedire, rilevare e rispondere alle minacce offrendo visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza di Azure consente di ottimizzare e monitorare la sicurezza delle macchine virtuali offrendo:

* [Consigli sulla sicurezza](../security-center/security-center-recommendations.md) per le macchine virtuali, ad esempio come applicare gli aggiornamenti del sistema, configurare gli endpoint ACL, abilitare antimalware, abilitare i gruppi di sicurezza di rete e applicare la crittografia del disco.
* Il monitoraggio dello stato delle macchine virtuali

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](../security-center/security-center-intro.md)
* [Domande frequenti sul Centro sicurezza di Azure](../security-center/security-center-faq.md)
* [Pianificazione e gestione del Centro sicurezza di Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformità
Macchine virtuali di Azure ha ottenuto le certificazioni per FISMA, FedRAMP, HIPAA, PCI DSS Livello 1 e altri importanti programmi di conformità. La certificazione consente alle applicazioni di Azure di soddisfare i requisiti di conformità e all'azienda di rispondere a un'ampia gamma di requisiti normativi locali e internazionali.

Altre informazioni:

* [Centro protezione Microsoft - Conformità](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Trusted Cloud: sicurezza, privacy e conformità di Microsoft Azure](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
