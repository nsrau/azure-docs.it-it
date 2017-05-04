---
title: StorSimple 8000 come destinazione di backup con Veeam | Microsoft Docs
description: Descrive la configurazione della destinazione di backup StorSimple con Veeam.
services: storsimple
documentationcenter: 
author: harshakirank
manager: matd
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d4a0dfbfab052e98e0dd641e8cab8fc143c2ff41
ms.lasthandoff: 04/27/2017

---

# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple come destinazione di backup con Veeam

## <a name="overview"></a>Panoramica

Azure StorSimple è una soluzione di archiviazione cloud ibrida di Microsoft. StorSimple consente di risolvere le complessità della crescita esponenziale dei dati usando un account di Archiviazione di Azure come estensione della soluzione locale e suddividendo automaticamente i dati in livelli tra l'archiviazione locale e l'archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veeam e le procedure consigliate per l'integrazione di entrambe le soluzioni. Vengono anche presentate alcune indicazioni su come configurare Veeam per un'integrazione ottimale con StorSimple. Per la configurazione migliore di Veeam al fine di soddisfare i requisiti di backup individuali e i Contratti di servizio, si rimanda alle procedure consigliate di Veeam, ai progettisti e agli amministratori di backup.

Anche se illustra i concetti chiave e i passaggi di configurazione, questo articolo non costituisce in alcun modo una guida dettagliata per la configurazione o l'installazione. L'articolo presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

### <a name="who-should-read-this"></a>A chi è rivolto questo articolo?

Le informazioni di questo articolo sono particolarmente utili per gli amministratori di backup, gli amministratori e i progettisti di archiviazione che conoscono l'archiviazione, Windows Server 2012 R2, Ethernet, i servizi cloud e Veeam.

### <a name="supported-versions"></a>Versioni supportate

