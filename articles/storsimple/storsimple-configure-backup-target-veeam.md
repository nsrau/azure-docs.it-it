---
title: Configurare Microsoft Azure StorSimple con Veeam | Microsoft Docs
description: Descrive la configurazione della destinazione di backup StorSimple con Veeam.
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
ms.date: 12/06/2016
ms.author: hkanna
translationtype: Human Translation
ms.sourcegitcommit: dfecc291c441b862499d7eaaab421c7b72f929b2
ms.openlocfilehash: 2f71afc6c60cc22ef73a902ee67466313221def2

---

# <a name="configure-storsimple-with-veeam"></a>Configurare StorSimple con Veeam

## <a name="overview"></a>Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che risolve le complessità date dalla crescita esponenziale dei dati. Questa soluzione usa un account di archiviazione Azure come un'estensione della soluzione locale e organizza automaticamente i dati in livelli tra archiviazione locale e archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veeam e le procedure consigliate per l'integrazione di entrambe le soluzioni. Vengono inoltre illustrate indicazioni su come configurare Veeam per integrarsi al meglio con StorSimple. Rinviamo alle procedure consigliate di Veeam, ai progettisti e agli amministratori di backup per informazioni su come configurare al meglio Veeam per soddisfare le esigenze di backup e i contratti di servizio.

Questo articolo illustra i concetti chiave e passaggi di configurazione, ma non costituisce una guida dettagliata di configurazione o installazione. Nell'articolo si presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

## <a name="why-storsimple-as-a-backup-target"></a>Perché usare StorSimple come destinazione di backup?

StorSimple è una destinazione di backup ideale per i motivi seguenti:

-   Offre archiviazione locale standard per le applicazioni di backup da usare senza apportare modifiche per garantire una rapida destinazione di backup. StorSimple è inoltre disponibile per il ripristino rapido dei backup recenti.

-   La suddivisione in livelli del cloud è perfettamente integrata con un account di archiviazione cloud per l'uso dell'archiviazione economica di Microsoft Azure.

-   Offre automaticamente archiviazione esterna per il ripristino di emergenza.


## <a name="target-audience"></a>Destinatari

I destinatari di questo documento includono gli amministratori di backup, gli amministratori e i progettisti di archiviazione con conoscenza di archiviazione, Windows Server 2012 R2, Ethernet, servizi cloud e Veeam.

## <a name="supported-versions"></a>Versioni supportate

-   Veeam 9 e versioni successive.

