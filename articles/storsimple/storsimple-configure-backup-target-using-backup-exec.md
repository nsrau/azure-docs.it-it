---
title: Configurare StorSimple con Veritas Backup Exec | Microsoft Docs
description: Descrive la configurazione della destinazione di backup StorSimple con Veritas Backup Exec.
services: storsimple
documentationcenter: 
author: hkanna
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: b7c6a9ffb2c598048bf0547420b1e7afac2c3e74
ms.openlocfilehash: 7a2fa4ccac6b2eda935477034c5f58a0ed90da37

---

# <a name="configure-storsimple-with-veritas-backup-exec8482"></a>Configurare StorSimple con Veritas Backup Exec&#8482;

## <a name="overview"></a>Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che risolve le complessità date dalla crescita esponenziale dei dati. Questa soluzione usa un account di archiviazione di Azure come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra archiviazione locale e archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veritas Backup Exec e le procedure consigliate per l'integrazione di entrambe le soluzioni. Vengono anche presentate alcune indicazioni su come configurare Veritas Backup Exec per integrarsi al meglio con StorSimple. Per informazioni su come configurare al meglio Veritas Backup Exec per soddisfare le esigenze di backup e i contratti di servizio, fare riferimento alle procedure consigliate di Veritas, agli amministratori e ai progettisti di backup.

Questo articolo illustra i concetti chiave e i passaggi di configurazione, ma non costituisce in alcun modo una guida dettagliata di configurazione o di installazione. Nell'articolo si presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

## <a name="why-storsimple-as-a-backup-target"></a>Perché usare StorSimple come destinazione di backup?

StorSimple è una destinazione di backup ideale per i motivi seguenti:

-   Fornisce l'archiviazione locale standard per le applicazioni di backup, da usare senza apportare modifiche per garantire una rapida destinazione di backup. StorSimple è anche disponibile per il ripristino rapido dei backup recenti.

-   La suddivisione in livelli nel cloud è perfettamente integrata con l'economica soluzione di archiviazione di Microsoft Azure.

-   Fornisce automaticamente l'archiviazione esterna per il ripristino di emergenza.


## <a name="target-audience"></a>Destinatari

I destinatari di questo documento includono gli amministratori di backup, gli amministratori e i progettisti di archiviazione con conoscenza di archiviazione, Windows Server 2012 R2, Ethernet, servizi cloud e Veritas Backup Exec.

## <a name="supported-versions"></a>Versioni supportate

Per un elenco completo delle versioni supportate, andare a:

