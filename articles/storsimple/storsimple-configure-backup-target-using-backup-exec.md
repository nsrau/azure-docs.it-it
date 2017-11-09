---
title: StorSimple 8000 come destinazione di backup con Backup Exec | Microsoft Docs
description: Descrive la configurazione della destinazione di backup StorSimple con Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: a28b46e10bbdd5331cc665fad3f80523b3aa8a58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple come destinazione di backup con Backup Exec

## <a name="overview"></a>Panoramica

Azure StorSimple è una soluzione di archiviazione cloud ibrida di Microsoft. StorSimple consente di risolvere le complessità della crescita esponenziale dei dati usando un account di archiviazione di Azure come estensione della soluzione locale e suddividendo automaticamente i dati in livelli tra l'archiviazione locale e l'archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veritas Backup Exec e le procedure consigliate per l'integrazione di entrambe le soluzioni. Vengono anche presentate alcune indicazioni su come configurare Backup Exec per un'integrazione ottimale con StorSimple. Per la configurazione migliore di Backup Exec al fine di soddisfare i requisiti di backup individuali e i Contratti di servizio, si rimanda alle procedure consigliate di Veritas, ai progettisti e agli amministratori di backup.

Anche se illustra i concetti chiave e i passaggi di configurazione, questo articolo non costituisce in alcun modo una guida dettagliata per la configurazione o l'installazione. L'articolo presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

### <a name="who-should-read-this"></a>A chi è rivolto questo articolo?

Le informazioni di questo articolo sono particolarmente utili per gli amministratori di backup, gli amministratori e i progettisti di archiviazione che conoscono l'archiviazione, Windows Server 2012 R2, Ethernet, i servizi cloud e Backup Exec.

## <a name="supported-versions"></a>Versioni supportate