-   [StorSimple Update 3 e versioni successive](/storsimple-overview#storsimple-workload-summary).



## <a name="key-concepts"></a>Concetti chiave

Come con qualsiasi altra soluzione di archiviazione, un'attenta valutazione delle prestazioni di archiviazione della soluzione, dei contratti di servizio, della frequenza di modifica e delle esigenze di crescita della capacità è fondamentale per ottenere un esito positivo. L'idea principale è che l'introduzione di un livello cloud, i tempi e le velocità effettive di accesso al cloud svolgono un ruolo fondamentale nella capacità di StorSimple di eseguire le rispettive attività.

StorSimple è progettato per garantire l'archiviazione per le applicazioni che usano un working set di dati ben definito (dati attivi). In questo modello viene archiviato il working set di dati nei livelli locali e il rimanente set di dati non in funzione, poco attivi o archiviati viene suddiviso in livelli nel cloud. Questo modello è illustrato nella figura seguente. La linea verde quasi piatta rappresenta i dati archiviati nei livelli locali del dispositivo StorSimple. La linea rossa rappresenta la quantità totale di dati archiviati nella soluzione StorSimple in tutti i livelli. Lo spazio tra la linea verde piatta e la curva esponenziale rossa rappresenta la quantità totale di dati archiviati nel cloud.

**Suddivisione in livelli di StorSimple**
![Diagramma di suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Tenendo presente questa architettura, si noterà che StorSimple è ideale per fungere da destinazione di backup. StorSimple consente di eseguire le operazioni seguenti:

-   Eseguire le operazioni di ripristino più frequenti dal working set di dati locale.

-   Usare il cloud per il ripristino di emergenza esterno e per i dati meno recenti in cui i ripristini sono meno frequenti.

## <a name="storsimple-benefits"></a>Vantaggi di StorSimple

StorSimple offre una soluzione locale che si integra facilmente con Microsoft Azure, sfruttando l'accesso automatico all'archiviazione locale e cloud.

StorSimple Usa la suddivisione automatica in livelli tra il dispositivo locale, che contiene l'archiviazione a stato solido (Solid State Drive, SSD), SAS (Serial-attached SCSI) e Archiviazione di Azure. La suddivisione automatica in livelli mantiene i dati a cui si accede di frequente in locale, nei livelli SSD e SAS, e sposta in Archiviazione di Azure i dati a cui si accede raramente.

StorSimple offre i seguenti vantaggi:

-   Algoritmi di deduplicazione e compressione univoci che usano il cloud per ottenere livelli di deduplicazione senza precedenti

-   Disponibilità elevata

-   Replica geografica usando la replica geografica di Azure

-   Integrazione di Azure

-   Crittografia dei dati nel cloud

-   Miglioramento del ripristino di emergenza e della conformità

Sebbene StorSimple presenti due scenari di distribuzione principali (destinazione di backup primaria e secondaria), tenere presente che fondamentalmente si tratta di un normale dispositivo di archiviazione a blocchi. StorSimple esegue tutte le operazioni di compressione e deduplicazione e invia e recupera facilmente i dati dal cloud all'applicazione e al file system.

Per altre informazioni su StorSimple, vedere [Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida](storsimple-overview.md) e rivedere le [Specifiche e conformità tecniche per il dispositivo StorSimple](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Il dispositivo StorSimple come destinazione di backup è supportato solo con StorSimple 8000 Update 3 o versione successiva.

## <a name="architecture-overview"></a>Panoramica dell'architettura

Le tabelle seguenti contengono le indicazioni iniziali da modello ad architettura per il dispositivo.

#### <a name="storsimple-capacities-for-local-and-cloud-storage"></a>Capacità di StorSimple per l'archiviazione locale e cloud


| Capacità di archiviazione       | 8100       | 8600       |
|------------------------|---------------|-----------------|
| Capacità di archiviazione locale | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Capacità di archiviazione cloud | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Le dimensioni di archiviazione si intendono senza alcuna deduplicazione o compressione.

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacità di StorSimple per il backup primario e secondario


| Scenario di backup  | Capacità di archiviazione locale                                         | Capacità di archiviazione cloud                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Backup primario   | Backup recenti archiviati nell'archivio locale per il ripristino rapido (RPO) | La cronologia dei backup (RPO) rientra nella capacità del cloud |
| Backup secondario | La copia secondaria dei dati di backup può essere archiviata nella capacità del cloud  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione di backup primaria

In questo scenario i volumi StorSimple vengono presentati all'applicazione di backup come unico repository per i backup. La figura seguente illustra l'architettura della soluzione in cui tutti i backup usano volumi a più livelli StorSimple per i backup e i ripristini.

![Diagramma logico con StorSimple come destinazione di backup primaria](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione primaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.

2.  Il server di backup scrive i dati nei volumi a più livelli StorSimple.

3.  Il server di backup aggiorna il database del catalogo e completa il processo di backup.

4.  Lo script di snapshot attiva la gestione di snapshot cloud di StorSimple.

5.  A seconda dei criteri di conservazione, il server di backup elimina i backup scaduti.

### <a name="primary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione primaria

1.  Il server di backup avvia il ripristino dei dati appropriati dal repository di archiviazione.

2.  L'agente di backup riceve i dati dal server di backup.

3.  Il server di backup completa il processo di ripristino.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione di backup secondaria

In questo scenario i volumi StorSimple vengono usati principalmente per la conservazione o l'archiviazione a lungo termine.

La figura seguente illustra l'architettura in cui i backup e i ripristini iniziali scelgono come destinazione un volume a prestazioni elevate. Questi backup vengono copiati e archiviati in un volume StorSimple a più livelli in una determinata pianificazione.

È importante impostare le dimensioni del volume a prestazioni elevate con spazio e prestazioni sufficienti a gestire i requisiti delle prestazioni e della capacità dei criteri di conservazione.

![Diagramma logico con StorSimple come destinazione di backup secondaria](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Passaggi logici per il backup nella destinazione secondaria

1.  Il server di backup contatta l'agente di backup di destinazione e l'agente di backup trasmette i dati al server di backup.

2.  Il server di backup scrive i dati nell'archiviazione a prestazioni elevate.

3.  Il server di backup aggiorna il database del catalogo e completa il processo di backup.

4.  A seconda dei criteri di conservazione, il server di backup copia i backup in StorSimple.

5.  Lo script di snapshot attiva la gestione di snapshot cloud di StorSimple.

6.  A seconda dei criteri di conservazione, il server di backup elimina i backup scaduti.

### <a name="secondary-target-restore-logical-steps"></a>Passaggi logici per il ripristino dalla destinazione secondaria

1.  Il server di backup avvia il ripristino dei dati appropriati dal repository di archiviazione.

2.  L'agente di backup riceve i dati dal server di backup.

3.  Il server di backup completa il processo di ripristino.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

La distribuzione di questa soluzione è costituita da tre passaggi: preparazione dell'infrastruttura di rete, distribuzione del dispositivo StorSimple come destinazione di backup e distribuzione del software Veeam. Ognuno dei passaggi precedenti viene illustrato nel dettaglio nelle sezioni seguenti.

### <a name="configure-the-network"></a>Configurare la rete

StorSimple come soluzione integrata con il cloud di Azure richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per ottenere prestazioni ottimali della soluzione, è consigliabile rispettare le procedure consigliate di rete seguenti:

-   il collegamento che connette la suddivisione in livelli di StorSimple ad Azure deve soddisfare i requisiti di larghezza di banda, tramite l'applicazione della Qualità del servizio (QoS) corretta alle opzioni dell'infrastruttura in modo che corrispondano ai contratti di servizio RPO/RTO.

-   Le latenze massime di accesso all'archivio BLOB di Azure devono essere comprese nell'intervallo di 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Distribuire Veeam

Per le procedure consigliate sull'installazione di Veeam, vedere [Procedure consigliate per Veeam9](https://bp.veeam.expert/) e il manuale dell'utente alla pagina [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Help Center Veeam (documentazione tecnica))

## <a name="configure-the-solution"></a>Configurare la soluzione

In questa sezione vengono descritti alcuni esempi di configurazione. Gli esempi e i suggerimenti seguenti illustrano l'implementazione più semplice ed essenziale. Questa implementazione potrebbe non essere applicabile direttamente ai propri requisiti specifici di backup.

### <a name="configure-storsimple"></a>Configurare StorSimple

| Attività di distribuzione di StorSimple                                                                                                                 | Commenti aggiuntivi                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Distribuire un dispositivo StorSimple locale.                                                                                    | Versione supportata: Update 3 e versioni successive.                                                                                                                                                                                                                                                                 |
| Abilitare la modalità di destinazione di backup.                                                                                                                | Usare i comandi seguenti per abilitare o disabilitare la modalità e ottenere lo stato. Per altre informazioni, vedere [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md).</br> Abilitare la modalità di backup:`Set-HCSBackupApplianceMode -enable`</br>  Disabilitare la modalità di backup:`Set-HCSBackupApplianceMode -disable`</br> Stato corrente delle impostazioni di modalità di backup`Get-HCSBackupApplianceMode` |
| Creare un contenitore comune per il volume che archivia i dati di backup. Vengono deduplicati tutti i dati in un contenitore del volume. | I contenitori di volume StorSimple definiscono i domini di deduplicazione.                                                                                                                                                                                                                                             |
| Creazione dei volumi StorSimple.                                                                                                                 | Creare volumi con dimensioni il più possibile simili all'uso previsto in quanto le dimensioni del volume possono influire sulla durata dello snapshot cloud. Per altre informazioni su come impostare le dimensioni di un volume, passare a [Criteri di conservazione](#retention-policies).</br> </br> Usare i volumi a più livelli StorSimple e selezionare **Usare questo volume per i dati di archivio a cui si accede non di frequente**. </br> I volumi aggiunti solo in locale non sono supportati.        |
| Creare un criterio di backup di StorSimple univoco per tutti i volumi della destinazione di backup.                                                               | Un criterio di backup di StorSimple definisce il gruppo di coerenza del volume.                                                                                                                                                                                                                                       |
| Disabilitare la pianificazione come snapshot.                                                                                                    | Gli snapshot vengono attivati come operazione di post-elaborazione.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |


### <a name="configure-host-backup-server-storage"></a>Configurare l'archiviazione del server di backup host

Assicurarsi che l'archiviazione del server di backup host sia configurata in base alle linee guida seguenti:  

- Non usare volumi con spanning, creati mediante il servizio di gestione dischi di Windows, in quanto non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione dell'unità di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server Veeam. 
    - Usare iSCSI per server fisici.


## <a name="best-practices-for-storsimple-and-veeam"></a>Procedure consigliate per StorSimple e Veeam

Configurare la soluzione in base alle linee guida seguenti.

### <a name="operating-system"></a>Sistema operativo

-   Disabilitare la crittografia di Windows Server e la deduplicazione per il file system NTFS.

-   Disabilitare la deframmentazione di Windows Server sui volumi StorSimple.

-   Disabilitare l'indicizzazione di Windows Server sui volumi StorSimple.

-   Eseguire una scansione antivirus dell'host di origine (non nei volumi StorSimple).

-   Disabilitare il valore predefinito [manutenzione di Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) in Gestione attività.

    - Disabilitare il configuratore di manutenzione nell'Utilità di pianificazione attività di Windows.

        oppure

    - Scaricare: [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx)

      - Dopo aver scaricato PSEXEC, eseguire Windows PowerShell come amministratore e digitare:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Assicurarsi che il dispositivo StorSimple sia aggiornato alla versione [Update 3 o successive](storsimple-install-update-3.md).

-   Isolare il traffico iSCSI e cloud. Usare connessioni iSCSI dedicate per il traffico tra StorSimple e il server di backup.

-   Assicurarsi che il dispositivo StorSimple sia una destinazione di backup dedicata. I carichi di lavoro misti non sono supportati in quanto influiscono su RTO/RPO.

### <a name="veeam"></a>Veeam

-   Il database Veeam deve essere locale nel server e non deve trovarsi in un volume StorSimple.

-   Per il ripristino di emergenza eseguire il backup del database Veeam in un volume StorSimple.

-   Per questa soluzione sono supportati i backup Veeam completi e incrementali. Si consiglia di non usare backup sintetici e differenziali.

-   I file dei dati di backup devono contenere solo dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta tra diversi processi.

-   Disabilitare la verifica dei processi. Se necessario, pianificare la verifica dopo l'ultimo processo di backup. È importante tenere presente che questo processo influisce sulla finestra di backup.

-   Disabilitare la preallocazione dei supporti.

-   Assicurarsi che l'elaborazione parallela sia abilitata.

-   Disabilitare la compressione.

-   Disabilitare la deduplicazione nel processo di backup.

-   Impostare ottimizzazione su **LAN Target** (Destinazione LAN).

-   Abilitare **Create active full backup** (Crea backup completo attivo) ogni 2 settimane.

-   Nel repository di backup, configurare **Use per-VM backup file** (Usa file di backup per ciascuna macchina virtuale).

-   Impostare **Use multiple upload streams per job** (Usa più flussi di caricamento per processo) su 8 (massimo 16 consentiti). Aumentare o diminuire in base all'uso della CPU sul dispositivo StorSimple.

## <a name="retention-policies"></a>Criteri di conservazione

Uno dei criteri di conservazione di backup più usati è GFS (Grandfather, Father and Son). In questo criterio viene eseguito ogni giorno un backup incrementale. I backup completi vengono eseguiti con frequenza settimanale e mensile. Questo criterio genera 6 volumi StorSimple a più livelli.

-   Un volume contiene i criteri di conservazione settimanali, mensili e annuali completi.

-   Gli altri 5 volumi archiviano i backup incrementali giornalieri.

Nell'esempio seguente si usa una rotazione GFS. Nell'esempio si presuppone quanto segue:

-   Vengono usati dati compressi o non duplicati.

-   I backup completi hanno dimensione di 1 TiB ciascuno.

-   I backup incrementali giornalieri hanno dimensione di 500 GiB ciascuno.

-   4 backup settimanali conservati per un mese.

-   12 backup mensili conservati per un anno.

-   1 backup annuale conservato per 10 anni.

In base ai presupposti precedenti creare un volume a più livelli StorSimple a 26 TiB per i backup mensili e annuali completi. Creare un volume a più livelli StorSimple a 5 TiB per ciascuno dei backup incrementali giornalieri.

| Conservazione per tipo di backup | Dimensioni TiB | Moltiplicatore GFS\*                                       | Capacità totale TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Settimanale completo           | 1        | 4                                                      | 4                           |
| Incrementale giornaliero     | 0,5      | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Mensile completo          | 1        | 12                                                     | 12                          |
| Annuale completo           | 1        | 10                                                     | 10                          |
| Requisito GFS       |          |                                                        | 38                          |
| Quota aggiuntiva      | 4        |                                                        | 42 requisito GFS totale.   |

\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i criteri di backup.

## <a name="configuring-veeam-storage"></a>Configurazione dell'archiviazione Veeam

1.  Passare alle impostazioni **Backup Infrastructure** (Infrastruttura di backup). Selezionare **Repository di backup**, fare clic con il tasto destro del mouse e selezionare **Add Backup Repository** (Aggiungi repository di backup).


    ![Console di gestione Veeam, schermata del repository di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

1.  Specificare un **Nome** e una **Descrizione** per il repository. Fare clic su **Avanti >**.

    ![Console di gestione Veeam, schermata con nome e descrizione](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

1.  Selezionare **Microsoft Windows Server** come repository di backup. Scegliere il server Veeam. Fare clic su **Avanti >**.

    ![Console di gestione Veeam, selezionare il tipo di repository di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

1.  Per specificare la **posizione**, individuare e selezionare il volume necessario. Impostare **Limit maximum concurrent tasks to:** (Limita numero massimo attività simultanee a:) su 4. In questo modo si garantisce che vengano elaborati contemporaneamente solo 4 dischi virtuali durante l'elaborazione di ciascuna macchina virtuale.
Fare clic su **Advanced**.

    ![Console di gestione Veeam, selezionare il volume](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


1.  Scegliere **Use per-VM backup files** (Usa file di backup per ciascuna macchina virtuale).


    ![Console di gestione Veeam, impostazioni di compatibilità di archiviazione](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

1.  Abilitare **vPower NFS service on the mount server (recommended)** (Servizio vPower NFS nel server di montaggio (scelta consigliata)).

    ![Console di gestione Veeam, schermata del repository di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

1.  Rivedere le impostazioni e procedere alla pagina successiva.

    ![Console di gestione Veeam, schermata del repository di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Viene aggiunto un repository al server Veeam.

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione di backup primaria

> [!IMPORTANT]
> Se è necessario ripristinare i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

Nella figura seguente viene illustrato il mapping di un volume tipico per un processo di backup. In questo caso tutti i backup settimanali vengono mappati nel disco completo di sabato e i backup incrementali vengono mappati in dischi incrementali dal lunedì al venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume a più livelli StorSimple.

![Diagramma logico di configurazione della destinazione di backup primaria ](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione di backup primaria

| Pianificazione di rotazione GFS per 4 settimane, mensile e annuale |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Tipo di backup o frequenza   | Completa          | Incrementale (giorno 1-5)  |
| Settimanale (settimana 1-4)    | Sabato | Da lunedì a venerdì |
| Mensile     | Sabato  |             |
| Annuale      | Sabato  |             |


### <a name="assigning-storsimple-volumes-to-a-veeam-backup-job"></a>Assegnazione di volumi StorSimple a un processo di backup Veeam

1.  Creare un processo giornaliero con Veeam StorSimplein in caso di uno scenario di destinazione di backup primaria o DAS/NAS/JBOD in caso di uno scenario di destinazione di backup secondaria come destinazione di backup. Passare a **Backup e replica &gt; Backup**. Fare clic con il tasto destro del mouse e selezionare VMware o Hyper-V in base all'ambiente.

    ![Console di gestione Veeam, nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

1.  Specificare un **Nome** e una **Descrizione** per il processo di backup giornaliero.

    ![Console di gestione Veeam, schermata nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

1.  Selezionare una macchina virtuale su cui eseguire il backup.

    ![Console di gestione Veeam, schermata nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

1.  Indicare in maniera appropriata le voci **Backup proxy** (Proxy di backup) e **Backup repository** (Repository di backup). Impostare **Restore points to keep on disk** (Punti di ripristino da mantenere su disco) in base alle definizioni RPO/RTO dell'ambiente sull'archivio collegato locale. Fare clic su Avanzate.

    ![Console di gestione Veeam, schermata nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

    Nella scehda **Backup** scegliere **Incrementale**. Accertarsi che l'opzione **Create synthetic full backups periodically** (Crea periodicamente backup completi sintetici) non sia selezionata.
 Abilitare **Create active full backups periodically** (Crea periodicamente backup completi attivi) e selezionare Settimanale sugli **Active full backups** (Backup completi attivi) abilitati ogni sabato.


    ![Console di gestione Veeam, schermata impostazioni avanzate nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

    Nella scheda Archiviazione, accertarsi che deduplicazione e compressione siano disabilitate e che lo scambio e l'eliminazione dei blocchi di file siano abilitati. Impostare **Storage optimization** (Ottimizzazione archiviazione) e **LAN Target** (Destinazione LAN) per avere prestazioni e deduplicazioni equilibrate.

    ![Console di gestione Veeam, schermata impostazioni avanzate nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Per informazioni dettagliate sulle impostazioni di deduplicazione e compressione, vedere [Deduplicazione e compressione dei dati](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)

1.  È possibile scegliere l'opzione **Enable Application Aware Processing** (Abilita elaborazione con riconoscimento dell'applicazione).


    ![Console di gestione Veeam, schermata elaborazione guest di un nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

1.  Impostare la pianificazione in modo che venga eseguita una volta al giorno a un orario specificato di propria scelta.

    ![Console di gestione Veeam, schermata Pianificazione nuovo processo di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione di backup secondaria

> [!NOTE]
> Per i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. È ad esempio possibile usare un volume RAID per contenere spazi, I/O e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

Nella figura seguente viene illustrato tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo caso tutti i backup vengono eseguiti nel volume RAID locale (per il server). Questi backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo da gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.



| Tipo di backup e conservazione                    |Archiviazione configurata| Dimensioni (TiB) | Moltiplicatore GFS | Capacità totale (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Settimana 1 (completo e incrementale) |Disco locale (breve termine)| 1        | 1              | 1           |
| StorSimple Settimana 2-4           |Disco StorSimple (lungo termine) | 1        | 4              | 4                   |
| Mensile completo                                 |Disco StorSimple (lungo termine) | 1        | 12             | 12                   |
| Annuale completo                               |Disco StorSimple (lungo termine) | 1        | 1              | 1                   |
|Requisiti di dimensione dei volumi GFS | |          |                | 18*|

\* La capacità totale include i 17 TiB dei dischi StorSimple e 1 TiB del volume RAID locale.

![Diagramma logico con StorSimple come destinazione di backup secondaria ](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

#### <a name="gfs-example-schedule"></a>Pianificazione di esempio GFS

| Pianificazione della rotazione GFS settimanale, mensile e annuale|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Settimana                                                                     | Completa               | Incrementale Giorno 1        | Incrementale Giorno 2        | Incrementale Giorno 3        | Incrementale Giorno 4        | Incrementale Giorno 5        |
| Settimana 1                                                                   | Volume RAID locale  | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale |
| Settimana 2                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 3                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 4                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Mensile                                                                  | StorSimple Mensile |                   |                   |                   |                   |                   |
| Annuale                                                                   | StorSimple Annuale  |                   |                   |                   |                   |                   |


### <a name="assigning-storsimple-volumes-to-a-veeam-copy-job"></a>Assegnazione di volumi StorSimple a un processo di copia Veeam

1.  Avviare la procedura guidata per un nuovo processo di copia di backup. Passare a **Processi > Copia di backup**. Selezionare VMware o Hyper-V in base al proprio ambiente.

    ![Console di gestione Veeam, schermata nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

1.  Specificare il nome e la descrizione del processo.

    ![Console di gestione Veeam, schermata nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

1.  Selezionare le macchine virtuali da elaborare e scegliere tra i backup quello giornaliero creato in precedenza.

    ![Console di gestione Veeam, schermata nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

1.  Se necessario, escludere gli oggetti dal processo di copia di backup.

1.  Selezionare **Backup repository** (Repository di backup), indicare un valore per il parametro **Restore points to keep** (Punti di ripristino da mantenere) e abilitare l'opzione **Keep the following restore points for archival purposes** (Mantieni i seguenti punti di ripristino per motivi di archiviazione).
    
 Definire la frequenza di backup e quindi fare clic su **Avanzate**.

    ![Console di gestione Veeam, schermata nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

1.  Specificare le impostazioni avanzate seguenti:

    -   Nella scheda **Manutenzione** disabilitare la protezione dal danneggiamento a livello di archiviazione.

    -   Nella scheda **Archiviazione** assicurarsi che deduplicazione e compressione siano disabilitate.

    ![Console di gestione Veeam, schermata impostazioni avanzate nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    ![Console di gestione Veeam, schermata impostazioni avanzate nuovo processo di copia di backup](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

1.  Specificare come diretto il trasferimento dati.

1.  Definire la pianificazione della finestra di copia di backup in base alle proprie esigenze e terminare.


Per altre informazioni, vedere [Creare processi di copia di backup](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Ciò equivale alla spedizione di nastri a una struttura esterna e, se si usa l'archiviazione con ridondanza geografica di Azure (GRS), equivale alla spedizione di nastri a più siti. Se è necessario un ripristino del dispositivo in una situazione di emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.


La sezione seguente illustra come creare un breve script per attivare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup. 

> [!NOTE] 
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-and-delete-cloud-snapshots-with-a-script"></a>Avviare ed eliminare gli snapshot cloud con uno script

> [!NOTE] 
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, fare riferimento alla documentazione di Veeam.


#### <a name="backup-lifecycle"></a>Ciclo di vita del backup


![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

#### <a name="requirements"></a>Requirements:

-   Il server che esegue lo script deve avere accesso al cloud Azure.

-   L'account utente deve disporre delle autorizzazioni necessarie.

-   Un criterio di Backup StorSimple con i volumi StorSimple associati configurato ma non abilitato.

-   Nome della risorsa StorSimple, chiave di registrazione, nome del dispositivo e ID dei criteri di backup.

#### <a name="steps"></a>Passaggi:

1.  [Installare Azure PowerShell](/powershell-install-configure/).

2.  [Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione.](https://msdn.microsoft.com/library/dn385850.aspx)

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
            Write-Host "This snpashotdate was created on " $SnapshotStartTimeStamp.Date.ToShortDateString()
            Write-Host "Instance ID " $SnapShotInstanceID
            Write-Host "This snpashotdate is older and needs to be deleted"
            Write-host "\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#"
            Remove-AzureStorSimpleDeviceBackup -DeviceName $ApplianceName -BackupId $SnapShotInstanceID -Force -Verbose
        }
    }
    ```

6.  Per aggiungere lo script al processo di backup, modificare le opzioni avanzate del processo Veeam. 

![Scheda script di impostazioni avanzate per il backup Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple alla fine del processo di backup giornaliero come script di post-elaborazione. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO/RTO, consultare il progettista del backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino
==============================

I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. Quando si ripristinano i dati suddivisi in più livelli nel cloud, ripristini vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini si verificano alla velocità del disco locale del dispositivo.

Veeam consente un ripristino rapido e granulare a livello di file tramite StorSimple, usando gli Explorer integrati nella console Veeam. Usare gli Explorer di Veeam per ripristinare elementi individuali come messaggi e-mail, oggetti di Active Directory o elementi SharePoint dai backup. Il ripristino può essere eseguito senza interruzioni alle macchine virtuali locali. È inoltre possibile eseguire il ripristino temporizzato per Microsoft SQL e Oracle Database. Veeam e StorSimple semplificano e rendono più veloce il processo di ripristino a livello di elemento da Azure. Per informazioni su come eseguire un ripristino, fare riferimento alla documentazione di Veeam.


- [https://www.veeam.com/microsoft-exchange-recovery.html](https://www.veeam.com/microsoft-exchange-recovery.html)

- [https://www.veeam.com/microsoft-active-directory-explorer.html](https://www.veeam.com/microsoft-active-directory-explorer.html)

- [https://www.veeam.com/microsoft-sql-server-explorer.html](https://www.veeam.com/microsoft-sql-server-explorer.html)

- [https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)

- [https://www.veeam.com/oracle-backup-recovery-explorer.html](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da vari fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.


| Scenario                                                                    | Impatto                                             | Come ripristinare                                                                                                                                                                               | Note                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Errore del dispositivo StorSimple.                                               | Le operazioni di backup e ripristino vengono interrotte. | Sostituire il dispositivo con errore ed eseguire [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md) | Se viene eseguito un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud nel nuovo dispositivo. Tutte le operazioni avverranno pertanto alle velocità del cloud. Inoltre, la ripetizione dell'indicizzazione e della catalogazione può fare in modo che tutti i set di backup vengano analizzati ed estratti dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante.                                 |
| Errore del server Veeam.                                                     | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database come illustrato in [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Help Center Veeam (documentazione tecnica))
     | Il server Veeam deve essere ricompilato o ripristinato nel sito del ripristino di emergenza. Ripristinare il database al punto più recente. Se il database Veeam ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. La ripetizione dell'indicizzazione e della catalogazione può fare in modo che tutti i set di backup vengano analizzati ed estratti dal livello cloud al livello del dispositivo locale. In questo modo il tempo sarà un fattore ancora più importante. |
| Errore del sito che comporta la perdita del server di backup e di StorSimple. | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi Veeam.                                                                                                                                                  | Ripristinare innanzitutto StorSimple e quindi Veeam. Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud nel nuovo dispositivo. Tutte le operazioni avverranno pertanto alle velocità del cloud.                                                                                                                                                                            |


## <a name="references"></a>Riferimenti

In questo articolo si è fatto riferimento ai documenti seguenti:

- [Installazione MPIO per StorSimple](storsimple-configure-mpio-windows-server.md)

- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Abilitare e configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)



<!--HONumber=Dec16_HO2-->