-   [Backup Exec versione 16 e versioni successive](http://backupexec.com/compatibility).

-   [StorSimple Update 3 e versioni successive](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Concetti chiave

Come con qualsiasi altra soluzione di archiviazione, per ottenere un esito positivo è fondamentale un'attenta valutazione delle esigenze relative alle prestazioni di archiviazione, ai contratti di servizio, alla frequenza di modifica e alla crescita di capacità della soluzione. L'idea principale è che con l'introduzione di un livello cloud, i tempi e le velocità effettive di accesso al cloud hanno un ruolo fondamentale nella capacità di StorSimple di soddisfare le esigenze dell'utente.

StorSimple è progettato per garantire l'archiviazione per le applicazioni che usano un working set di dati ben definito (dati attivi). In questo modello il working set di dati viene archiviato nei livelli locali e il rimanente set di dati non in funzione, poco attivi o archiviati viene suddiviso in livelli nel cloud. Questo modello è illustrato nella figura seguente. La linea verde quasi piatta rappresenta i dati archiviati nei livelli locali del dispositivo StorSimple. La linea rossa rappresenta la quantità totale di dati archiviati nella soluzione StorSimple in tutti i livelli. Lo spazio tra la linea verde piatta e la curva esponenziale rossa rappresenta la quantità totale di dati archiviati nel cloud.

**Suddivisione in livelli di StorSimple**
![Diagramma di suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Tenendo presente questa architettura, si noterà che StorSimple è particolarmente adatto a essere usato come destinazione di backup. StorSimple consente di eseguire le operazioni seguenti:

-   Eseguire le operazioni di ripristino più frequenti dal working set di dati locale.

-   Usare il cloud per il ripristino di emergenza esterno e per i dati meno recenti per i quali i ripristini sono meno frequenti.

## <a name="storsimple-benefits"></a>Vantaggi di StorSimple

StorSimple rappresenta una soluzione locale che si integra facilmente con Microsoft Azure, sfruttando l'accesso automatico all'archiviazione locale e cloud.

StorSimple usa la suddivisione automatica in livelli tra il dispositivo locale, contenente il dispositivo a stato solido (Solid State Drive, SSD) e l'archiviazione SAS (Serial-attached SCSI), e Archiviazione di Azure. La suddivisione automatica in livelli mantiene in locale, nei livelli SSD e SAS, i dati a cui si accede di frequente e sposta in Archiviazione di Azure i dati a cui si accede raramente.

StorSimple offre i vantaggi seguenti:

-   Algoritmi di deduplicazione e compressione univoci che usano il cloud per ottenere livelli di deduplicazione senza precedenti

-   Disponibilità elevata

-   Replica geografica usando la replica geografica di Azure

-   Integrazione di Azure

-   Crittografia dei dati nel cloud

-   Miglioramento del ripristino di emergenza e della conformità

Sebbene StorSimple presenti due scenari di distribuzione principali (destinazione di backup primaria e secondaria), si tenga presente che fondamentalmente si tratta di un normale dispositivo di archiviazione a blocchi. StorSimple esegue tutte le operazioni di compressione e deduplicazione e invia e recupera facilmente i dati dal cloud all'applicazione e al file system.

Per altre informazioni su StorSimple, vedere [Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida](storsimple-overview.md) e rivedere le [Specifiche e conformità tecniche per il dispositivo StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Il dispositivo StorSimple come destinazione di backup è supportato solo con StorSimple 8000 Update 3 o versione successiva.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Le tabelle seguenti contengono le indicazioni iniziali da modello ad architettura per il dispositivo.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacità di StorSimple per l'archiviazione locale e cloud


| Capacità di archiviazione       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Capacità di archiviazione locale | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacità di archiviazione cloud | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Le dimensioni di archiviazione si intendono senza alcuna deduplicazione o compressione.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacità di StorSimple per i backup primario e secondario


| Scenario di backup  | Capacità di archiviazione locale                                         | Capacità di archiviazione cloud                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Backup primario   | Backup recenti archiviati nell'archivio locale per il ripristino rapido (RPO) | La cronologia dei backup (RPO) rientra nella capacità del cloud |
| Backup secondario | La copia secondaria dei dati di backup può essere archiviata nella capacità del cloud  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione di backup primaria

In questo scenario i volumi StorSimple vengono presentati all'applicazione di backup come unico repository per i backup. La figura seguente illustra l'architettura della soluzione in cui tutti i backup usano i volumi StorSimple a livelli per i backup e i ripristini.

![Diagramma logico con StorSimple come destinazione primaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione primaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.

2.  Il server di backup scrive i dati nei volumi StorSimple a livelli.

3.  Il server di backup aggiorna il database del catalogo e completa il processo di backup.

4.  Lo script di snapshot attiva la gestione di snapshot cloud di StorSimple (start-delete).

5.  A seconda dei criteri di conservazione, il server di backup elimina i backup scaduti.

###  <a name="primary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione primaria

1.  Il server di backup avvia il ripristino dei dati appropriati dal repository di archiviazione.

2.  L'agente di backup riceve i dati dal server di backup.

3.  Il server di backup completa il processo di ripristino.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione secondaria di backup

In questo scenario i volumi StorSimple vengono usati principalmente per la conservazione o l'archiviazione a lungo termine.

La figura seguente illustra l'architettura in cui i backup e i ripristini iniziali scelgono come destinazione un volume a prestazioni elevate. Questi backup vengono copiati e archiviati in un volume StorSimple a livelli in una determinata pianificazione.

È importante impostare le dimensioni del volume a prestazioni elevate con spazio e prestazioni sufficienti a gestire i requisiti delle prestazioni e della capacità dei criteri di conservazione.

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione secondaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.

2.  Il server di backup scrive i dati nell'archiviazione a prestazioni elevate.

3.  Il server di backup aggiorna il database del catalogo e completa il processo di backup.

4.  A seconda dei criteri di conservazione, il server di backup copia i backup in StorSimple.

5.  Lo script di snapshot attiva la gestione di snapshot cloud di StorSimple (start-delete).

6.  A seconda dei criteri di conservazione, il server di backup elimina i backup scaduti.

### <a name="secondary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione secondaria

1.  Il server di backup avvia il ripristino dei dati appropriati dal repository di archiviazione.

2.  L'agente di backup riceve i dati dal server di backup.

3.  Il server di backup completa il processo di ripristino.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

La distribuzione di questa soluzione è costituita da tre passaggi: preparazione dell'infrastruttura di rete, distribuzione del dispositivo StorSimple come destinazione di backup e distribuzione di Veritas Backup Exec. Ognuno dei passaggi precedenti viene illustrato nel dettaglio nelle sezioni seguenti.

### <a name="configure-the-network"></a>Configurare la rete

StorSimple come soluzione integrata con il cloud di Azure richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per garantire prestazioni ottimali nell'uso della soluzione, è consigliabile rispettare le procedure consigliate di rete riportate di seguito:

-   Il collegamento che connette la suddivisione in livelli di StorSimple ad Azure deve soddisfare i requisiti di larghezza di banda tramite l'applicazione della Qualità del servizio (QoS) corretta alle opzioni dell'infrastruttura, in modo che rispettino i contratti di servizio RPO/RTO.

-   Le latenze massime di accesso ad Archiviazione BLOB di Azure devono essere comprese nell'intervallo di 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veritas-backup-exec"></a>Distribuire Veritas Backup Exec

Per le procedure consigliate per l'installazione di Veritas Backup Exec, consultare [Best practices for Backup Exec installation](https://www.veritas.com/support/en_US/article.000068207) (Procedure consigliate per l'installazione di Backup Exec).

## <a name="configure-the-solution"></a>Configurare la soluzione

In questa sezione vengono descritti alcuni esempi di configurazione. Gli esempi e i suggerimenti seguenti illustrano l'implementazione più semplice ed essenziale. Questa implementazione potrebbe non essere applicabile direttamente ai requisiti specifici di backup dell'utente.

### <a name="configure-storsimple"></a>Configurare StorSimple

| Attività di distribuzione di StorSimple                                                                                                                 | Commenti aggiuntivi                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Distribuire un dispositivo StorSimple locale.                                                                                                | Versione supportata: Update 3 e versioni successive.                                                                                                                                                                                                                                                                 |
| Abilitare la modalità di destinazione di backup.                                                                                                                   | Usare i comandi seguenti per abilitare o disabilitare la modalità e ottenere lo stato. Per altre informazioni, vedere [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md).</br> Abilitare la modalità di backup:`Set-HCSBackupApplianceMode -enable`</br>  Disabilitare la modalità di backup:`Set-HCSBackupApplianceMode -disable`</br> Stato corrente delle impostazioni di modalità di backup`Get-HCSBackupApplianceMode` |
| Creare un contenitore comune per il volume che archivia i dati di backup.   Tutti i dati contenuti in un contenitore di volumi Vengono deduplicati. | I contenitori di volumi StorSimple definiscono i domini di deduplicazione.                                                                                                                                                                                                                                             |
| Creazione di volumi StorSimple                                                                                                                 | Creare volumi con dimensioni il più possibile simili all'uso previsto in quanto le dimensioni dei volumi possono influire sulla durata dello snapshot cloud. Per altre informazioni su come impostare le dimensioni di un volume, passare a [Criteri di conservazione](#retention-policies).</br> </br> Usare i volumi StorSimple a livelli e selezionare **Usare questo volume per i dati di archivio a cui si accede non di frequente**. </br> I volumi solo aggiunti in locale non sono supportati.|
| Creare un criterio di backup di StorSimple univoco per tutti i volumi della destinazione di backup.                                                               | Un criterio di backup di StorSimple definisce il gruppo di coerenza del volume.                                                                                                                                                                                                                                       |
| Disabilitare la pianificazione alla scadenza degli snapshot.                                                                                                    | Gli snapshot vengono attivati come operazione di post-elaborazione.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |




### <a name="configure-host-backup-server-storage"></a>Configurare l'archiviazione del server di backup host

Assicurarsi che l'archiviazione del server di backup host sia configurata in base alle linee guida seguenti.  

- I volumi con spanning, creati tramite il servizio di gestione dischi di Windows, non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione dell'unità di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server Veeam.
    - Usare iSCSI in caso di server fisici.
    - Usare dischi pass-through per i server virtuali.


## <a name="best-practices-for-storsimple-and-veritas-backup-exec"></a>Procedure consigliate per StorSimple e Veritas Backup Exec

Configurare la soluzione in base alle linee guida seguenti.


### <a name="operating-system"></a>Sistema operativo

-   Disabilitare la crittografia di Windows Server e la deduplicazione per il file system NTFS.

-   Disabilitare la deframmentazione di Windows Server sui volumi StorSimple.

-   Disabilitare l'indicizzazione di Windows Server sui volumi StorSimple.

-   Eseguire una scansione antivirus dell'host di origine (non nei volumi StorSimple).

-   Disabilitare la [manutenzione di Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) predefinita in Gestione attività.

    - Disabilitare lo strumento di configurazione della manutenzione nell'Utilità di pianificazione attività di Windows.

        oppure

    - Scaricare: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - Dopo aver scaricato PSEXEC, eseguire Windows PowerShell come amministratore e digitare:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Assicurarsi che il dispositivo StorSimple sia aggiornato alla versione [Update 3 o successiva](storsimple-install-update-3.md).

-   Isolare il traffico iSCSI e cloud. Usare connessioni iSCSI dedicate per il traffico tra StorSimple e il server di backup.

-   Assicurarsi che il dispositivo StorSimple sia una destinazione di backup dedicata. I carichi di lavoro misti non sono supportati in quanto influiscono sugli obiettivi RTO/RPO.

### <a name="veritas-backup-exec"></a>Veritas Backup Exec

-   Veritas Backup Exec deve essere installato su un'unità locale del server e non su un volume di StorSimple.

-   Impostare le **operazioni di scrittura simultanea** dell'archiviazione di Veritas Backup Exec sul valore massimo consentito.

    -   **Le dimensioni del buffer e del blocco** di archiviazione di Veritas Backup Exec devono essere impostate su 512 KB.

    -   La **lettura e scrittura in buffering** dell'archiviazione di Veritas Backup Exec devono essere abilitate.

-   I backup completi e incrementali di Veritas Backup Exec sono supportati mentre i backup sintetici e differenziali non sono raccomandati.

-   I file dei dati di backup devono contenere solo dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta tra diversi processi.

-   Disabilitare la verifica dei processi. Se necessario, pianificare la verifica dopo l'ultimo processo di backup. È importante tenere presente che questo processo influisce sulla finestra di backup.

-   In **Archiviazione > Disco > Dettagli > Proprietà**, disabilitare **Preallocate disk space** (Prealloca spazio del disco).

Consultare la documentazione di Veritas Backup Exec per le ultime impostazioni di Veritas Backup Exec e le procedure consigliate per implementare questi requisiti, visitando il sito [www.veritas.com](https://www.veritas.com).

## <a name="retention-policies"></a>Criteri di conservazione

Uno dei criteri di conservazione di backup più usati è GFS (Grandfather, Father e Son). In questo criterio viene eseguito ogni giorno un backup incrementale. I backup completi vengono eseguiti con frequenza settimanale e mensile. Questo criterio genera 6 volumi StorSimple a livelli.

-   Un volume contiene i criteri di conservazione settimanali, mensili e annuali completi.

-   Gli altri 5 volumi archiviano i backup incrementali giornalieri.

Nell'esempio seguente si usa una rotazione GFS. Nell'esempio si presuppone quanto segue:

-   Vengono usati dati compressi o non duplicati.

-   I backup completi hanno dimensione di 1 TiB ciascuno.

-   I backup incrementali giornalieri hanno dimensione di 500 GiB ciascuno.

-   4 backup settimanali conservati per un mese.

-   12 backup mensili conservati per un anno.

-   1 backup annuale conservato per 10 anni.

In base i presupposti precedenti creare un volume StorSimple a livelli a 26 TiB per i backup mensili e annuali completi. Creare un volume StorSimple a livelli a 5 TiB per ciascuno dei backup incrementali giornalieri.

| Conservazione per tipo di backup | Dimensioni TiB | Moltiplicatore GFS\*                                       | Capacità totale TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Settimanale completo           | 1        | 4                                                      | 4                           |
| Incrementale giornaliero     | 0,5      | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Mensile completo          | 1        | 12                                                     | 12                          |
| Annuale completo           | 1        | 10                                                     | 10                          |
| Requisito GFS       |          |                                                        | 38                          |
| Quota aggiuntiva      | 4        |                                                        | 42 requisito GFS totale   |

\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i criteri di backup.

## <a name="configure-veritas-backup-exec-storage"></a>Configurare l'archiviazione di Veritas Backup Exec

1.  Nella console di gestione Veritas Backup Exec selezionare **Storage&gt; (archiviazione ) Configure Storage (Configura archiviazione) &gt; Disk-Based Storage (Archiviazione basata su disco)**. Fare clic su **Avanti&gt;**.

    ![Console di gestione Veritas Backup Exec, schermata di configurazione dell'archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

1.  Fare clic su **Disk Storage** (Archiviazione su disco) e procedere alla pagina successiva.

    ![Console di gestione Veritas Backup Exec, schermata di selezione dell'archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

1.  Inserire un nome rappresentativo, ad esempio, Sabato completo e una descrizione. Procedere alla pagina successiva.

    ![Console di gestione Veritas Backup Exec, Nome e schermata della descrizione](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

1.  Selezionare il disco appropriato e fare clic su **Next &gt; (Avanti)**. Ad esempio, è stato selezionato D.

    ![Console di gestione Veritas Backup Exec, schermata di selezione del disco di archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

1.  Aumentare il numero di operazioni di scrittura fino a 16 e fare clic su **Next &gt; (Avanti)**.

    ![Console di gestione Veritas Backup Exec, schermata delle impostazioni per le operazioni di scrittura simultanee ](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

1.  Rivedere le impostazioni e fare clic su **Finish** (Fine).

    ![Console di gestione Veritas Backup Exec, schermata di riepilogo della configurazione dell'archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

1.  Alla fine di ogni assegnazione, modificare le impostazioni del dispositivo di archiviazione in modo che corrispondano a quelle consigliate nell'elenco di procedure consigliate.

    ![Console di gestione Veritas Backup Exec, schermata delle impostazioni del dispositivo di archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

1.  Ripetere tutti i passaggi precedenti fino al termine dell'assegnazione dei volumi StorSimple a Veritas Backup Exec.

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione di backup primaria

> [!NOTE]
> Tenere presente che se è necessario ripristinare i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

Nella figura seguente viene illustrato il mapping di un volume tipico per un processo di backup. In questo caso tutti i backup settimanali vengono mappati nel disco completo di sabato e i backup incrementali vengono mappati in dischi incrementali dal lunedì al venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume a più livelli StorSimple.

![Diagramma logico di configurazione della destinazione primaria di backup ](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione primaria di backup

| Pianificazione di rotazione GFS per 4 settimane, mensile e annuale |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Tipo di backup o frequenza   | Completa          | Incrementale (giorno 1-5)  |
| Settimanale (settimana 1-4)    | Sabato | Da lunedì a venerdì | 
| Mensile     | Sabato  |             |
| Annuale      | Sabato  |             |


### <a name="assigning-storsimple-volumes-to-a-veritas-backup-exec-backup-job"></a>Assegnazione di volumi StorSimple a un processo di backup Veritas Backup Exec.

Nella sequenza seguente si presuppone che Veritas Backup Exec, l'host di destinazione, sia configurato in conformità alle linee guida dell'agente Veritas Backup Exec.

1.  Nella console di gestione Veritas Backup Exec andare a **Host &gt; Backup &gt; Backup su disco &gt;**.

    ![Nella console di gestione Veritas Backup Exec selezionare Host, Backup, Backup su disco](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

1.  Nella finestra delle proprietà di definizione di Backup selezionare **Edit** (Modifica) in Backup.

    ![Finestra delle proprietà di definizione di Backup selezionare Edit (Modifica)](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

1.  Configurare i backup completi e incrementali in modo che soddisifno i requisiti di RPO/RTO e siano conformi alle procedure consigliate di Veritas.

2.  Nella finestra delle opzioni di Backup selezionare **Storage** (Archiviazione).

    ![Scheda archiviazione opzioni di Backup](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

1.  Assegnare i volumi StorSimple corrispondenti alla pianificazione dei backup.

    > [!NOTE]
    > **Compression** (Compressione) e **Encryption type** (Tipo di crittografia) sono impostati su **None** (Nessuno).

2.  In **Verify** (Verifica) selezionare il **Do not verify data for this job** (Non verificare i dati per questo processo) poiché l'opzione potrebbe influire sulla suddivisione in livelli di StorSimple.

    > [!NOTE]
    > La deframmentazione, l'indicizzazione e la verifica in background compromettono la suddivisione in livelli di StorSimple.

    ![Impostazioni di verifica delle opzioni di Backup](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

1.  Dopo aver configurato il resto delle opzioni di backup per soddisfare i requisiti, selezionare **OK** al termine.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione secondaria di backup

> [!NOTE]
> Tenere presente che se è necessario ripristinare i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. È ad esempio possibile usare un volume RAID per contenere spazi, I/O e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

Nella figura seguente viene illustrato tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo caso tutti i backup vengono eseguiti nel volume RAID locale (per il server). Questi backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo da gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Esempio GFS con StorSimple come destinazione secondaria di backup

![Diagramma logico con StorSimple come destinazione secondaria di backup ](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Nella tabella seguente viene illustrato come configurare i backup per l'esecuzione in dischi di StorSimple e locali, inclusi i requisiti di capacità singoli e totali.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configurazione di backup e requisiti di capacità

| Tipo di backup e conservazione                    |Archiviazione configurata| Dimensioni (TiB) | Moltiplicatore GFS | Capacità totale (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Settimana 1 (completo e incrementale) |Disco locale (breve termine)| 1        | 1              | 1           |
| StorSimple Settimana 2-4           |Disco StorSimple (lungo termine) | 1        | 4              | 4                   |
| Mensile completo                                 |Disco StorSimple (lungo termine) | 1        | 12             | 12                   |
| Annuale completo                               |Disco StorSimple (lungo termine) | 1        | 1              | 1                   |
|Requisiti di dimensione dei volumi GFS | |          |                | 18*|

\* la capacità totale include i 17 TiB dei dischi StorSimple e 1 TiB del volume RAID locale.


#### <a name="gfs-example-schedule"></a>Pianificazione di esempio GFS

|Pianificazione della rotazione GFS settimanale, mensile e annuale|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Frequenza                                                                     | Completa               | Incrementale Giorno 1        | Incrementale Giorno 2        | Incrementale Giorno 3        | Incrementale Giorno 4        | Incrementale Giorno 5        |
| Settimana 1                                                                   | Volume RAID locale  | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale |
| Settimana 2                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 3                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 4                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Mensile                                                                  | StorSimple Mensile |                   |                   |                   |                   |                   |
| Annuale                                                                   | StorSimple Annuale  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-backup-exec-archivededuplication-job"></a>Assegnazione di volumi StorSimple a un processo di archiviazione/duplicazione di Backup Exec

1.  Dopo aver configurato il processo di backup iniziale, per usare il volume RAID come destinazione di backup primaria, passare alla console di gestione Veritas Backup Exec. Selezionare il processo che si desidera archiviare in un volume StorSimple, fare clic con il pulsante destro del mouse e selezionare **Backup Definition Properties** (Proprietà definizione Backup). Fare clic su **Modifica**.

    ![Console Backup Exec, scheda proprietà definizioni backup](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

1.  Selezionare **Add Stage** (Aggiungi fase) e nell'elenco a discesa selezionare **Duplicate to Disk** (Duplica su disco). Fare clic su **Modifica**.

    ![Console Backup Exec, aggiungi fase definizioni backup](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

2.  In **Duplicate Options** (Opzioni di duplicazione) selezionare un valore appropriato per **Source** (Origine) e **Schedule** (Pianificazione).

    ![Console Backup Exec, proprietà definizione Backup, opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

1.  Dall'elenco a discesa **Storage** (Archiviazione) selezionare il volume di StorSimple in cui si desidera che il processo di archiviazione memorizzi i dati. In questo caso, l'origine è impostata su **Full** (Completo) e la destinazione sul volume di archiviazione completo di StorSimple.

    ![Console Backup Exec, proprietà definizione Backup, opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

1.  Passare a **Verify** (Verifica) e selezionare **Do not verify data for this job** (Non verificare i dati per questo processo).

    ![Console Backup Exec, proprietà definizione Backup, opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

1.  Fare clic su **OK**.

    ![Console Backup Exec, proprietà definizione Backup, opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

1.  Nella colonna **Backup** aggiungere una nuova fase. Scegliere l'origine come incrementale e la destinazione come volume di StorSimple in cui viene archiviato il processo di backup incrementale. Ripetere i passaggi precedenti.

## <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Ciò equivale alla spedizione di nastri a una struttura esterna e, se si usa l'archiviazione con ridondanza geografica di Azure (GRS), equivale alla spedizione di nastri a più siti. Se è stato necessario un ripristino del dispositivo in una situazione di emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.

La sezione seguente illustra come creare un breve script per attivare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup.

> [!NOTE]
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Avviare ed eliminare gli snapshot cloud con uno script

> [!NOTE]
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, fare riferimento alla documentazione di Veritas Backup Exec.

#### <a name="backup-lifecycle"></a>Ciclo di vita del backup

![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

#### <a name="requirements"></a>Requirements:

-   Il server che esegue lo script deve avere accesso al cloud Azure.

-   L'account utente deve disporre delle autorizzazioni necessarie.

-   Un criterio di Backup StorSimple con i volumi StorSimple associati configurato ma non abilitato.

-   Nome della risorsa StorSimple, chiave di registrazione, nome del dispositivo e ID dei criteri di backup.

#### <a name="steps"></a>Passaggi:

1.  [Installare Azure PowerShell](/powershell-install-configure/).

2.  [Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione](https://msdn.microsoft.com/library/dn385850.aspx)

3.  Nel portale di Azure classico ottenere il nome della risorsa e la [chiave di registrazione per il servizio StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  Nel server che esegue lo script eseguire Windows PowerShell come amministratore. Digitare:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Prendere nota dell'ID dei criteri di backup.

5.  Nel blocco note creare un nuovo Script di Windows PowerShell e salvarlo nella stessa posizione in cui sono state salvate le impostazioni di pubblicazione di Azure. Ad esempio: `C:\\CloudSnapshot\\StorSimpleCloudSnapshot.ps1`.

    Copiare e incollare il frammento di codice seguente:

    ```powershell
    Import-AzurePublishSettingsFile "c:\\CloudSnapshot Snapshot\\myAzureSettings.publishsettings"
    Disable-AzureDataCollection
    $ApplianceName = <myStorSimpleApplianceName>
    $RetentionInDays = 20
    $RetentionInDays = -$RetentionInDays
    $Today = Get-Date
    $ExpirationDate = $Today.AddDays($RetentionInDays)
    Select-AzureStorSimpleResource -ResourceName "myResource" –RegistrationKey
    Start-AzureStorSimpleDeviceBackupJob –DeviceName $ApplianceName -BackupType CloudSnapshot -BackupPolicyId <BackupId> -Verbose
    $CompletedSnapshots =@()
    $CompletedSnapshots = Get-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName
    Write-Host "The Expiration date is " $ExpirationDate
    Write-Host

    ForEach ($SnapShot in $CompletedSnapshots)
    {
    $SnapshotStartTimeStamp = $Snapshot.CreatedOn
    if ($SnapshotStartTimeStamp -lt $ExpirationDate)

        {
        $SnapShotInstanceID = $SnapShot.InstanceId
        Write-Host "This snapshotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
        Write-Host "Instance ID " $SnapShotInstanceID
        Write-Host "This snpashotdate is older and needs to be deleted"
        Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
        Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }   
    ```

1.  Aggiungere lo script al processo di backup in Veritas Backup Exec, modificando i comandi di pre-post delle opzioni del processo Veritas Backup Exec.

    ![Console di Backup Exec, opzioni di backup, scheda dei comandi Pre/post](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple alla fine del processo di backup giornaliero come script di post-elaborazione. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO/RTO, consultare il progettista del backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino

I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. Quando si ripristinano i dati suddivisi in più livelli nel cloud, ripristini vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini si verificano alla velocità del disco locale del dispositivo. Per informazioni su come eseguire un ripristino, fare riferimento alla documentazione di Veritas Backup Exec e rispettare le procedure consigliate di ripristino di Veritas Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da vari fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.

| Scenario                                                                    | Impatto                                             | Come ripristinare                                                                                                                                                                               | Note                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Errore del dispositivo StorSimple.                                                | Le operazioni di backup e ripristino vengono interrotte. | Sostituire il dispositivo con errore ed eseguire [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md). | Se viene eseguito un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud nel nuovo dispositivo. Tutte le operazioni avverranno pertanto alle velocità del cloud. La ripetizione dell'indicizzazione e della catalogazione può anche fare in modo che tutti i set di backup vengano analizzati ed estratti dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante.                                                               |
| Errore del server Veritas Backup Exec.                                       | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database come descritto nel dettaglio in [How to do a manual Backup and Restore of Backup Exec (BEDB) database](http://www.veritas.com/docs/000041083) (Come eseguire backup e ripristino manuale del database Backup Exec (BEDB)).            | Il server di Veritas Backup Exec deve essere ricompilato o ripristinato nel sito di ripristino di emergenza. Il database deve essere ripristinato al punto più recente. Se il database Veritas Backup Exec ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. La ripetizione dell'indicizzazione e della catalogazione può fare in modo che tutti i set di backup vengano analizzati ed estratti dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante. |
| Errore del sito che comporta la perdita del server di backup e di StorSimple. | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi Veritas Backup Exec.                                                                                                                                   | Ripristinare innanzitutto StorSimple e quindi Veritas Backup Exec.                                                                Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud nel nuovo dispositivo. Tutte le operazioni avverranno pertanto alle velocità del cloud.|


## <a name="references"></a>Riferimenti

In questo articolo si è fatto riferimento ai documenti seguenti:

- [Installazione MPIO per StorSimple](storsimple-configure-mpio-windows-server.md)

- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Abilitare e configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni:

- Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set-u2.md).
- Informazioni su come eseguire [Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


<!--HONumber=Dec16_HO2-->