-   [Backup Exec 16 e versioni successive](http://backupexec.com/compatibility)
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
![Diagramma della suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

| Capacità di archiviazione       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![Diagramma logico con StorSimple come destinazione primaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Distribuire Backup Exec.

I singoli passaggi sono descritti dettagliatamente nelle sezioni seguenti.

### <a name="set-up-the-network"></a>Configurare la rete

Poiché StorSimple è una soluzione integrata con il cloud di Azure, richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per garantire prestazioni ottimali della soluzione, è consigliabile rispettare le seguenti procedure consigliate di rete:

-   Il collegamento fra i livelli di StorSimple e Azure deve soddisfare i requisiti di larghezza di banda. A tale scopo, applicare il livello di qualità del servizio (QoS) necessario alle opzioni dell'infrastruttura per soddisfare i Contratti di servizio relativi a RPO e obiettivo tempo di ripristino (RTO).
-   Le latenze massime di accesso all'archiviazione BLOB di Azure devono essere di circa 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Distribuire Backup Exec

Per le procedure consigliate per l'installazione di Backup Exec, vedere [Best practices for Backup Exec installation](https://www.veritas.com/support/en_US/article.000068207) (Procedure consigliate per l'installazione di Backup Exec).

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

- Non usare i volumi con spanning, creati tramite il servizio di gestione dischi di Windows. I dischi con spanning non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server Backup Exec.
    - Usare iSCSI per i server fisici.
    - Usare dischi pass-through per i server virtuali.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Procedure consigliate per StorSimple e Backup Exec

Configurare la soluzione in base alle linee guida riportate nelle sezioni seguenti.

### <a name="operating-system-best-practices"></a>Procedure consigliate per il sistema operativo

-   Disabilitare la crittografia di Windows Server e la deduplicazione per il file system NTFS.
-   Disabilitare la deframmentazione di Windows Server sui volumi StorSimple.
-   Disabilitare l'indicizzazione di Windows Server sui volumi StorSimple.
-   Eseguire una scansione antivirus dell'host di origine (non nei volumi StorSimple).
-   Disabilitare la [manutenzione di Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) predefinita in Gestione attività. Eseguire questa operazione in uno dei modi seguenti:
   - Disattivare lo strumento di configurazione della manutenzione nell'Utilità di pianificazione attività di Windows.
   - Scaricare [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) di Windows Sysinternals. Dopo aver scaricato PsExec, eseguire Azure PowerShell come amministratore e digitare:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Procedure consigliate di StorSimple

  -   Assicurarsi che il dispositivo StorSimple sia aggiornato alla versione [Update 3 o successiva](storsimple-install-update-3.md).
  -   Isolare il traffico iSCSI e cloud. Usare connessioni iSCSI dedicate per il traffico tra StorSimple e il server di backup.
  -   Assicurarsi che il dispositivo StorSimple sia una destinazione di backup dedicata. I carichi di lavoro misti non sono supportati in quanto influenzano RTO e RPO.

### <a name="backup-exec-best-practices"></a>Procedure consigliate di Backup Exec

-   Backup Exec deve essere installato su un'unità locale del server e non su un volume di StorSimple.
-   Impostare le **operazioni di scrittura simultanea** dell'archiviazione di Backup Exec sul valore massimo consentito.
    -   Impostare le **dimensioni del buffer e del blocco** di archiviazione di Backup Exec su 512 KB.
    -   Attivare la **lettura e scrittura in buffering** dell'archiviazione di Backup Exec.
-   StorSimple supporta i backup completi e incrementali di Backup Exec. Si consiglia di non usare backup sintetici e differenziali.
-   I file dei dati di backup devono contenere solo i dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta tra diversi processi.
-   Disabilitare la verifica dei processi. Se necessario, pianificare la verifica dopo l'ultimo processo di backup. È importante tenere presente che questo processo influisce sulla finestra di backup.
-   Selezionare **Storage** (Archiviazione) > **Your disk** (Disco) > **Details** (Dettagli) > **Properties** (Proprietà). Disattivare **Pre-allocate disk space** (Prealloca spazio del disco).

Per le ultime impostazioni di Backup Exec e le procedure consigliate per l'implementazione di questi requisiti, vedere [il sito Web di Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Criteri di conservazione

Uno dei tipi di criteri di conservazione dei backup più comuni è GFS (Grandfather, Father e Son). In un criterio GFS viene eseguito un backup incrementale ogni giorno e vengono eseguiti backup completi ogni settimana e ogni mese. Questo criterio genera sei volumi StorSimple a livelli. Un volume contiene i backup completi settimanali, mensili e annuali. Gli altri cinque volumi archiviano i backup incrementali giornalieri.

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

## <a name="set-up-backup-exec-storage"></a>Configurare l'archiviazione con Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Per configurare l'archiviazione con Backup Exec

1.  Nella console di gestione di Backup Exec selezionare **Storage** (Archiviazione) > **Configure Storage** (Configura archiviazione) > **Disk-Based Storage** (Archiviazione basata su disco) > **Next** (Avanti).

    ![Console di gestione di Backup Exec, pagina di configurazione dell'archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Selezionare **Disk Storage** (Archiviazione su disco) e quindi selezionare **Next** (Avanti).

    ![Console di gestione di Backup Exec, pagina di selezione dell'archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Inserire un nome rappresentativo, ad esempio **Saturday Full** (Sabato completo), e una descrizione. Selezionare **Avanti**.

    ![Console di gestione di Backup Exec, pagina del nome e descrizione](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Selezionare il disco in cui si desidera creare il dispositivo di archiviazione su disco e quindi selezionare **Next** (Avanti).

    ![Console di gestione di Backup Exec, pagina di selezione del disco di archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Aumentare il numero di operazioni di scrittura fino a **16** e fare clic su **Next** (Avanti).

    ![Console di gestione di Backup Exec, pagina delle impostazioni per le operazioni di scrittura simultanee](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Rivedere le impostazioni e selezionare **Finish** (Fine).

    ![Console di gestione di Backup Exec, pagina di riepilogo della configurazione di archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Alla fine di ogni assegnazione di volume modificare le impostazioni del dispositivo di archiviazione in modo che corrispondano a quelle indicate in [Procedure consigliate per StorSimple e Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Console di gestione di Backup Exec, pagina delle impostazioni del dispositivo di archiviazione](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Ripetere i passaggi da 1 a 7 finché non si assegnano tutti i volumi StorSimple a Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurare StorSimple come destinazione di backup primaria

> [!NOTE]
> Il ripristino dei dati da un backup suddiviso in livelli nel cloud viene eseguito alle velocità del cloud.

La figura seguente illustra il mapping di un volume tipico a un processo di backup. In questo caso, tutti i backup settimanali vengono mappati al disco Saturday full e i backup incrementali vengono mappati ai dischi incrementali dei giorni da lunedì a venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume a più livelli StorSimple.

![Diagramma logico di configurazione della destinazione primaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione primaria di backup

Di seguito è riportato un esempio di una pianificazione a rotazione GFS per quattro settimane, mensile e annuale:

| Frequenza/Tipo di backup | Completa | Incrementale (giorni 1-5)  |   
|---|---|---|
| Settimanale (settimane 1-4) | Sabato | Lunedì-venerdì |
| Mensile  | Sabato  |   |
| Annuale | Sabato  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Assegnare volumi StorSimple a un processo di backup di Backup Exec

Nella sequenza seguente si presuppone che Veritas Backup Exec, l'host di destinazione, sia configurato in conformità alle linee guida dell'agente Veritas Backup Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Per assegnare volumi StorSimple a un processo di backup di Backup Exec

1.  Nella console di gestione di Backup Exec selezionare**Host** (Host) > **Backup** (Backup) > **Backup to Disk** (Backup su disco).

    ![Nella console di gestione di Backup Exec selezionare Host (Host), Backup (Backup), Backup to Disk (Backup su disco)](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  Nella finestra di dialogo **Backup Definition Properties** (Proprietà definizione backup), sotto **Backup** (Backup), selezionare **Edit** (Modifica).

    ![Console di gestione di Backup Exec, finestra di dialogo Backup Definition Properties (Proprietà definizione backup)](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Configurare i backup completi e incrementali in modo che soddisifno i requisiti di RPO e RTO e siano conformi alle procedure consigliate di Veritas.

4.  Nella finestra **Backup Options** (Opzioni di backup) selezionare **Storage** (Archiviazione).

    ![Console di gestione di Backup Exec, finestra di dialogo Backup Options Storage (Opzioni di backup Archiviazione)](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Assegnare i volumi StorSimple corrispondenti alla pianificazione dei backup.

    > [!NOTE]
    > **Compression** (Compressione) e **Encryption type** (Tipo di crittografia) sono impostati su **None** (Nessuno).

6.  In **Verify** (Verifica) selezionare la casella di controllo **Do not verify data for this job** (Non verificare i dati per questo processo). L'opzione potrebbe influire sulla suddivisione in livelli di StorSimple.

    > [!NOTE]
    > La deframmentazione, l'indicizzazione e la verifica in background compromettono la suddivisione in livelli di StorSimple.

    ![Console di gestione di Backup Exec, finestra di dialogo Backup Options Verify (Opzioni di backup Verifica)](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Dopo aver configurato il resto delle opzioni di backup per soddisfare i requisiti, selezionare **OK** per terminare.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurare StorSimple come destinazione secondaria di backup

> [!NOTE]
>I ripristini dei dati da un backup suddiviso in livelli nel cloud vengono eseguiti alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. Ad esempio, è possibile usare un volume RAID (Redundant Array of Independent Disks, array ridondante di dischi indipendenti) per ottenere spazio, input/output (I/O) e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

La figura seguente illustra tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo scenario tutti i backup vengono eseguiti nel volume RAID locale (per il server). i backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo che possa gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Esempio GFS con StorSimple come destinazione secondaria di backup

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

La tabella seguente indica come configurare il backup per l'esecuzione su dischi locali e dischi StorSimple. Include i requisiti di capacità totale e individuali.

### <a name="backup-configuration-and-capacity-requirements"></a>Configurazione di backup e requisiti di capacità

| Tipo di backup e conservazione | Archiviazione configurata | Dimensioni (TiB) | Moltiplicatore GFS | Capacità totale\* (TiB) |
|---|---|---|---|---|
| Settimana 1 (completo e incrementale) |Disco locale (breve termine)| 1 | 1 | 1 |
| StorSimple settimane 2-4 |Disco StorSimple (lungo termine) | 1 | 4 | 4 |
| Completo mensile |Disco StorSimple (lungo termine) | 1 | 12 | 12 |
| Completo annuale |Disco StorSimple (lungo termine) | 1 | 1 | 1 |
|Requisiti di dimensione dei volumi GFS |  |  |  | 18*|
\* La capacità totale include 17 TiB dei dischi StorSimple e 1 TiB del volume RAID locale.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Pianificazione di esempio GFS: rotazione GFS settimanale, mensile e annuale

| Settimana | Completa | Incrementale Giorno 1 | Incrementale Giorno 2 | Incrementale Giorno 3 | Incrementale Giorno 4 | Incrementale Giorno 5 |
|---|---|---|---|---|---|---|
| Settimana 1 | Volume RAID locale  | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale |
| Settimana 2 | StorSimple settimane 2-4 |   |   |   |   |   |
| Settimana 3 | StorSimple settimane 2-4 |   |   |   |   |   |
| Settimana 4 | StorSimple settimane 2-4 |   |   |   |   |   |
| Mensile | StorSimple Mensile |   |   |   |   |   |
| Annuale | StorSimple Annuale  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Assegnazione di volumi StorSimple a un processo di archiviazione e duplicazione di Backup Exec

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Per assegnare volumi StorSimple a un processo di archiviazione e duplicazione di Backup Exec

1.  Nella console di gestione di Backup Exec fare clic con il pulsante destro del mouse sul processo che si vuole archiviare in un volume StorSimple e selezionare **Backup Definition Properties** (Proprietà definizione backup) > **Edit** (Modifica).

    ![Console di gestione di Backup Exec, scheda Backup Definition Properties (Proprietà definizione backup)](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Selezionare **Add Stage** (Aggiungi fase) > **Duplicate to Disk** (Duplica su disco) > **Edit** (Modifica).

    ![Console di gestione di Backup Exec, aggiungere una fase](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  Nella finestra di dialogo **Duplicate Options** (Opzioni di duplicazione), selezionare i valori che si vuole usare per **Source** (Origine) e **Schedule** (Pianificazione).

    ![Console di gestione di Backup Exec, proprietà di definizione del backup e opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  Nell'elenco a discesa **Storage** (Archiviazione) selezionare il volume di StorSimple in cui si desidera che il processo di archiviazione memorizzi i dati.

    ![Console di gestione di Backup Exec, proprietà di definizione del backup e opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Selezionare **Verify** (Verifica) e quindi attivare la casella di controllo **Do not verify data for this job** (Non verificare i dati per questo processo).

    ![Console di gestione di Backup Exec, proprietà di definizione del backup e opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Selezionare **OK**.

    ![Console di gestione di Backup Exec, proprietà di definizione del backup e opzioni di duplicazione](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Nella colonna **Backup** aggiungere una nuova fase. Per l'origine scegliere l'opzione **incrementale**. Per la destinazione scegliere il volume StorSimple in cui viene archiviato il processo di backup incrementale. Ripetere i passaggi da 1 a 6.

## <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Creare uno snapshot cloud equivale a spedire i nastri di backup locali a una struttura esterna. Se si usa l'archiviazione con ridondanza geografica di Azure, la creazione di uno snapshot cloud equivale a spedire i nastri di backup a più siti. Se è necessario ripristinare un dispositivo dopo un'emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.

La sezione seguente descrive come creare un breve script per avviare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup.

> [!NOTE]
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Avviare ed eliminare gli snapshot cloud mediante uno script

> [!NOTE]
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, vedere la [documentazione di Backup Exec](https://www.veritas.com/support/en_US/15047.html).

### <a name="backup-lifecycle"></a>Ciclo di vita del backup

![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Requisiti

-   Il server che esegue lo script deve avere accesso alle risorse del cloud Azure.
-   L'account utente deve avere le autorizzazioni necessarie.
-   Deve essere configurato ma non attivato un criterio di backup StorSimple con i volumi StorSimple associati.
-   Saranno necessari il nome della risorsa StorSimple, la chiave di registrazione, il nome del dispositivo e l'ID dei criteri di backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Per avviare o eliminare uno snapshot cloud

1.  [Installare Azure PowerShell](/powershell/azure/overview).
2. Scaricare e installare lo script di PowerShell [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1).
3. Nel server che esegue lo script eseguire PowerShell come amministratore. Assicurarsi di eseguire lo script con `-WhatIf $true` per visualizzare le modifiche apportate dallo script. Al termine della convalida, passare `-WhatIf $false`. Eseguire questo comando:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Aggiungere lo script al processo di backup in Backup Exec, modificando i comandi di pre-elaborazione e post-elaborazione delle opzioni del processo di Backup Exec.

    ![Console di Backup Exec, opzioni di backup, scheda dei comandi di pre-elaborazione e post-elaborazione](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple come script di post-elaborazione alla fine del processo di backup giornaliero. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO e RTO, consultare il progettista di backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino

I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. I ripristini dei dati suddivisi in livelli nel cloud vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini si verificano alla velocità del disco locale del dispositivo. Per informazioni su come eseguire un ripristino, vedere la documentazione di Backup Exec. Si consiglia di adottare le procedure consigliate per il ripristino di Backup Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da numerosi fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.

| Scenario | Impatto | Come ripristinare | Note |
|---|---|---|---|
| Errore del dispositivo StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Sostituire il dispositivo con errore ed eseguire [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md). | Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale e questo processo può richiedere molto tempo. |
| Errore del server di Backup Exec | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database come descritto nel dettaglio in [How to do a manual Backup and Restore of Backup Exec (BEDB) database](http://www.veritas.com/docs/000041083) (Come eseguire backup e ripristino manuale del database Backup Exec (BEDB)). | È necessario ricompilare o ripristinare il server di Backup Exec nel sito di ripristino di emergenza. Ripristinare il database al punto più recente. Se il database Backup Exec ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante. |
| Errore del sito che comporta la perdita sia del server di backup che di StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi Backup Exec. | Ripristinare innanzitutto StorSimple e quindi Backup Exec. Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. |

## <a name="references"></a>Riferimenti

Questo articolo fa riferimento ai documenti seguenti:

- [Configurazione di Multipath I/O per StorSimple](storsimple-configure-mpio-windows-server.md)
- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [eseguire il ripristino da un set di backup](storsimple-restore-from-backup-set-u2.md).
- Informazioni su come eseguire le procedure di [failover e ripristino di emergenza di un dispositivo](storsimple-device-failover-disaster-recovery.md).