-   Veeam 9 e versioni successive
-   [StorSimple Update 3 e versioni successive](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Perché usare StorSimple come destinazione di backup?

StorSimple è un'ottima scelta come destinazione di backup perché:

-   Offre archiviazione locale standard per le applicazioni di backup da usare come rapida destinazione di backup senza modifiche. È possibile usare StorSimple anche per il ripristino rapido dei backup recenti.
-   La suddivisione in livelli nel cloud è perfettamente integrata con un account di archiviazione cloud di Azure per l'uso dell'economica soluzione di Archiviazione di Azure.
-   Offre automaticamente archiviazione esterna per il ripristino di emergenza.


## <a name="key-concepts"></a>Concetti chiave

Come con qualsiasi soluzione di archiviazione, per ottenere un esito positivo è fondamentale un'attenta valutazione delle esigenze relative alle prestazioni di archiviazione, ai Contratti di servizio, alla frequenza di modifica e alla crescita di capacità della soluzione. L'idea principale è che con l'introduzione di un livello cloud, i tempi e le velocità effettive di accesso al cloud hanno un ruolo fondamentale nella capacità di StorSimple di soddisfare le esigenze dell'utente.

StorSimple è progettato per garantire l'archiviazione per le applicazioni che usano un working set di dati ben definito (dati attivi). In questo modello, il working set di dati viene archiviato nei livelli locali e il rimanente set di dati non in funzione, poco attivi o archiviati viene suddiviso in livelli nel cloud. Questo modello è illustrato nella figura seguente. La linea verde quasi piatta rappresenta i dati archiviati nei livelli locali del dispositivo StorSimple. La linea rossa rappresenta la quantità totale di dati archiviati nella soluzione StorSimple in tutti i livelli. Lo spazio tra la linea verde piatta e la curva esponenziale rossa rappresenta la quantità totale di dati archiviati nel cloud.

**Suddivisione in livelli di StorSimple**
![Diagramma della suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Tenendo presente questa architettura, si noterà che StorSimple è particolarmente adatto a essere usato come destinazione di backup. È possibile usare StorSimple per:

-   Eseguire le operazioni di ripristino più frequenti dal working set di dati locale.
-   Usare il cloud per il ripristino di emergenza esterno e per i dati meno recenti per i quali i ripristini sono meno frequenti.

## <a name="storsimple-benefits"></a>Vantaggi di StorSimple

StorSimple offre una soluzione locale che si integra facilmente con Microsoft Azure, sfruttando l'accesso automatico all'archiviazione locale e cloud.

StorSimple usa la suddivisione automatica in livelli tra il dispositivo locale, provvisto di archiviazione SSD (dispositivo a stato solido) e archiviazione SAS (Serial-attached SCSI), e Archiviazione di Azure. La suddivisione automatica in livelli mantiene i dati utilizzati di frequente in locale, sui livelli SSD e SAS. Sposta i dati a cui si accede di rado in Archiviazione di Azure.

StorSimple offre i vantaggi seguenti:

-   Algoritmi di deduplicazione e compressione univoci che usano il cloud per ottenere livelli avanzati di deduplicazione
-   Disponibilità elevata
-   Replica geografica usando la replica geografica di Azure
-   Integrazione di Azure
-   Crittografia dei dati nel cloud
-   Miglioramento del ripristino di emergenza e della conformità

Sebbene StorSimple presenti due scenari di distribuzione principali (destinazione di backup primaria e secondaria), fondamentalmente si tratta di un normale dispositivo di archiviazione a blocchi. StorSimple esegue tutta la compressione e la deduplicazione necessarie. Invia e recupera senza problemi i dati tra il cloud e l'applicazione e il file system.

Per altre informazioni su StorSimple, vedere [Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida](storsimple-overview.md). È possibile vedere anche le [specifiche tecniche di StorSimple serie 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> L'uso di un dispositivo StorSimple come destinazione di backup è supportato solo per StorSimple 8000 Update 3 e versioni successive.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Le tabelle seguenti riportano le indicazioni iniziali da modello ad architettura per il dispositivo.

**Capacità di StorSimple per l'archiviazione locale e cloud**

| Capacità di archiviazione | 8100 | 8600 |
|---|---|---|
| Capacità di archiviazione locale | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacità di archiviazione cloud | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Le dimensioni di archiviazione si intendono senza alcuna deduplicazione o compressione.

**Capacità di StorSimple per il backup primario e secondario**

| Scenario di backup  | Capacità di archiviazione locale  | Capacità di archiviazione cloud  |
|---|---|---|
| Backup primario  | Backup recenti archiviati nell'archivio locale per il ripristino rapido per soddisfare l'obiettivo punto di ripristino (RPO) | La cronologia dei backup (RPO) rientra nella capacità del cloud |
| Backup secondario | La copia secondaria dei dati di backup può essere archiviata nella capacità del cloud  | N/D  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione di backup primaria

In questo scenario i volumi StorSimple vengono presentati all'applicazione di backup come unico archivio per i backup. La figura seguente mostra l'architettura della soluzione in cui tutti i backup usano i volumi StorSimple a livelli per i backup e i ripristini.

![Diagramma logico con StorSimple come destinazione primaria di backup](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione primaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.
2.  Il server di backup scrive i dati nei volumi StorSimple a livelli.
3.  Il server di backup aggiorna il database del catalogo e quindi completa il processo di backup.
4.  Uno script snapshot attiva la gestione di snapshot cloud di StorSimple (start o delete).
5.  Il server di backup elimina i backup scaduti in base ai criteri di conservazione.

### <a name="primary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione primaria

1.  Il server di backup avvia il ripristino dei dati appropriati dall'archivio.
2.  L'agente di backup riceve i dati dal server di backup.
3.  Il server di backup completa il processo di ripristino.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione secondaria di backup

In questo scenario i volumi StorSimple vengono usati principalmente per la conservazione o l'archiviazione a lungo termine.

La figura seguente mostra un'architettura in cui i backup e i ripristini iniziali hanno come destinazione un volume a prestazioni elevate. Questi backup vengono copiati e archiviati in un volume StorSimple a livelli in una determinata pianificazione.

È importante impostare la dimensione del volume a prestazioni elevate in modo tale che possa gestire i requisiti di capacità e prestazioni dei criteri di conservazione.

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione secondaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.
2.  Il server di backup scrive i dati nell'archiviazione a prestazioni elevate.
3.  Il server di backup aggiorna il database del catalogo e quindi completa il processo di backup.
4.  Il server di backup copia i backup in StorSimple in base ai criteri di conservazione.
5.  Uno script snapshot attiva la gestione di snapshot cloud di StorSimple (start o delete).
6.  Il server di backup elimina i backup scaduti in base ai criteri di conservazione.

### <a name="secondary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione secondaria

1.  Il server di backup avvia il ripristino dei dati appropriati dall'archivio.
2.  L'agente di backup riceve i dati dal server di backup.
3.  Il server di backup completa il processo di ripristino.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

La distribuzione della soluzione richiede tre passaggi:

1. Preparare l'infrastruttura di rete.
2. Distribuire il dispositivo StorSimple come destinazione dei backup.
3. Distribuire Veeam.

I singoli passaggi sono descritti dettagliatamente nelle sezioni seguenti.

### <a name="set-up-the-network"></a>Configurare la rete

Poiché StorSimple è una soluzione integrata con il cloud di Azure, richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione dei dati e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per garantire prestazioni ottimali della soluzione, è consigliabile rispettare le seguenti procedure consigliate di rete:

-   Il collegamento fra i livelli di StorSimple e Azure deve soddisfare i requisiti di larghezza di banda. A tale scopo, applicare il livello di qualità del servizio (QoS) necessario alle opzioni dell'infrastruttura per soddisfare i Contratti di servizio relativi a RPO e all'obiettivo tempo di ripristino (RTO).
-   Le latenze massime di accesso all'archiviazione BLOB di Azure devono essere di circa 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Distribuire Veeam

Per le procedure consigliate sull'installazione di Veeam, vedere [Veeam Backup & Replication Best Practices](https://bp.veeam.expert/) (Procedure consigliate per il backup e la replica con Veeam) e il manuale dell'utente alla pagina [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Help Center Veeam (documentazione tecnica)).

## <a name="set-up-the-solution"></a>Configurare la soluzione

In questa sezione vengono descritti alcuni esempi di configurazione. Gli esempi e i suggerimenti seguenti illustrano l'implementazione più semplice ed essenziale. Questa implementazione potrebbe non essere applicabile direttamente ai requisiti specifici di backup dell'utente.

### <a name="set-up-storsimple"></a>Configurare StorSimple

| Attività di distribuzione di StorSimple  | Commenti aggiuntivi |
|---|---|
| Distribuire un dispositivo StorSimple locale. | Versioni supportate: Update 3 e versioni successive. |
| Attivare la destinazione di backup. | Usare i comandi seguenti per attivare o disattivare la modalità di destinazione di backup e ottenere lo stato. Per altre informazioni, vedere [Connettersi in remoto a un dispositivo StorSimple](storsimple-remote-connect.md).</br> Per attivare la modalità di backup: `Set-HCSBackupApplianceMode -enable`. </br> Per disattivare la modalità di backup: `Set-HCSBackupApplianceMode -disable`. </br> Per ottenere lo stato corrente delle impostazioni della modalità di backup: `Get-HCSBackupApplianceMode`. |
| Creare un contenitore comune per il volume che archivia i dati di backup. Tutti i dati contenuti in un contenitore di volumi vengono deduplicati. | I contenitori di volumi StorSimple definiscono i domini di deduplicazione.  |
| Creare i volumi di StorSimple. | Creare volumi con dimensioni il più possibile simili all'uso previsto in quanto le dimensioni del volume possono influire sulla durata dello snapshot cloud. Per vedere come dimensionare un volume, leggere le informazioni sui [criteri di conservazione](#retention-policies).</br> </br> Usare i volumi StorSimple a livelli e selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**. </br> L'uso di soli volumi aggiunti in locale non è supportato. |
| Creare un criterio di backup di StorSimple univoco per tutti i volumi della destinazione di backup. | Un criterio di backup di StorSimple definisce il gruppo di coerenza del volume. |
| Disabilitare la pianificazione alla scadenza degli snapshot. | Gli snapshot vengono attivati come operazione di post-elaborazione. |

### <a name="set-up-the-host-backup-server-storage"></a>Configurare l'archiviazione del server di backup host

Configurare l'archiviazione del server di backup host in base alle seguenti linee guida:  

- Non usare i volumi con spanning, creati tramite il servizio di gestione dischi di Windows. I volumi con spanning non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione dell'unità di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server Veeam.
    - Usare iSCSI per server fisici.


## <a name="best-practices-for-storsimple-and-veeam"></a>Procedure consigliate per StorSimple e Veeam

Configurare la soluzione in base alle linee guida riportate nelle sezioni seguenti.

### <a name="operating-system-best-practices"></a>Procedure consigliate per il sistema operativo

-   Disabilitare la crittografia di Windows Server e la deduplicazione per il file system NTFS.
-   Disabilitare la deframmentazione di Windows Server sui volumi StorSimple.
-   Disabilitare l'indicizzazione di Windows Server sui volumi StorSimple.
-   Eseguire una scansione antivirus dell'host di origine (non nei volumi StorSimple).
-   Disabilitare la [manutenzione di Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) predefinita in Gestione attività. Eseguire questa operazione in uno dei modi seguenti:
    - Disattivare lo strumento di configurazione della manutenzione nell'Utilità di pianificazione attività di Windows.
    - Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) di Windows Sysinternals. Dopo aver scaricato PsExec, eseguire Windows PowerShell come amministratore e digitare:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Procedure consigliate di StorSimple

-   Assicurarsi che il dispositivo StorSimple sia aggiornato alla versione [Update 3 o successiva](storsimple-install-update-3.md).
-   Isolare il traffico iSCSI e cloud. Usare connessioni iSCSI dedicate per il traffico tra StorSimple e il server di backup.
-   Assicurarsi che il dispositivo StorSimple sia una destinazione di backup dedicata. I carichi di lavoro misti non sono supportati in quanto influenzano RTO e RPO.

### <a name="veeam-best-practices"></a>Procedure consigliate di Veeam

-   Il database Veeam deve essere locale nel server e non deve trovarsi in un volume StorSimple.
-   Per il ripristino di emergenza, eseguire il backup del database di Veeam in un volume StorSimple.
-   Per questa soluzione sono supportati i backup Veeam completi e incrementali. Si consiglia di non usare backup sintetici e differenziali.
-   I file dei dati di backup devono contenere solo i dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta tra diversi processi.
-   Disattivare la verifica dei processi. Se necessario, pianificare la verifica dopo l'ultimo processo di backup. È importante tenere presente che questo processo influisce sulla finestra di backup.
-   Disattivare la preallocazione dei supporti di memorizzazione.
-   Assicurarsi che l'elaborazione parallela sia attivata.
-   Disattivare la compressione.
-   Disattivare la deduplicazione nel processo di backup.
-   Impostare ottimizzazione su **LAN Target** (Destinazione LAN).
-   Attivare **Create active full backup** (Crea backup completo attivo) ogni 2 settimane.
-   Nell'archivio di backup impostare **Use per-VM backup file** (Usa file di backup per ciascuna macchina virtuale).
-   Impostare **Use multiple upload streams per job** (Usa più flussi di caricamento per processo) su **8** (massimo 16 consentiti). Aumentare o diminuire questo numero in base all'uso della CPU sul dispositivo StorSimple.

## <a name="retention-policies"></a>Criteri di conservazione

Uno dei tipi di criteri di conservazione dei backup più comuni è GFS (Grandfather, Father e Son). In un criterio GFS viene eseguito un backup incrementale ogni giorno e vengono eseguiti backup completi ogni settimana e ogni mese. Questo criterio dà come risultato sei volumi StorSimple a livelli: un volume contiene i backup completi settimanali, mensili e annuali; gli altri cinque volumi contengono i backup incrementali giornalieri.

Nell'esempio seguente si usa una rotazione GFS. Nell'esempio si presuppone quanto segue:

-   Vengono usati dati compressi o non deduplicati.
-   I backup completi hanno dimensione di 1 TiB ciascuno.
-   I backup incrementali giornalieri hanno dimensione di 500 GiB ciascuno.
-   Quattro backup settimanali sono conservati per un mese.
-   Dodici backup mensili sono conservati per un anno.
-   Un backup annuale è conservato per 10 anni.

In base ai presupposti precedenti creare un volume a più livelli StorSimple a 26 TiB per i backup mensili e annuali completi. Creare un volume StorSimple a livelli da 5 TiB per ciascuno dei backup incrementali giornalieri.

| Conservazione per tipo di backup | Dimensioni (TiB) | Moltiplicatore GFS\* | Capacità totale (TiB)  |
|---|---|---|---|
| Completo settimanale | 1 | 4  | 4 |
| Incrementale giornaliero | 0,5 | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Completo mensile | 1 | 12 | 12 |
| Completo annuale | 1  | 10 | 10 |
| Requisito GFS |   | 38 |   |
| Quota aggiuntiva  | 4  |   | 42 (requisito GFS totale)  |
\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i requisiti di backup.

## <a name="set-up-veeam-storage"></a>Configurare l'archiviazione Veeam

### <a name="to-set-up-veeam-storage"></a>Per configurare l'archiviazione Veeam

1.  Nella console di backup e replica di Veeam, in **Repository Tools** (Strumenti di archivio), passare a **Backup Infrastructure** (Infrastruttura di backup). Fare clic con il pulsante destro del mouse su **Backup Repositories** (Archivi di backup) e selezionare **Add Backup Repository** (Aggiungi archivio di backup).

    ![Console di gestione di Veeam, pagina dell'archivio di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Nella finestra di dialogo **New Backup Repository** (Nuovo archivio di backup) immettere un nome e una descrizione per l'archivio. Selezionare **Avanti**.

    ![Console di gestione di Veeam, pagina di nome e descrizione](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Come tipo selezionare **Microsoft Windows server** (Serve Microsoft Windows). Selezionare il server Veeam. Selezionare **Avanti**.

    ![Console di gestione Veeam, selezionare il tipo di repository di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Per specificare la **posizione**, individuare e selezionare il volume necessario. Selezionare la casella di controllo **Limit maximum concurrent tasks to:** (Limita numero massimo attività simultanee a:) e impostare il valore su **4**. In questo modo si garantisce che vengano elaborati contemporaneamente solo quattro dischi virtuali durante l'elaborazione di ciascuna macchina virtuale (VM). Selezionare il pulsante **Advanced** (Avanzate).

    ![Console di gestione Veeam, selezionare il volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Nella finestra di dialogo **Storage Compatibility Settings** (Impostazioni di compatibilità di archiviazione) selezionare la casella di controllo **Use per-VM backup files** (Usa file di backup per ciascuna macchina virtuale).

    ![Console di gestione Veeam, impostazioni di compatibilità di archiviazione](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Nella finestra di dialogo **New Backup Repository** (Nuovo archivio di backup) selezionare la casella di controllo **Enable vPower NFS service on the mount server (recommended)** (Abilita servizio NFS vPower nel server di montaggio (scelta consigliata)). Selezionare **Avanti**.

    ![Console di gestione di Veeam, pagina dell'archivio di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Rivedere le impostazioni e selezionare **Next** (Avanti).

    ![Console di gestione di Veeam, pagina dell'archivio di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Viene aggiunto un repository al server Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurare StorSimple come destinazione di backup primaria

> [!IMPORTANT]
> Il ripristino dei dati da un backup suddiviso in livelli nel cloud viene eseguito alle velocità del cloud.

La figura seguente illustra il mapping di un volume tipico a un processo di backup. In questo caso, tutti i backup settimanali vengono mappati al disco Saturday full e i backup incrementali vengono mappati ai dischi incrementali dei giorni da lunedì a venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume a più livelli StorSimple.

![Diagramma logico di configurazione della destinazione primaria di backup](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione primaria di backup

Di seguito è riportato un esempio di una pianificazione a rotazione GFS per quattro settimane, mensile e annuale:

| Frequenza/Tipo di backup | Completa | Incrementale (giorni 1-5)  |   
|---|---|---|
| Settimanale (settimane 1-4) | Sabato | Lunedì-venerdì |
| Mensile  | Sabato  |   |
| Annuale | Sabato  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Assegnare volumi StorSimple a un processo di backup Veeam

Per lo scenario di destinazione del backup primario, creare un processo giornaliero con il volume StorSimple primario di Veeam. Per uno scenario di destinazione del backup secondario, creare un processo giornaliero mediante archiviazione DAS (Direct Attached Storage), NAS (Network Attached Storage) o JBOD (Just a Bunch of Disks).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Per assegnare volumi StorSimple a un processo di backup Veeam

1.  Nella console di backup e replica di Veeam selezionare **Backup & Replication** (Backup e replica). Fare clic con il pulsante destro del mouse su **Backup** e selezionare **VMware** o **Hyper-V** in base all'ambiente.

    ![Console di gestione Veeam, nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Nella finestra di dialogo **New Backup Job** (Nuovo processo di backup) immettere un nome e una descrizione per il processo di backup giornaliero.

    ![Console di gestione di Veeam, pagina del nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Selezionare una macchina virtuale su cui eseguire il backup.

    ![Console di gestione di Veeam, pagina del nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Selezionare i valori desiderati per **Backup proxy** (Proxy di backup) e **Backup repository** (Repository di backup). Selezionare un valore per **Restore points to keep on disk** (Punti di ripristino da mantenere su disco) in base alle definizioni RPO e RTO dell'ambiente per l'archivio collegato locale. Selezionare **Advanced** (Avanzate).

    ![Console di gestione di Veeam, pagina del nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Nella finestra di dialogo **Advanced Settings** (Impostazioni avanzate), nella scheda **Backup**, selezionare **Incremental** (Incrementale). Assicurarsi che sia selezionata la casella di controllo **Create synthetic full backups periodically** (Crea periodicamente backup completi sintetici). Selezionare la casella di controllo **Create active full backups periodically** (Crea periodicamente backup completi attivi). Per **Active full backup** (Backup completo attivo) selezionare la casella di controllo **Weekly on selected days** (Settimanale nei giorni selezionati) per Saturday (Sabato).

    ![Console di gestione di Veeam, pagina delle impostazioni avanzate nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Sulla scheda **Storage** (Archiviazione) assicurasi che la casella di controllo **Enable inline data deduplication** (Abilita deduplicazione dei dati in linea) sia deselezionata. Selezionare la casella di controllo **Exclude swap file blocks** (Escludi blocchi di file di scambio) e selezionare la casella di controllo **Exclude deleted file blocks** (Escludi blocchi di file eliminati). Impostare **Compression level** (Livello di compressione) su **None** (Nessuno). Per avere prestazioni e deduplicazioni equilibrate impostare **Storage optimization** (Ottimizzazione archiviazione) su **LAN Target** (Destinazione LAN). Selezionare **OK**.

    ![Console di gestione di Veeam, pagina delle impostazioni avanzate nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Per informazioni sulle impostazioni di deduplicazione e compressione di Veeam, vedere [Deduplicazione e compressione dei dati](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  Nella finestra di dialogo **Edit Backup Job** (Modifica processo di backup) è possibile selezionare la casella di controllo **Enable application-aware processing** (Abilita elaborazione compatibile con le applicazioni), opzione facoltativa.

    ![Console di gestione di Veeam, pagina dell'elaborazione guest di un nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Impostare la pianificazione in modo che venga eseguita una volta al giorno a un orario che è possibile specificare.

    ![Console di gestione di Veeam, pagina di pianificazione nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurare StorSimple come destinazione secondaria di backup

> [!NOTE]
> I ripristini dei dati da un backup suddiviso in livelli nel cloud vengono eseguiti alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. Ad esempio, è possibile usare un volume RAID (Redundant Array of Independent Disks, array ridondante di dischi indipendenti) per ottenere spazio, input/output (I/O) e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

La figura seguente illustra tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo scenario tutti i backup vengono eseguiti nel volume RAID locale (per il server). Questi backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo che possa gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Esempio GFS con StorSimple come destinazione secondaria di backup

La tabella seguente indica come configurare il backup per l'esecuzione su dischi locali e dischi StorSimple. Include i requisiti di capacità totale e individuali.

| Tipo di backup e conservazione | Archiviazione configurata | Dimensioni (TiB) | Moltiplicatore GFS | Capacità totale\* (TiB) |
|---|---|---|---|---|
| Settimana 1 (completo e incrementale) |Disco locale (breve termine)| 1 | 1 | 1 |
| StorSimple settimane 2-4 |Disco StorSimple (lungo termine) | 1 | 4 | 4 |
| Completo mensile |Disco StorSimple (lungo termine) | 1 | 12 | 12 |
| Completo annuale |Disco StorSimple (lungo termine) | 1 | 1 | 1 |
|Requisiti di dimensione dei volumi GFS |  |  |  | 18*|
\* La capacità totale include 17 TiB dei dischi StorSimple e 1 TiB del volume RAID locale.


### <a name="gfs-example-schedule"></a>Pianificazione di esempio GFS

Pianificazione della rotazione GFS settimanale, mensile e annuale

| Settimana | Completa | Incrementale Giorno 1 | Incrementale Giorno 2 | Incrementale Giorno 3 | Incrementale Giorno 4 | Incrementale Giorno 5 |
|---|---|---|---|---|---|---|
| Settimana 1 | Volume RAID locale  | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale |
| Settimana 2 | StorSimple settimane 2-4 |   |   |   |   |   |
| Settimana 3 | StorSimple settimane 2-4 |   |   |   |   |   |
| Settimana 4 | StorSimple settimane 2-4 |   |   |   |   |   |
| Mensile | StorSimple Mensile |   |   |   |   |   |
| Annuale | StorSimple Annuale  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Assegnare volumi StorSimple a un processo di copia Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Per assegnare volumi StorSimple a un processo di copia Veeam

1.  Nella console di backup e replica di Veeam selezionare **Backup & Replication** (Backup e replica). Fare clic con il pulsante destro del mouse su **Backup** e selezionare **VMware** o **Hyper-V** in base all'ambiente.

    ![Console di gestione di Veeam, pagina del processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Nella finestra di dialogo **New Backup Copy Job** (Nuovo processo di copia di backup) immettere un nome e una descrizione per il processo.

    ![Console di gestione di Veeam, pagina del processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Selezionare le VM da elaborare. Selezionare From backups (Dai backup) e quindi selezionare il backup giornaliero creato in precedenza.

    ![Console di gestione di Veeam, pagina del processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Se necessario, escludere gli oggetti dal processo di copia di backup.

5.  Selezionare l'archivio di backup e impostare un valore per **Restore points to keep** (Punti di ripristino da mantenere). Assicurarsi di selezionare la casella di controllo **Keep the following restore points for archival purposes** (Mantieni i seguenti punti di ripristino per motivi di archiviazione). Definire la frequenza di backup e quindi fare clic su **Advanced** (Avanzate).

    ![Console di gestione di Veeam, pagina del processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Specificare le impostazioni avanzate seguenti:

    * Nella scheda **Maintenance** (Manutenzione) disattivare la protezione dal danneggiamento a livello di archiviazione.

    ![Console di gestione di Veeam, pagina delle impostazioni avanzate nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Nella scheda **Archiviazione** assicurarsi che la deduplicazione e compressione siano disattivate.

    ![Console di gestione di Veeam, pagina delle impostazioni avanzate nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Specificare che il trasferimento dei dati è diretto.

8.  Definire la pianificazione della finestra di copia del backup in base alle esigenze e quindi completare la procedura guidata.

Per altre informazioni, vedere [Creare processi di copia di backup](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Creare uno snapshot cloud equivale a spedire i nastri di backup locali a una struttura esterna. Se si usa l'archiviazione con ridondanza geografica di Azure, la creazione di uno snapshot cloud equivale a spedire i nastri di backup a più siti. Se è necessario ripristinare un dispositivo dopo un'emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.

La sezione seguente descrive come creare un breve script per avviare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup.

> [!NOTE]
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Avviare ed eliminare gli snapshot cloud mediante uno script

> [!NOTE]
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, vedere la documentazione di Veeam.


### <a name="backup-lifecycle"></a>Ciclo di vita del backup

![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Requisiti

-   Il server che esegue lo script deve avere accesso alle risorse del cloud Azure.
-   L'account utente deve avere le autorizzazioni necessarie.
-   Deve essere configurato ma non attivato un criterio di backup StorSimple con i volumi StorSimple associati.
-   Saranno necessari il nome della risorsa StorSimple, la chiave di registrazione, il nome del dispositivo e l'ID dei criteri di backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Per avviare o eliminare uno snapshot cloud

1. [Installare Azure PowerShell](/powershell/azure/overview).
2. [Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione](https://msdn.microsoft.com/library/dn385850.aspx).
3. Nel portale di Azure classico ottenere il nome della risorsa e la [chiave di registrazione per il servizio StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).
4. Nel server che esegue lo script eseguire PowerShell come amministratore. Digitare il comando seguente:

    `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Annotare l'ID del criterio di backup.
5. Nel Blocco note creare un nuovo script di PowerShell mediante il codice seguente.

    Copiare e incollare questo frammento di codice:
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
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```
6. Per aggiungere lo script al processo di backup, modificare le opzioni avanzate del processo Veeam.

    ![Scheda script di impostazioni avanzate per il backup Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple come script di post-elaborazione alla fine del processo di backup giornaliero. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO e RTO, consultare il progettista di backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino

I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. I ripristini dei dati suddivisi in livelli nel cloud vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini si verificano alla velocità del disco locale del dispositivo.

Veeam consente un ripristino rapido e granulare a livello di file tramite StorSimple, usando le visualizzazioni di esplorazione integrate nella console di Veeam. Usare gli Explorer di Veeam per ripristinare elementi individuali come messaggi e-mail, oggetti di Active Directory ed elementi SharePoint dai backup. Il ripristino può essere eseguito senza interruzioni alle macchine virtuali locali. È inoltre possibile eseguire il ripristino temporizzato per il database SQL di Azure e i database Oracle. Veeam e StorSimple semplificano e rendono più veloce il processo di ripristino a livello di elemento da Azure. Per informazioni su come eseguire un ripristino, vedere la documentazione di Veeam:

- Per [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Per [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Per [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Per [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Per [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da numerosi fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.

| Scenario | Impatto | Come ripristinare | Note |
|---|---|---|---|
| Errore del dispositivo StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Sostituire il dispositivo con errore ed eseguire [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md). | Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale e questo processo può richiedere molto tempo. |
| Errore del server di Veeam | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database come illustrato in [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Help Center Veeam (documentazione tecnica)).  | È necessario ricompilare o ripristinare il server di Veeam nel sito di ripristino di emergenza. Ripristinare il database al punto più recente. Se il database Veeam ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante. |
| Errore del sito che comporta la perdita sia del server di backup che di StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi Veeam. | Ripristinare innanzitutto StorSimple e quindi Veeam. Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. |


## <a name="references"></a>Riferimenti

Questo articolo fa riferimento ai documenti seguenti:

- [Configurazione di Multipath I/O per StorSimple](storsimple-configure-mpio-windows-server.md)
- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [eseguire il ripristino da un set di backup](storsimple-restore-from-backup-set-u2.md).
- Informazioni su come eseguire le procedure di [failover e ripristino di emergenza di un dispositivo](storsimple-device-failover-disaster-recovery.md).

