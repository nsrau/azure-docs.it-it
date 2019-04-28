---
title: Configurare il ripristino di emergenza di macchine virtuali VMware o server fisici in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Informazioni su come configurare il ripristino di emergenza di macchine virtuali VMware o server fisici Windows e Linux in un sito secondario con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 1a61a825ce223282e85c9262c613814530ba130f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565892"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configurare il ripristino di emergenza di macchine virtuali VMware locali o server fisici in un sito secondario

InMage Scout in [Azure Site Recovery](site-recovery-overview.md) consente la replica in tempo reale tra siti VMware locali. InMage Scout è incluso nelle sottoscrizioni del servizio Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Annuncio di fine del supporto

A breve terminerà il supporto per lo scenario di Azure Site Recovery per la replica tra data center locali VMware o fisici.

-   Da agosto 2018 non è possibile configurare lo scenario nell'insieme di credenziali di Servizi di ripristino e il software InMage Scout non può essere scaricato dall'insieme di credenziali. Le distribuzioni esistenti saranno supportate. 
-   Dal 31 dicembre 2020 lo scenario non sarà più supportato.
- I partner esistenti possono caricare nuovi clienti nello scenario finché non termina il supporto.

Durante il 2018 e il 2019 verranno rilasciati due aggiornamenti: 

-   Aggiornamento 7: consente di risolvere problemi di conformità e di configurazione di rete e fornisce il supporto di TLS 1.2.
-   Aggiornamento 8: aggiunge il supporto per i sistemi operativi Linux RHEL/CentOS 7.3/7.4/7.5 e per SUSE 12

Dopo l'Aggiornamento 8 non verranno rilasciati ulteriori aggiornamenti. Vi sarà un supporto limitato a hotfix per i sistemi operativi aggiunti nell'Aggiornamento 8 e correzioni di bug basate sul massimo sforzo.

Azure Site Recovery continua a innovare, fornendo ai clienti di VMware e Hyper-V una soluzione DRaaS facile e di alta qualità con Azure come sito di ripristino di emergenza. Microsoft consiglia ai clienti esistenti di InMage / Azure Site Recovery Scout di prendere in considerazione l'uso dello scenario da VMware ad Azure di Azure Site Recovery per le proprie esigenze di continuità aziendale. Lo scenario da VMware ad Azure di Azure Site Recovery è una soluzione di ripristino di emergenza di livello aziendale per applicazioni VMware, che offre valori RPO e RTO di pochi minuti, supporto per la replica e il ripristino di applicazioni su più macchine virtuali, caricamento semplice, monitoraggio completo e un importante vantaggio per quanto riguarda il costo totale di proprietà.

### <a name="scenario-migration"></a>Migrazione dello scenario
In alternativa, è consigliabile configurare il ripristino di emergenza per le macchine virtuali VMware e i computer fisici locali, replicandoli in Azure. Procedere come segue:

1.  Esaminare il confronto rapido riportato di seguito. Prima di poter replicare i computer locali, è necessario verificare che soddisfino i [requisiti](./vmware-physical-azure-support-matrix.md#replicated-machines) per la replica in Azure. Se si replicano VM VMware, si consiglia di esaminare le [linee guida per la pianificazione della capacità](./site-recovery-plan-capacity-vmware.md) e di eseguire lo [strumento Deployment Planner](./site-recovery-deployment-planner.md) per identificare i requisiti di capacità e verificare la conformità.
2.  Dopo l'esecuzione di Deployment Planner, è possibile configurare la replica: o Per le macchine virtuali VMware, seguire queste esercitazioni per [preparare Azure](./tutorial-prepare-azure.md), [preparare l'ambiente VMware locale](./vmware-azure-tutorial-prepare-on-premises.md) e [configurare il ripristino di emergenza](./vmware-azure-tutorial-prepare-on-premises.md).
o Per i computer fisici, seguire questa [esercitazione](./physical-azure-disaster-recovery.md).
3.  Dopo che i computer vengono replicati in Azure, è possibile eseguire un'[esercitazione sul ripristino di emergenza](./site-recovery-test-failover-to-azure.md) per assicurarsi che tutto funzioni come previsto.

