---
title: StorSimple 8000 come destinazione di backup con NetBackup | Microsoft Docs
description: Descrive la configurazione della destinazione di backup di StorSimple con Veritas NetBackup.
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
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: cbc33fa15d069b55844cc6e4356ca5a01b40c199
ms.lasthandoff: 04/27/2017

---

# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple come destinazione di backup con NetBackup

## <a name="overview"></a>Panoramica

Azure StorSimple è una soluzione di archiviazione cloud ibrida di Microsoft. StorSimple consente di risolvere le complessità della crescita esponenziale dei dati usando un account di archiviazione di Azure come estensione della soluzione locale e suddividendo automaticamente i dati in livelli tra l'archiviazione locale e l'archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veritas NetBackup e le procedure consigliate per l'integrazione di entrambe le soluzioni. Vengono anche presentate alcune indicazioni su come configurare Veritas NetBackup per un'integrazione ottimale con StorSimple. Per la configurazione migliore di Veritas NetBackup al fine di soddisfare i requisiti di backup individuali e i Contratti di servizio, si rimanda alle procedure consigliate di Veritas, ai progettisti e agli amministratori di backup.

Anche se illustra i concetti chiave e i passaggi di configurazione, questo articolo non costituisce in alcun modo una guida dettagliata per la configurazione o l'installazione. L'articolo presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

### <a name="who-should-read-this"></a>A chi è rivolto questo articolo?

Le informazioni di questo articolo sono particolarmente utili per gli amministratori di backup, gli amministratori e i progettisti di archiviazione che conoscono l'archiviazione, Windows Server 2012 R2, Ethernet, i servizi cloud e Veritas NetBackup.

### <a name="supported-versions"></a>Versioni supportate