### <a name="quick-comparison"></a>Confronto rapido

**Funzionalità** | **Replica in Azure** |**Replica tra data center VMware**
--|--|--
**Componenti richiesti** |Servizio Mobility nei computer replicati. Server di configurazione in locale, server di elaborazione, server master di destinazione. Server di elaborazione temporaneo in Azure per il failback.|Servizio Mobility, server di elaborazione, server di configurazione e server di destinazione master
**Configurazione e orchestrazione** |Insieme di credenziali di Servizi di ripristino nel portale di Azure | Con vContinuum 
**Elementi replicati** |Disco (Windows e Linux) |Volume-Windows<br> Disco-Linux
**Cluster di dischi condivisi** |Non supportate|Supportato
**Limiti di varianza dei dati (media)** |10 MB/s di dati per disco<br> 25 MB/s di dati per VM<br> [Altre informazioni](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s di dati per disco  <br> > 25 MB/s di dati per VM
**Monitoraggio** |Dal portale di Azure|Da CX (server di configurazione)
**Matrice di supporto** | [Fare clic qui per i dettagli](./vmware-physical-azure-support-matrix.md)|[Scarica la matrice di compatibilità di Azure Site Recovery Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione:

- [Verificare](vmware-physical-secondary-support-matrix.md) i requisiti di supporto per tutti i componenti.
- Assicurarsi che i computer da replicare siano conformi al [supporto del computer replicato](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Scaricare e installare gli aggiornamenti dei componenti

 Verificare e installare gli [aggiornamenti](#updates) più recenti. Gli aggiornamenti devono essere installati nei server nell'ordine seguente:

1. Server RX (se applicabile)
2. Server di configurazione
3. Server di elaborazione
4. Server di destinazione master
5. Server vContinuum
6. Server di origine (server Windows e Linux)

Installare gli aggiornamenti come segue:

> [!NOTE]
>È possibile che la versione dell'aggiornamento dei file di tutti i componenti Scout non corrisponda a quella disponibile nel file ZIP di aggiornamento. La versione meno recente indica che non sono state apportate modifiche al componente dopo l'aggiornamento precedente a quello attuale.

Scaricare il file ZIP di [aggiornamento](https://aka.ms/asr-scout-update7) e i file di configurazione dell'[aggiornamento di MySQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade). Il file ZIP contiene tutti i file binari di base e i file binari dell'aggiornamento cumulativo dei componenti seguenti: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Estrarre i file ZIP.
  2. **Server RX**: copiare **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** nel server RX ed estrarlo. Nella cartella estratta eseguire **/Install**.
  3. **Server di configurazione e server di elaborazione**: copiare **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** nel server di configurazione e in quello di elaborazione. Fare doppio clic per eseguirlo.<br>
  4. **Server di destinazione master Windows**: per aggiornare l'agente unificato, copiare **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** nel server. Fare doppio clic per eseguirlo. Lo stesso file può essere usato anche per una nuova installazione. Lo stesso aggiornamento dell'agente unificato è applicabile anche per il server di origine.
  Non è necessario che l'aggiornamento venga applicato al server di destinazione master preparato con **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**, perché questo è un programma di installazione disponibile a livello generale che include tutte le modifiche più recenti.
  5. **Server vContinuum**:  copiare **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** nel server.  Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.
  6. **Server di destinazione master Linux**: per aggiornare l'agente unificato, copiare **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** nel server di destinazione master Linux ed estrarlo. Nella cartella estratta eseguire **/Install**.
  7. **Server di origine Windows**: per aggiornare l'agente unificato, copiare **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** nel server di origine. Fare doppio clic sul file per eseguirlo. 
  8. **Server di origine Linux**: per aggiornare l'agente unificato, copiare la versione corrispondente del file dell'agente unificato nel server Linux ed estrarlo. Nella cartella estratta eseguire **/Install**.  Esempio: per il server RHEL 6.7 a 64 bit, copiare **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** nel server ed estrarlo. Nella cartella estratta eseguire **/Install**.
  9. Dopo l'aggiornamento del server di configurazione, il server di elaborazione e il server RX con i programmi di installazione menzionati in precedenza, le librerie PHP e MySQL devono essere aggiornate manualmente con la procedura descritta nella sezione 7.4 della [guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Abilitare la replica

1. Impostare la replica tra i siti di origine e di destinazione VMware.
2. Vedere i documenti elencati di seguito per altre informazioni sull'installazione, sulla protezione e sul ripristino:

   * [Note sulla versione](https://aka.ms/asr-scout-release-notes)
   * [Matrice di compatibilità](https://aka.ms/asr-scout-cm)
   * [manuale dell'utente](https://aka.ms/asr-scout-user-guide)
   * [manuale dell'utente RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide)
   * [Aggiornamento delle librerie MYSQL e PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aggiornamenti

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Aggiornato: 31 dicembre 2018 - Scaricare [Scout Update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 è un programma di installazione completo che può essere usato per una nuova installazione o per aggiornare agenti/MT esistenti presenti in aggiornamenti precedenti (da Update 1 ad Update 6). Include tutte le correzioni apportate da Update 1 a Update 6 oltre alle nuove correzioni e ai miglioramenti descritti di seguito.
 
#### <a name="new-features"></a>Nuove funzionalità
* Conformità allo standard PCI
* Supporto di TLS v1.2

#### <a name="bug-and-security-fixes"></a>Correzioni di bug e per la sicurezza
* Corretto: il cluster Windows o i computer autonomi presentano una configurazione degli indirizzi IP non corretta dopo il ripristino/ripristino di emergenza.
* Corretto: a volte l'operazione di aggiunta disco non riesce per il cluster V2V.
* Corretto: la procedura guidata di vContinuum si blocca durante la fase di ripristino se la destinazione master è Windows Server 2016
* Corretto: i problemi di sicurezza di MySQL si attenuano aggiornando MySQL alla versione 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Aggiornamento manuale di PHP e MySQL sui server di configurazione, elaborazione e RX
La piattaforma di script PHP deve essere aggiornata alla versione 7.2.10 nei server di configurazione, elaborazione e RX.
IL sistema di gestione di database MySQL deve essere aggiornato alla versione 5.7.23 nei server di configurazione, elaborazione e RX.
Seguire i passaggi manuali forniti nel [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide) per aggiornare le versioni PHP e MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Aggiornato: 12 ottobre 2017

Scaricare [Scout Update 6](https://aka.ms/asr-scout-update6).

Scout Update 6 è un aggiornamento cumulativo. Include tutte le correzioni apportate da Update 1 a Update 5 oltre alle nuove correzioni e ai miglioramenti indicati di seguito. 

#### <a name="new-platform-support"></a>Supporto di nuove piattaforme
* È stato aggiunto il supporto per Windows Server 2016 di origine
* È stato aggiunto il supporto per i sistemi operativi Linux seguenti:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* È stato aggiunto il supporto per VMware Center 6.5

Installare gli aggiornamenti come segue:

> [!NOTE]
>È possibile che la versione dell'aggiornamento dei file di tutti i componenti Scout non corrisponda a quella disponibile nel file ZIP di aggiornamento. La versione meno recente indica che non sono state apportate modifiche al componente dopo l'aggiornamento precedente a quello attuale.

Scaricare il file ZIP dell'[aggiornamento](https://aka.ms/asr-scout-update6). Il file contiene i componenti seguenti: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bits for RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA _\<Linux del sistema operativo > _8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Estrarre i file ZIP.
  2. **Server RX**: copiare **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** nel server RX ed estrarlo. Nella cartella estratta eseguire **/Install**.
  3. **Server di configurazione e server di elaborazione**: copiare **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** nel server di configurazione e in quello di elaborazione. Fare doppio clic per eseguirlo.<br>
  4. **Server di destinazione master Windows**: Per aggiornare l'agente unificato, copiare **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** nel server. Fare doppio clic per eseguirlo. Lo stesso aggiornamento dell'agente unificato è applicabile anche per il server di origine. Se l'origine non è stata aggiornata alla versione Update 4, è consigliabile aggiornare l'agente unificato.
  Non è necessario che l'aggiornamento venga applicato al server di destinazione master preparato con **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, perché questo è un programma di installazione disponibile a livello generale che include tutte le modifiche più recenti.
  5. **Server vContinuum**:  copiare **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** nel server.  Assicurarsi che la procedura guidata vContinuum sia stata chiusa. Fare doppio clic sul file per eseguirlo.
  Non è necessario che l'aggiornamento venga applicato al server di destinazione master preparato con **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**, perché questo è un programma di installazione disponibile a livello generale che include tutte le modifiche più recenti.
  6. **Server di destinazione master Linux**: per aggiornare l'agente unificato, copiare **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** nel server di destinazione master ed estrarlo. Nella cartella estratta eseguire **/Install**.
  7. **Server di origine Windows**: Per aggiornare l'agente unificato, copiare **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** nel server di origine. Fare doppio clic sul file per eseguirlo. 
  Non è necessario installare l'agente della versione Update 5 nel server di origine se è già stato aggiornato a Update 4 o se l'agente di origine è stato installato con il programma di installazione di base più recente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Server di origine Linux**: per aggiornare l'agente unificato, copiare la versione corrispondente del file dell'agente unificato nel server Linux ed estrarlo. Nella cartella estratta eseguire **/Install**.  Esempio: per il server RHEL 6.7 a 64 bit, copiare **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** nel server ed estrarlo. Nella cartella estratta eseguire **/Install**.


> [!NOTE]
> * Il programma di installazione di base dell'agente unificato per Windows è stato aggiornato per supportare Windows Server 2016. Il nuovo programma di installazione **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** è incluso nel pacchetto Scout di base disponibile a livello generale (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Lo stesso programma di installazione verrà usato per tutte le versioni di Windows supportate. 
> * Il programma di installazione d base per Windows vContinuum e per il server di destinazione master è stato aggiornato per supportare Windows Server 2016. Il nuovo programma di installazione **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** è incluso nel pacchetto Scout di base disponibile a livello generale (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Lo stesso programma di installazione verrà usato per distribuire il server di destinazione master Windows 2016 e il server di destinazione master Windows 2012R2.
> * Windows Server 2016 su server fisico non è supportato da ASR Scout. Supporta solo Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Correzioni di bug e miglioramenti
- Gli errori di protezione failback per una VM Linux VM con un elenco di dischi da replicare sono vuoti alla fine del file di configurazione.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 è un aggiornamento cumulativo. Include tutte le correzioni apportate da Update 1 a Update 4 e le nuove correzioni indicate di seguito.
- Le correzioni da Site Recovery Scout Update 4 a Update 5 sono specifiche per i componenti di destinazione master e vContinuum.
- Se i server di origine e i server di destinazione master, di configurazione, di elaborazione e RX eseguono già Update 4, applicarlo solo nel server di destinazione master. 

#### <a name="new-platform-support"></a>Supporto di nuove piattaforme
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 a 64 bit  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** è incluso nel pacchetto Scout GA di base (**InMage_Scout_Standard_8.0.1 GA.zip**). Scaricare il pacchetto GA dal portale come descritto in Creare un insieme di credenziali.


#### <a name="bug-fixes-and-enhancements"></a>Correzioni di bug e miglioramenti

* Correzioni per una maggiore affidabilità del supporto dei cluster Windows:
    * Correzione di un problema relativo ad alcuni dischi del cluster MSCS P2V che diventavano RAW dopo il ripristino.
    * Correzione di un problema relativo al mancato ripristino del cluster MSCS P2V a causa della mancata corrispondenza dell'ordine dei dischi.
    * Correzione di un problema relativo all'aggiunta non riuscita di dischi da parte di un'operazione del cluster MSCS con un errore di dimensioni disco non corrispondenti.
    * Correzione di un problema relativo al controllo di conformità del mapping del cluster MSCS di origine con LUN RDM che non riesce durante la verifica delle dimensioni.
    * Correzione di un problema relativo alla protezione di un cluster a nodo singolo che non riesce a causa della mancata corrispondenza dell'interfaccia SCSI. 
    * Correzione di un problema relativo alla riprotezione del server del cluster Windows P2V che non riesce se sono presenti dischi del cluster di destinazione. 
    
* Correzione del problema seguente: se durante la protezione con failback il server di destinazione master selezionato non si trova nello stesso server ESXi di quello del computer di origine protetto (durante la protezione avanzata), vContinuum sceglie il server di destinazione master non corretto durante il ripristino di failback e l'operazione di ripristino non riesce.

> [!NOTE]
> * Le correzioni per il cluster P2V sono applicabili solo ai cluster MSCS fisici appena protetti con Site Recovery Scout Update 5. Per installare le correzioni per il cluster nei cluster MSCS P2V protetti con aggiornamenti precedenti, seguire i passaggi di aggiornamento descritti nella sezione 12 delle [Note sulla versione di Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Se al momento della riprotezione su ogni nodo del cluster è attivo lo stesso set di dischi che era attivo al momento della protezione iniziale, la riprotezione di un cluster MSCS fisico potrà soltanto riusare i dischi di destinazione esistenti. In caso contrario, usare i passaggi manuali illustrati nella sezione 12 delle [Note sulla versione di Site Recovery Scout](https://aka.ms/asr-scout-release-notes) per spostare i dischi sul lato di destinazione nel percorso dell'archivio dati corretto in modo da riusarli durante la riprotezione. Se si riprotegge il cluster MSCS in modalità P2V senza seguire i passaggi di aggiornamento, viene creato un nuovo disco nel server ESXi di destinazione. Sarà necessario eliminare manualmente i vecchi dischi dall'archivio dati.
> * Quando un server SLES11 o SLES11 (con qualsiasi Service Pack) di origine viene riavviato normalmente, contrassegnare manualmente le coppie di replica del disco **radice** per la risincronizzazione. Nell'interfaccia CX non viene visualizzata alcuna notifica. Se il disco radice non viene contrassegnato per la risincronizzazione è possibile che si verifichino problemi di integrità dei dati.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 è un aggiornamento cumulativo. Include tutte le correzioni apportate da Update 1 a Update 3 e le nuove correzioni indicate di seguito.

#### <a name="new-platform-support"></a>Supporto di nuove piattaforme

* È stato aggiunto il supporto per vCenter/vSphere 6.0, 6.1 e 6.2
* È stato aggiunto il supporto per questi sistemi operativi Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2
  * CentOS 7.0, 7.1 e 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 a 64 bit  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** è incluso nel pacchetto Scout GA di base **InMage_Scout_Standard_8.0.1 GA.zip**. Scaricare il pacchetto Scout GA dal portale come descritto in Creare un insieme di credenziali.

#### <a name="bug-fixes-and-enhancements"></a>Correzioni di bug e miglioramenti

* Per evitare problemi di risincronizzazione, la gestione dell'arresto è stata migliorata per i sistemi operativi e i cloni Linux seguenti:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Per Linux, tutte le autorizzazioni di accesso alle cartelle nella directory di installazione dell'agente unificato sono ora limitate solo all'utente locale.
* In Windows è stato risolto un problema di timeout che si verificava durante il rilascio di segnalibri di coerenza comuni distribuiti sulle applicazioni distribuite con carico elevato, ad esempio i cluster SQL Server e Share Point.
* Correzione relativa ai log nel programma di installazione di base del server di configurazione.
* È stato aggiunto un collegamento di download a VMware vCLI 6.0 al programma di installazione di base del server di destinazione master Windows.
* Sono stati aggiunti controlli e log aggiuntivi per le modifiche alla configurazione di rete durante le esercitazioni per il failover e il ripristino di emergenza.
* Correzione di un problema che causava la mancata segnalazione delle informazioni di conservazione al server di configurazione.  
* Per i cluster fisici è stato risolto un problema che impediva il ridimensionamento del volume nella procedura guidata di vContinuum durante l'operazione di riduzione del volume di origine.
* Una correzione di un problema di protezione del cluster che ha presentato un errore: "Impossibile trovare la firma del disco" quando il disco del cluster è un disco PRDM.
* Correzione per l'arresto anomalo del server di trasporto cxps a causa di un'eccezione relativa a un valore non compreso nell'intervallo.
* Ora è possibile ridimensionare le colonne del nome del server e dell'indirizzo IP nella pagina **Installazione push** della procedura guidata di vContinuum.
* Miglioramenti dell'API RX:
  * Sono ora disponibili i cinque punti di coerenza comuni più recenti (solo tag garantiti).
  * I dettagli relativi alla capacità e allo spazio disponibile vengono visualizzati per tutti i dispositivi protetti.
  * È disponibile lo stato del driver Scout nel server di origine.

> [!NOTE]
> * Il pacchetto di base **InMage_Scout_Standard_8.0.1_GA.zip** include:
>     * Un programma di installazione di base del server di configurazione aggiornato (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Un programma di installazione di base del server di destinazione master Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Per tutte le nuove installazioni, usare la nuova GA del server di configurazione e del server di destinazione master Windows.
> * Update 4 può essere applicato direttamente in 8.0.1 GA.
> * Non è possibile eseguire il rollback degli aggiornamenti del server di configurazione e di RX dopo che sono stati applicati.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Tutti gli aggiornamenti di Site Recovery sono cumulativi. Update 3 contiene tutte le correzioni di Update 1 e Update 2. Update 3 può essere applicato direttamente in 8.0.1 GA. Non è possibile eseguire il rollback degli aggiornamenti del server di configurazione e di RX dopo che sono stati applicati.

#### <a name="bug-fixes-and-enhancements"></a>Correzioni di bug e miglioramenti
In Update 3 sono stati risolti i problemi seguenti:

* I server di configurazione e RX non vengono registrati nell'insieme di credenziali quando si trovano dietro il proxy.
* Il numero di ore in cui l'obiettivo del punto di ripristino (RPO) non veniva raggiunto non viene aggiornato nel report sull'integrità.
* Il server di configurazione non si sincronizza con RX quando i dettagli dell'hardware ESX o della rete contengono caratteri UTF-8.
* I controller di dominio Windows Server 2008 R2 non vengono avviati dopo il ripristino.
* La sincronizzazione offline non funziona come previsto.
* Dopo il failover della macchina virtuale, l'eliminazione della coppia di replica rimane a lungo ferma nella console del server di configurazione. Gli utenti non possono completare il failback o riprendere le operazioni.
* Le operazioni complessive di creazione snapshot eseguite dal processo di coerenza sono state ottimizzate per ridurre le disconnessioni delle applicazioni, ad esempio i client di SQL Server.
* Le prestazioni dello strumento di coerenza (VACP.exe) sono state migliorate. L'uso della memoria necessaria per la creazione di snapshot in Windows è stato ridotto.
* Il servizio di installazione push si arresta in modo anomalo quando la password supera i 16 caratteri.
* vContinuum non controlla e richiede le nuove credenziali di vCenter quando queste vengono modificate.
* In Linux il gestore della cache del server di destinazione master (cachemgr) non scarica i file dal server di elaborazione. Ciò comporta la limitazione della coppia di replica.
* Quando l'ordine dei dischi fisici del cluster di failover (MSCS) non è uguale in tutti i nodi, la replica non viene impostata per alcuni volumi del cluster. Per usufruire di questa correzione, il cluster deve essere riprotetto.  
* La funzionalità SMTP non funziona come previsto dopo l'aggiornamento di RX da Scout 7.1 a Scout 8.0.1.
* Sono state aggiunte altre statistiche nel log per l'operazione di rollback per tenere traccia del tempo impiegato per completarla.
* È stato aggiunto il supporto per sistemi operativi Linux nel server di origine:
  * Red Hat Enterprise Linux (RHEL) 6 Update 7
  * CentOS 6 Update 7
* Le console del server di configurazione e di RX ora visualizzano le notifiche per la coppia, che passa alla modalità bitmap.
* In RX sono state aggiunte le correzioni per la sicurezza seguenti:
    * Bypass dell'autorizzazione tramite manomissione dei parametri: Accesso limitato agli utenti non applicabili.
    * Richiesta intersito falsa: Il concetto di token di pagina viene implementato e generato in modo casuale per ogni pagina. Questo significa che esiste una sola istanza di accesso singolo per lo stesso utente e l'aggiornamento della pagina non funziona. L'utente viene invece reindirizzato al dashboard.
    * Caricamento di file dannosi: i file sono limitati a estensioni specifiche: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
    * Scripting intersito persistente: sono state aggiunte convalide dell'input.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (aggiornamento del 3 dicembre 15)

Le correzioni apportate in Update 2 includono:

* **Server di configurazione**: problemi che impedivano il corretto funzionamento della funzionalità di misurazione gratuita per 31 giorni quando il server di configurazione veniva registrato nell'insieme di credenziali di Azure Site Recovery.
* **Agente unificato**: correzione di un problema di Update 1 a causa del quale l'aggiornamento non veniva installato nel server di destinazione master durante l'aggiornamento dalla versione 8.0 alla versione 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Update 1 include le correzioni di bug e le nuove funzionalità seguenti:

* 31 giorni di protezione gratuita per istanza del server. Consente di testare le funzionalità o impostare un modello di verifica.
* Tutte le operazioni nel server, incluse quelle di failover e failback, sono gratuite per i primi 31 giorni. Il periodo inizia quando un server viene per la prima volta protetto con Site Recovery Scout. Dal 32esimo giorno, per ogni server protetto verrà addebitata al sito del cliente la tariffa per istanza standard per la protezione di Site Recovery.
* Il numero di server protetti addebitati è disponibile in qualsiasi momento nel **Dashboard** nell'insieme di credenziali.
* È stato aggiunto il supporto per vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* È stato aggiunto il supporto per questi sistemi operativi Linux nel server di origine:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Correzioni dei bug per risolvere i problemi seguenti:
  * La registrazione nell'insieme di credenziali non riesce per il server di configurazione o RX.
  * I volumi del cluster non vengono visualizzati come previsto quando le macchine virtuali in cluster vengono riprotette al riavvio.
  * Il failback ha esito negativo quando il server di destinazione master è ospitato in un server ESXi diverso rispetto alle macchine virtuali di produzione locali.
  * Le autorizzazioni del file di configurazione vengono modificate durante l'aggiornamento a 8.0.1. Questa modifica influisce sulla protezione e sulle operazioni.
  * La soglia di risincronizzazione non viene applicata come previsto, determinando un comportamento incoerente della replica.
  * Le impostazioni RPO non vengono visualizzate correttamente nella console del server di configurazione. Il valore di dati non compressi mostra erroneamente il valore compresso.
  * L'operazione di rimozione non elimina come previsto nella procedura guidata vContinuum e la replica non viene eliminata dalla console del server di configurazione.
  * Nella procedura guidata vContinuum il disco viene deselezionato automaticamente quando si fa clic su **Dettagli** nella visualizzazione del disco durante la protezione delle macchine virtuali MSCS.
  * Nello scenario da macchina fisica a virtuale (P2V), i servizi HP obbligatori, ad esempio CIMnotify e CqMgHost, non vengono impostati su manuale nel ripristino della macchina virtuale. Questo problema determina un tempo di avvio aggiuntivo.
  * La protezione di macchine virtuali Linux ha esito negativo quando sono presenti più di 26 dischi nel server di destinazione master.