-   NetBackup 7.7.x e versioni successive
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
![Diagramma della suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

![Diagramma logico con StorSimple come destinazione primaria di backup](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione primaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.
2.  Il server di backup scrive i dati nei volumi StorSimple a livelli.
3.  Il server di backup aggiorna il database del catalogo e quindi completa il processo di backup.
4.  Uno script snapshot attiva la gestione di snapshot di StorSimple (start o delete).
5.  Il server di backup elimina i backup scaduti in base ai criteri di conservazione.

### <a name="primary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione primaria

1.  Il server di backup avvia il ripristino dei dati appropriati dall'archivio.
2.  L'agente di backup riceve i dati dal server di backup.
3.  Il server di backup completa il processo di ripristino.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione secondaria di backup

In questo scenario i volumi StorSimple vengono usati principalmente per la conservazione o l'archiviazione a lungo termine.

La figura seguente mostra un'architettura in cui i backup e i ripristini iniziali hanno come destinazione un volume a prestazioni elevate. Questi backup vengono copiati e archiviati in un volume StorSimple a livelli in una determinata pianificazione.

È importante impostare la dimensione del volume a prestazioni elevate in modo tale che possa gestire i requisiti di capacità e prestazioni dei criteri di conservazione.

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione secondaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.
2.  Il server di backup scrive i dati nell'archiviazione a prestazioni elevate.
3.  Il server di backup aggiorna il database del catalogo e quindi completa il processo di backup.
4.  Il server di backup copia i backup in StorSimple in base ai criteri di conservazione.
5.  Uno script snapshot attiva la gestione di snapshot di StorSimple (start o delete).
6.  Il server di backup elimina i backup scaduti in base ai criteri di conservazione.

### <a name="secondary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione secondaria

1.  Il server di backup avvia il ripristino dei dati appropriati dall'archivio.
2.  L'agente di backup riceve i dati dal server di backup.
3.  Il server di backup completa il processo di ripristino.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

La distribuzione della soluzione richiede tre passaggi:
1. Preparare l'infrastruttura di rete.
2. Distribuire il dispositivo StorSimple come destinazione dei backup.
3. Distribuire Veritas NetBackup.

I singoli passaggi sono descritti dettagliatamente nelle sezioni seguenti.

### <a name="set-up-the-network"></a>Configurare la rete

Poiché StorSimple è una soluzione integrata con il cloud di Azure, richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione dei dati e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per garantire prestazioni ottimali della soluzione, è consigliabile rispettare le seguenti procedure consigliate di rete:

-   Il collegamento fra i livelli di StorSimple e Azure deve soddisfare i requisiti di larghezza di banda. A tale scopo, applicare il livello di qualità del servizio (QoS) appropriato alle opzioni dell'infrastruttura per soddisfare i Contratti di servizio relativi a RPO e all'obiettivo tempo di ripristino (RTO).

-   Le latenze massime di accesso all'archiviazione BLOB di Azure devono essere di circa 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Distribuire NetBackup

Per una guida dettagliata alla distribuzione di NetBackup 7.7.x, vedere la [documentazione di NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

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

- Non usare volumi con spanning, creati mediante il servizio di gestione dischi di Windows, in quanto non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server di NetBackup.
    - Usare iSCSI per i server fisici.
    - Usare dischi pass-through per i server virtuali.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Procedure consigliate per StorSimple e NetBackup

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

### <a name="netbackup-best-practices"></a>Procedure consigliate di NetBackup

-   Il database di NetBackup deve essere locale nel server e non deve trovarsi in un volume StorSimple.
-   Per il ripristino di emergenza, eseguire il backup del database di NetBackup in un volume StorSimple.
-   Per questa soluzione sono supportati i backup completi e incrementali di NetBackup. Si consiglia di non usare backup sintetici e differenziali.
-   I file dei dati di backup devono contenere solo i dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta tra diversi processi.

Per le ultime impostazioni di NetBackup e le procedure consigliate per l'implementazione di questi requisiti, vedere la documentazione di NetBackup all'indirizzo [www.veritas.com](https://www.veritas.com).


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

## <a name="set-up-netbackup-storage"></a>Configurare l'archiviazione di NetBackup

### <a name="to-set-up-netbackup-storage"></a>Per configurare l'archiviazione di NetBackup

1.  Nella console di amministrazione di NetBackup selezionare **Media and Device Management** (Gestione dei supporti e dei dispositivi) > **Devices** (Dispositivi) > **Disk Pools** (Pool di dischi). Nella Configurazione guidata pool di dischi selezionare il tipo di server di archiviazione **AdvancedDisk** e poi fare clic su **Avanti**.

    ![Console di amministrazione di NetBackup, Configurazione guidata pool di dischi](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Selezionare il server e fare clic su **Avanti**.

    ![Console di amministrazione di NetBackup, selezionare il server](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Selezionare il volume StorSimple.

    ![Console di amministrazione di NetBackup, selezionare il volume StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Immettere un nome per la destinazione di backup e quindi selezionare **Avanti** > **Avanti** per completare la procedura guidata.

5.  Rivedere le impostazioni e selezionare **Finish** (Fine).

6.  Alla fine di ogni assegnazione di volume modificare le impostazioni del dispositivo di archiviazione in modo che corrispondano a quelle indicate in [Procedure consigliate per StorSimple e NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Ripetere i passaggi da 1 a 6 finché non si assegnano tutti i volumi StorSimple.

    ![Console di amministrazione di NetBackup, configurazione dei dischi](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Configurare StorSimple come destinazione di backup primaria

> [!NOTE]
> I ripristini dei dati da un backup suddiviso in livelli nel cloud vengono eseguiti alle velocità del cloud.

La figura seguente illustra il mapping di un volume tipico a un processo di backup. In questo caso, tutti i backup settimanali vengono mappati al disco Saturday full e i backup incrementali vengono mappati ai dischi incrementali dei giorni da lunedì a venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume a più livelli StorSimple.

![Diagramma logico di configurazione della destinazione primaria di backup ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione primaria di backup

Di seguito è riportato un esempio di una pianificazione a rotazione GFS per quattro settimane, mensile e annuale:

| Frequenza/Tipo di backup | Completa | Incrementale (giorni 1-5)  |   
|---|---|---|
| Settimanale (settimane 1-4) | Sabato | Lunedì-venerdì |
| Mensile  | Sabato  |   |
| Annuale | Sabato  |   |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Assegnazione di volumi StorSimple a un processo di backup NetBackup

Nella sequenza seguente si presuppone che NetBackup e l'host di destinazione siano configurati in conformità alle linee guida dell'agente di NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Per assegnare volumi StorSimple a un processo di backup NetBackup

1.  Nella console di amministrazione di NetBackup selezionare **NetBackup Management** (Gestione di NetBackup), fare clic con il pulsante destro del mouse su **Policies** (Criteri) e selezionare **New Policy** (Nuovo criterio).

    ![Console di amministrazione di NetBackup, creare un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2.  Nella finestra di dialogo **Add a New Policy** (Aggiungi nuovo criterio) inserire un nome per il criterio e quindi selezionare la casella di controllo **Use Policy Configuration Wizard** (Usa Configurazione guidata criteri). Selezionare **OK**.

    ![Console di amministrazione di NetBackup, finestra di dialogo Add a New Policy (Aggiungi nuovo criterio)](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3.  Nella Backup Policy Configuration Wizard (Configurazione guidata criteri) selezionare il tipo di backup desiderato e fare clic su **Next** (Avanti).

    ![Console di amministrazione di NetBackup, selezionare il tipo di backup](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4.  Per impostare il tipo di criterio, selezionare **Standard** e quindi fare clic su **Next** (Avanti).

    ![Console di amministrazione di NetBackup, selezionare il tipo di criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5.  Selezionare l'host, attivare la casella di controllo **Detect client operating system** (Rileva sistema operativo del client) e selezionare **Add** (Aggiungi). Selezionare **Avanti**.

    ![Console di amministrazione di NetBackup, elencare i client in un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6.  Selezionare le unità di cui si desidera eseguire il backup.

    ![Console di amministrazione di NetBackup, selezioni relative al backup per un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7.  Selezionare i valori di frequenza e conservazione che soddisfano i requisiti di rotazione dei backup.

    ![Console di amministrazione di NetBackup, frequenza e rotazione dei backup per un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8.  Selezionare **Next** (Avanti) > **Next** (Avanti) > **Finish** (Fine).  La pianificazione può essere modificata dopo la creazione dei criteri.

9.  Selezionare il criterio creato per espanderlo e selezionare **Pianificazioni**.

    ![Console di amministrazione di NetBackup, pianificazioni per un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10.  Fare clic con il pulsante destro del mouse su **Differential-Inc** (Differenziale-inc), selezionare **Copy to new** (Copia in nuovo) e quindi fare clic su **OK**.

    ![Console di amministrazione di NetBackup, copiare la pianificazione in un nuovo criterio](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11.  Fare clic con il pulsante destro del mouse sulla pianificazione appena creata e selezionare **Modifica**.

12.  Nella scheda **Attributi** attivare la casella di controllo **Sostituisci selezione archiviazione criteri** e quindi selezionare il volume in cui vengono inseriti i backup incrementali del lunedì.

    ![Console di amministrazione di NetBackup, modificare la pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13.  Nella scheda **Start Window** (Finestra di avvio) selezionare la finestra temporale per i backup.

    ![Console di amministrazione di NetBackup, modificare la finestra di avvio](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14.  Selezionare **OK**.

15.  Ripetere i passaggi da 10 a 14 per ogni backup incrementale. Selezionare il volume e la pianificazione appropriata per ogni backup creato.

16.  Fare clic con il pulsante destro del mouse sulla pianificazione **Differenziale-inc** ed eliminarla.

17.  Modificare la pianificazione completa in base alle esigenze di backup.

    ![Console di amministrazione di NetBackup, modificare la pianificazione completa](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18.  Modificare la finestra di avvio.

    ![Console di amministrazione di NetBackup, modificare la finestra di avvio](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19.  La pianificazione finale è simile alla seguente:

    ![Console di amministrazione di NetBackup, pianificazione finale](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Configurare StorSimple come destinazione secondaria di backup

> [!NOTE]
>I ripristini dei dati da un backup suddiviso in livelli nel cloud vengono eseguiti alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. Ad esempio, è possibile usare un volume RAID (Redundant Array of Independent Disks, array ridondante di dischi indipendenti) per ottenere spazio, input/output (I/O) e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

La figura seguente illustra tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo scenario tutti i backup vengono eseguiti nel volume RAID locale (per il server). i backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo che possa gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Esempio GFS con StorSimple come destinazione secondaria di backup

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

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


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Assegnare volumi StorSimple a un processo di archiviazione e duplicazione di NetBackup

Poiché NetBackup offre un'ampia gamma di opzioni per la gestione dell'archiviazione e dei supporti, si consiglia di consultare Veritas o il progettista NetBackup per valutare correttamente i requisiti SLP (Storage Lifecycle Policy, criteri del ciclo di vita dell'archiviazione).

Dopo aver definito i pool di dischi iniziali, è necessario definire altri tre criteri del ciclo di vita dell'archiviazione, per un totale di quattro criteri:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Per assegnare volumi StorSimple a un processo di archiviazione e duplicazione di NetBackup

1.  Nella console di amministrazione di NetBackup selezionare **Archiviazione** > **Criteri del ciclo di vita di archiviazione** > **Nuovi criteri del ciclo di vita di archiviazione**.

    ![Console di amministrazione di NetBackup, nuovi criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2.  Immettere un nome per lo snapshot e quindi selezionare **Add** (Aggiungi).

3.  Nella finestra di dialogo **New Operation** (Nuova operazione), sulla scheda **Properties** (Proprietà), per **Operation** (Operazione) selezionare **Backup**. Selezionare i valori desiderati per **Destination storage** (Archiviazione di destinazione), **Retention type** (Tipo di conservazione) e **Retention period** (Periodo di conservazione). Selezionare **OK**.

    ![Console di amministrazione di NetBackup, finestra di dialogo New Operation (Nuova operazione)](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

    In questo modo vengono definiti la prima operazione o il primo archivio.

4.  Selezionare l'operazione precedente per evidenziarla e quindi selezionare **Add** (Aggiungi). Nella finestra di dialogo **Change Storage Operation** (Modifica operazione di archiviazione) selezionare i valori desiderati per **Destination storage** (Archiviazione di destinazione), **Retention type** (Tipo di conservazione) e **Retention period** (Periodo di conservazione).

    ![Console di amministrazione NetBackup, finestra di dialogo Change Storage Operation (Modifica operazione di archiviazione)](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5.  Selezionare l'operazione precedente per evidenziarla e quindi selezionare **Add** (Aggiungi). Nella finestra di dialogo **Nuovi criteri del ciclo di vita di archiviazione** aggiungere backup mensili per un anno.

    ![Console di amministrazione di NetBackup, nuovi criteri del ciclo di vita dell'archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6.  Ripetere i passaggi 4 e 5 fino a creare i criteri di conservazione SLP completi necessari.

    ![Console di amministrazione di NetBackup, aggiunta di criteri nella finestra di dialogo Nuovi criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7.  Al termine della definizione dei criteri di conservazione SLP, in **Criterio**, definire un criterio di backup seguendo la procedura descritta in [Assegnazione di volumi StorSimple a un processo di backup NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8.  In **Pianificazioni**, nella finestra di dialogo **Modifica pianificazione**, fare clic con il pulsante destro del mouse su **Completo** e quindi selezionare **Modifica**.

    ![Console di amministrazione di NetBackup, finestra di dialogo Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9.  Selezionare la casella di controllo **Sostituisci selezione archiviazione criteri** e quindi selezionare il criterio di conservazione SLP creato nei passaggi da 1 a 6.

    ![Console di amministrazione NetBackup, Sostituisci selezione archiviazione criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10.  Fare clic su **OK**, quindi ripetere la procedura per la pianificazione del backup incrementale.

    ![Console di amministrazione di NetBackup, finestra di dialogo Modifica pianificazione per i backup incrementali](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Conservazione per tipo di backup | Dimensioni (TiB) | Moltiplicatore GFS\* | Capacità totale (TiB)  |
|---|---|---|---|
| Completo settimanale |  1  |  4 | 4  |
| Incrementale giornaliero  | 0,5  | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Completo mensile  | 1 | 12 | 12 |
| Completo annuale | 1  | 10 | 10 |
| Requisito GFS  |     |     | 38 |
| Quota aggiuntiva  | 4  |    | 42 (requisito GFS totale) |
\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i requisiti di backup.

## <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Creare uno snapshot cloud equivale a spedire i nastri di backup locali a una struttura esterna. Se si usa l'archiviazione con ridondanza geografica di Azure, la creazione di uno snapshot cloud equivale a spedire i nastri di backup a più siti. Se è necessario ripristinare un dispositivo dopo un'emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.

La sezione seguente descrive come creare un breve script per avviare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup.

> [!NOTE]
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Avviare ed eliminare gli snapshot cloud mediante uno script

> [!NOTE]
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, vedere la [documentazione di NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Ciclo di vita del backup

![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requisiti

-   Il server che esegue lo script deve avere accesso alle risorse del cloud Azure.
-   L'account utente deve avere le autorizzazioni necessarie.
-   Deve essere configurato ma non attivato un criterio di backup StorSimple con i volumi StorSimple associati.
-   Saranno necessari il nome della risorsa StorSimple, la chiave di registrazione, il nome del dispositivo e l'ID dei criteri di backup.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Per avviare o eliminare uno snapshot cloud

1.  [Installare Azure PowerShell](/powershell/azure/overview).
2.  [Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione](https://msdn.microsoft.com/library/dn385850.aspx).
3.  Nel portale di Azure classico ottenere il nome della risorsa e la [chiave di registrazione per il servizio StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).
4.  Nel server che esegue lo script eseguire PowerShell come amministratore. Digitare il comando seguente:

    `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Annotare l'ID del criterio di backup.
5.  Nel Blocco note creare un nuovo script di PowerShell mediante il codice seguente.

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
      Salvare lo script di PowerShell nello stesso percorso in cui sono state salvate le impostazioni di pubblicazione di Azure. Ad esempio, salvare il file come C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1.
6.  Aggiungere lo script al processo di backup in NetBackup. A tale scopo modificare i comandi di pre-elaborazione e post-elaborazione delle opzioni di processo di NetBackup.

> [!NOTE]
> È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple come script di post-elaborazione alla fine del processo di backup giornaliero. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO e RTO, consultare il progettista di backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino

I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. I ripristini dei dati suddivisi in livelli nel cloud vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini si verificano alla velocità del disco locale del dispositivo. Per informazioni su come eseguire un ripristino, vedere la [documentazione di NetBackup](http://www.veritas.com/docs/000094423). Si consiglia di adottare le procedure consigliate per il ripristino di NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da numerosi fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.

| Scenario | Impatto | Come ripristinare | Note |
|---|---|---|---|
| Errore del dispositivo StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Sostituire il dispositivo con errore ed eseguire [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md). | Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale e questo processo può richiedere molto tempo. |
| Errore del server di NetBackup | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database. | È necessario ricompilare o ripristinare il server di NetBackup nel sito di ripristino di emergenza. Ripristinare il database al punto più recente. Se il database di NetBackup ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante. |
| Errore del sito che comporta la perdita sia del server di backup che di StorSimple | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi NetBackup. | Ripristinare innanzitutto StorSimple e quindi NetBackup. Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni saranno eseguite alle velocità del cloud. |

## <a name="references"></a>Riferimenti

Questo articolo fa riferimento ai documenti seguenti:

- [Configurazione di Multipath I/O per StorSimple](storsimple-configure-mpio-windows-server.md)
- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [eseguire il ripristino da un set di backup](storsimple-restore-from-backup-set-u2.md).
- Informazioni su come eseguire le procedure di [failover e ripristino di emergenza di un dispositivo](storsimple-device-failover-disaster-recovery.md).

