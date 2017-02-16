---
title: Configurare StorSimple con Veritas NetBackup | Documentazione Microsoft
description: Descrive la configurazione della destinazione di backup di StorSimple con Veritas NetBackup.
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
ms.sourcegitcommit: 45fc96674d52a1e1273eed390195c59889b86a38
ms.openlocfilehash: 50c4be545f3b852d99968548784ffdff048d5355

---

# <a name="configure-storsimple-with-veritas-netbackup8482"></a>Configurare StorSimple con Veritas NetBackup&#8482;

## <a name="overview"></a>Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione cloud ibrida che consente di risolvere le complessità della crescita esponenziale dei dati usando un account di archiviazione Azure come estensione della soluzione locale e che suddivide automaticamente i dati in livelli tra l'archiviazione locale e l'archiviazione cloud.

Questo articolo illustra l'integrazione di StorSimple con Veritas NetBackup e le procedure consigliate per l'integrazione di entrambe le soluzioni. L'articolo presenta inoltre alcune indicazioni su come configurare Veritas NetBackup per una migliore integrazione con StorSimple. Per informazioni su come configurare al meglio Veritas NetBackup per soddisfare le esigenze di backup e i contratti di servizio, fare riferimento alle procedure consigliate di Veritas, agli amministratori e ai progettisti di backup.


Questo articolo illustra i concetti chiave e i passaggi di configurazione, ma non costituisce in alcun modo una guida dettagliata di configurazione o di installazione. Nell'articolo si presuppone che l'infrastruttura e i componenti di base funzionino correttamente e siano pronti per supportare i concetti descritti.

## <a name="why-storsimple-as-a-backup-target"></a>Perché usare StorSimple come destinazione di backup?

StorSimple è una destinazione di backup ideale per i motivi seguenti:

-   Fornisce l'archiviazione locale standard per le applicazioni di backup, da usare senza apportare modifiche per garantire una rapida destinazione di backup. StorSimple è inoltre disponibile per il ripristino rapido dei backup recenti.

-   La suddivisione in livelli nel cloud è perfettamente integrata con un account di archiviazione cloud per l'uso dell'economica soluzione di archiviazione di Microsoft Azure.

-   Fornisce automaticamente l'archiviazione esterna per il ripristino di emergenza.


## <a name="target-audience"></a>Destinatari

I destinatari di questo documento includono gli amministratori di backup, gli amministratori e i progettisti di archiviazione che hanno familiarità con l'archiviazione, Windows Server 2012 R2, Ethernet, i servizi cloud e NetBackup.

## <a name="supported-versions"></a>Versioni supportate

-   NetBackup 7.7.x e versione successiva.

-   [StorSimple Update 3 e versione successiva](storsimple-overview.md#storsimple-workload-summary).

## <a name="key-concepts"></a>Concetti chiave

Come con qualsiasi altra soluzione di archiviazione, per ottenere un esito positivo è fondamentale un'attenta valutazione delle esigenze relative alle prestazioni di archiviazione, ai contratti di servizio, alla frequenza di modifica e alla crescita di capacità della soluzione. L'idea principale è che con l'introduzione di un livello cloud, i tempi e le velocità effettive di accesso al cloud hanno un ruolo fondamentale nella capacità di StorSimple di soddisfare le esigenze dell'utente.

StorSimple è progettato per garantire l'archiviazione per le applicazioni che usano un working set di dati ben definito (dati attivi). In questo modello, il working set di dati viene archiviato nei livelli locali e il rimanente set di dati non in funzione, poco attivi o archiviati viene suddiviso in livelli nel cloud. Questo modello è illustrato nella figura seguente. La linea verde quasi piatta rappresenta i dati archiviati nei livelli locali del dispositivo StorSimple. La linea rossa rappresenta la quantità totale di dati archiviati nella soluzione StorSimple in tutti i livelli. Lo spazio tra la linea verde piatta e la curva esponenziale rossa rappresenta la quantità totale di dati archiviati nel cloud.

**Suddivisione in livelli di StorSimple**
![Diagramma della suddivisione in livelli di StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)


Tenendo presente questa architettura, si noterà che StorSimple è particolarmente adatto a essere usato come destinazione di backup. StorSimple consente di:

-   Eseguire le operazioni di ripristino più frequenti dal working set di dati locale.

-   Usare il cloud per il ripristino di emergenza esterno e per i dati meno recenti per i quali i ripristini sono meno frequenti.

## <a name="storsimple-benefits"></a>Vantaggi di StorSimple

StorSimple rappresenta una soluzione locale che si integra facilmente con Microsoft Azure, sfruttando i vantaggi offerti dall'accesso automatico all'archiviazione locale e cloud.

StorSimple usa la suddivisione automatica in livelli tra il dispositivo locale, contenente il dispositivo a stato solido (Solid State Drive, SSD) e l'archiviazione SAS (Serial-attached SCSI), e Archiviazione di Azure. La suddivisione automatica in livelli mantiene in locale, nei livelli SSD e SAS, i dati a cui si accede di frequente e sposta in Archiviazione di Azure i dati a cui si accede raramente.

StorSimple offre i vantaggi seguenti:

-   Algoritmi di deduplicazione e compressione univoci che usano il cloud per ottenere livelli avanzati di deduplicazione

-   Disponibilità elevata

-   Replica geografica usando la replica geografica di Azure

-   Integrazione di Azure

-   Crittografia dei dati nel cloud

-   Miglioramento del ripristino di emergenza e della conformità

Sebbene StorSimple presenti due scenari di distribuzione principali (destinazione di backup primaria e secondaria), si tenga presente che fondamentalmente si tratta di un normale dispositivo di archiviazione a blocchi. StorSimple esegue tutte le operazioni di compressione e deduplicazione, recupera facilmente i dati dal cloud e li invia all'applicazione e al file system.

Per altre informazioni su StorSimple, vedere [Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida](storsimple-overview.md) e le [specifiche tecniche del dispositivo StorSimple serie 8000](storsimple-technical-specifications-and-compliance.md).


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

#### <a name="storsimple-capacities-for-primary-and-secondary-backups"></a>Capacità di StorSimple per il backup primario e secondario


| Scenario di backup  | Capacità di archiviazione locale                                         | Capacità di archiviazione cloud                      |
|------------------|----------------------------------------------------------------|---------------------------------------------|
| Backup primario   | Backup recenti archiviati nell'archivio locale per il ripristino rapido (RPO). | La cronologia dei backup (RPO) rientra nella capacità del cloud. |
| Backup secondario | La copia secondaria dei dati di backup può essere archiviata nella capacità del cloud.  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione primaria di backup

In questo scenario i volumi StorSimple vengono presentati all'applicazione di backup come unico repository per i backup. La figura seguente illustra l'architettura della soluzione in cui tutti i backup usano i volumi StorSimple a livelli per i backup e i ripristini.

![Diagramma logico con StorSimple come destinazione primaria di backup](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

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

![Diagramma logico con StorSimple come destinazione secondaria di backup](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

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

La distribuzione di questa soluzione è costituita da tre passaggi: preparazione dell'infrastruttura di rete, distribuzione del dispositivo StorSimple come destinazione di backup e distribuzione di Veritas NetBackup. Ognuno dei passaggi precedenti viene illustrato nel dettaglio nelle sezioni seguenti.

### <a name="configure-the-network"></a>Configurare la rete

StorSimple come soluzione integrata con il cloud di Azure richiede una connessione al cloud di Azure attiva e funzionante. Questa connessione viene usata per operazioni quali gli snapshot cloud, la gestione e il trasferimento di metadati, nonché per la suddivisione in livelli di dati meno recenti e a cui si accede con minore frequenza nell'archiviazione cloud di Azure.

Per garantire prestazioni ottimali della soluzione, è consigliabile rispettare le procedure consigliate di rete riportate di seguito:

-   Il collegamento che connette la suddivisione in livelli di StorSimple ad Azure deve soddisfare i requisiti di larghezza di banda tramite l'applicazione della Qualità del servizio (QoS) corretta alle opzioni dell'infrastruttura, in modo che rispettino i contratti di servizio RPO/RTO.

-   Le latenze massime di accesso ad Archiviazione BLOB di Azure devono essere comprese nell'intervallo di 80 ms.

### <a name="deploy-storsimple"></a>Distribuire StorSimple

Per una guida dettagliata alla distribuzione di StorSimple, vedere [Distribuire un dispositivo StorSimple locale](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Distribuire NetBackup

Una guida dettagliata alla distribuzione di NetBackup 7.7.x è disponibile in [NetBackup 7.7.x Documentation](https://www.veritas.com/support/article.000094423) (Documentazione di NetBackup 7.7.x).

## <a name="configure-the-solution"></a>Configurare la soluzione

In questa sezione vengono descritti alcuni esempi di configurazione. Gli esempi e i suggerimenti seguenti illustrano l'implementazione più semplice ed essenziale. Questa implementazione potrebbe non essere applicabile direttamente ai requisiti specifici di backup dell'utente.

### <a name="configure-storsimple"></a>Configurare StorSimple

| Attività di distribuzione di StorSimple                                                                                                                 | Commenti aggiuntivi                                                                                                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Distribuire il dispositivo StorSimple locale                                                                                                 | Versione supportata: Update 3 e versione successiva.                                                                                                                                                                                                                                                                 |
| Abilitare la modalità di destinazione di backup                                                                                                                   | Usare i comandi seguenti per abilitare o disabilitare la modalità e ottenere lo stato. Per altre informazioni, vedere [Connettersi in remoto al dispositivo StorSimple](storsimple-remote-connect.md).</br> Abilitare la modalità di backup:`Set-HCSBackupApplianceMode -enable`</br>  Disabilitare la modalità di backup:`Set-HCSBackupApplianceMode -disable`</br> Stato corrente delle impostazioni della modalità di backup`Get-HCSBackupApplianceMode` |
| Creare un contenitore comune per il volume che archivia i dati di backup.   Tutti i dati contenuti in un contenitore di volumi vengono deduplicati. | I contenitori di volumi StorSimple definiscono i domini di deduplicazione.                                                                                                                                                                                                                                             |
| Creazione di volumi StorSimple                                                                                                                 | Creare volumi con dimensioni il più possibile simili all'utilizzo previsto in quanto le dimensioni dei volumi possono influire sulla durata dello snapshot cloud. Per altre informazioni su come impostare le dimensioni di un volume, passare a [Criteri di conservazione](#retention-policies).</br> </br> Usare i volumi StorSimple a livelli e selezionare **Usare questo volume per i dati di archivio a cui si accede non di frequente**. </br> I volumi solo aggiunti in locale non sono supportati.|
| Creare un criterio di backup di StorSimple univoco per tutti i volumi della destinazione di backup.                                                               | Un criterio di backup di StorSimple definisce il gruppo di coerenza del volume.                                                                                                                                                                                                                                       |
| Disabilitare la pianificazione alla scadenza degli snapshot.                                                                                                    | Gli snapshot vengono attivati come operazione di post-elaborazione.                                                                                                                                                                                                                                                         |
|                                                                                                     |                                             |



### <a name="configure-host-backup-server-storage"></a>Configurare l'archiviazione del server di backup host

Assicurarsi che l'archiviazione del server di backup host sia configurata in base alle linee guida seguenti.  

- I volumi con spanning, creati mediante il servizio di gestione dischi di Windows, non sono supportati.
- Formattare i volumi tramite NTFS con una dimensione dell'unità di allocazione di 64 KB.
- Mappare i volumi StorSimple direttamente al server di NetBackup. 
    - Usare iSCSI per i server fisici.
    - Usare dischi pass-through per i server virtuali.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Procedure consigliate per StorSimple e NetBackup

Configurare la soluzione in base alle linee guida seguenti.

### <a name="operating-system"></a>Sistema operativo

-   Disabilitare la crittografia di Windows Server e la deduplicazione per il file system NTFS.

-   Disabilitare la deframmentazione di Windows Server sui volumi StorSimple.

-   Disabilitare l'indicizzazione di Windows Server sui volumi StorSimple.

-   Eseguire una scansione antivirus dell'host di origine (non nei volumi StorSimple).

-   Disabilitare la [manutenzione di Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) predefinita in Gestione attività.

    - Disabilitare lo strumento di configurazione della manutenzione nell'Utilità di pianificazione attività di Windows.

        oppure

    - Scaricare [PSEXEC – Microsoft Sysinternals](https://technet.microsoft.com/sysinternals/bb897553.aspx).

      - Dopo aver scaricato PSEXEC, eseguire Windows PowerShell come amministratore e digitare:

            `psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable`

### <a name="storsimple"></a>StorSimple

-   Assicurarsi che il dispositivo StorSimple sia aggiornato alla versione [Update 3 o successiva](storsimple-install-update-3.md).

-   Isolare il traffico iSCSI e cloud. Usare connessioni iSCSI dedicate per il traffico tra StorSimple e il server di backup.

-   Assicurarsi che il dispositivo StorSimple sia una destinazione di backup dedicata. I carichi di lavoro misti non sono supportati in quanto influiscono sugli obiettivi RTO/RPO.

### <a name="netbackup"></a>NetBackup

-   Il database di NetBackup deve essere locale nel server e non deve trovarsi in un volume StorSimple.

-   Per il ripristino di emergenza, eseguire il backup del database di NetBackup in un volume StorSimple.

-   Per questa soluzione sono supportati i backup completi e incrementali di NetBackup. Si consiglia di non usare backup sintetici e differenziali.

-   I file dei dati di backup devono contenere solo dati per un processo specifico. Non è ad esempio consentito alcun supporto di aggiunta in processi diversi.

Per altre informazioni, vedere la [documentazione di NetBackup](https://www.veritas.com) per le impostazioni di NetBackup più recenti e per le procedure consigliate per implementare questi requisiti.


## <a name="retention-policies"></a>Criteri di conservazione

Uno dei criteri di conservazione di backup più usati è GFS (Grandfather, Father and Son). In questo criterio viene eseguito ogni giorno un backup incrementale. I backup completi vengono eseguiti con frequenza settimanale e mensile. Questo criterio genera 6 volumi StorSimple a livelli.

-   Un volume contiene i backup completi settimanali, mensili e annuali.

-   Gli altri 5 volumi archiviano i backup incrementali giornalieri.

Nell'esempio seguente si usa una rotazione GFS. Nell'esempio si presuppone quanto segue:

-   Vengono usati dati compressi o non deduplicati.

-   I backup completi hanno dimensione di 1 TiB ciascuno.

-   I backup incrementali giornalieri hanno dimensione di 500 GiB ciascuno.

-   4 backup settimanali conservati per un mese.

-   12 backup mensili conservati per un anno.

-   1 backup annuale conservato per 10 anni.

In base ai presupposti precedenti, creare un volume StorSimple a livelli da 26 TiB per i backup completi mensili e annuali. Creare un volume StorSimple a livelli da 5 TiB per ciascuno dei backup incrementali giornalieri.

| Conservazione per tipo di backup | Dimensioni TiB | Moltiplicatore GFS\*                                       | Capacità totale TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completo settimanale           | 1        | 4                                                      | 4                           |
| Incrementale giornaliero     | 0,5      | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Completo mensile          | 1        | 12                                                     | 12                          |
| Completo annuale           | 1        | 10                                                     | 10                          |
| Requisito GFS       |          |                                                        | 38                          |
| Quota aggiuntiva      | 4        |                                                        | 42 (requisito GFS totale)   |

\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i criteri di backup.

## <a name="configure-netbackup-storage"></a>Configurare l'archiviazione di NetBackup


1.  Nella console di gestione NetBackup selezionare Dispositivi\> avviare la Configurazione guidata pool di dischi\> selezionare\> AdvancedDisk\> e fare clic su Avanti.

    ![Console di gestione NetBackup - Configurazione pool di dischi](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

1.  Selezionare il server.

    ![Console di gestione NetBackup - Selezionare il server](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

1.  Fare clic su Avanti e selezionare il volume StorSimple.

    ![Console di gestione NetBackup - Selezionare il disco StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

1.  Assegnargli un nome, quindi fare clic su Avanti e di nuovo su Avanti per uscire.

    ![Console di gestione NetBackup - Schermata con nome e descrizione](./media/storsimple-configure-backup-target-using-netbackup/nbimage4.png)

    Dopo aver esaminato le impostazioni fare clic su Fine.

    1.  Alla fine di ogni assegnazione modificare le impostazioni del dispositivo di archiviazione in modo che corrispondano a quelle consigliate nell'elenco di procedure consigliate.

    2.  Ripetere i passaggi da 1 a 4 finché non si assegnano tutti i volumi StorSimple.

    ![Console di gestione NetBackup - Configurazione dischi](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple come destinazione primaria di backup

> [!NOTE]
> Tenere presente che se è necessario ripristinare i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

Nella figura seguente viene illustrato il mapping di un volume tipico a un processo di backup. In questo caso, tutti i backup settimanali vengono mappati al disco completo di sabato e i backup incrementali vengono mappati ai dischi incrementali dei giorni da lunedì a venerdì. Tutti i backup e i ripristini vengono eseguiti da un volume StorSimple a livelli.

![Diagramma logico di configurazione della destinazione primaria di backup ](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

#### <a name="storsimple-as-a-primary-backup-target-grandfather-father-and-son-gfs-schedule-example"></a>Esempio di pianificazione GFS con StorSimple come destinazione primaria di backup

| Pianificazione di rotazione GFS per 4 settimane, mensile e annuale |               |             |
|--------------------------------------------------------------------------|---------------|-------------|
| Frequenza/Tipo di backup   | Completa          | Incrementale (giorno 1-5)  |
| Settimanale (settimana 1-4)    | Sabato | Da lunedì a venerdì |
| Mensile     | Sabato  |             |
| Annuale      | Sabato  |             |


### <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Assegnazione di volumi StorSimple a un processo di backup NetBackup

Nella sequenza seguente si presuppone che NetBackup e l'host di destinazione siano configurati in conformità alle linee guida dell'agente di NetBackup.

1.  Nella console di gestione NetBackup selezionare Criteri\> e fare clic con il pulsante destro del mouse su\>Nuovi criteri.

    ![Schermata della console di gestione NetBackup](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

1.  Selezionare un nome di criterio e Usa la procedura guidata criteri.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

1.  Selezionare il tipo di backup appropriato, in questo caso File System, quindi Avanti.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

1.  Selezionare quindi Standard.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

1.  Selezionare l'host, quindi Rileva sistema operativo client e Aggiungi

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

1.  Quindi Avanti.

2.  Selezionare quindi l'unità di cui è necessario eseguire il backup, in questo caso G:\\DataChange3\\.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

1.  Selezionare la rotazione che soddisfa i requisiti.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

1.  Fare clic su Avanti, di nuovo Avanti e uscire. La pianificazione verrà modificata dopo la creazione dei criteri.

2.  Espandere i criteri creati e selezionare Pianificazioni.

    ![Console di gestione NetBackup - Nuovi criteri](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

1.  Fare clic con il pulsante destro del mouse su Differenziale-inc e selezionare Copia in nuovo, in questo caso Lun-inc e fare clic su OK.

    ![Console di gestione NetBackup - Nuovi criteri di pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

1.  Fare clic con il pulsante destro del mouse sulla pianificazione appena creata e scegliere Modifica.

2.  Nella scheda Attributi selezionare Sostituisci selezione archiviazione criteri e selezionare il volume in cui vengono inseriti i backup incrementali del lunedì. In questo caso, SS1.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

1.  Nella finestra Start selezionare la finestra per i backup. In questo caso, il lunedì dalle 20.00 alle 21.00.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

1.  Selezionare OK.

2.  Ripetere i passaggi da 11 a 15 per ciascun backup incrementale e selezionare il volume e la pianificazione appropriati.

3.  Fare quindi clic con il pulsante destro del mouse sulla pianificazione Differenziale-inc ed eliminarla.

4.  Dopo l'eliminazione della pianificazione Differenziale-inc, modificare la pianificazione Completa per soddisfare le esigenze.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

1.  Modificare la finestra di avvio, in questo caso sabato alle ore 06.00.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

1.  La pianificazione finale dovrebbe essere simile alla seguente.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple come destinazione secondaria di backup

> [!NOTE]
> Tenere presente che se è necessario ripristinare i dati da un backup suddiviso in livelli nel cloud, il ripristino viene eseguito alle velocità del cloud.

In questo modello è necessario un supporto di archiviazione diverso da StorSimple che funga da cache temporanea. È ad esempio possibile usare un volume RAID per contenere spazi, I/O e larghezza di banda. È consigliabile usare RAID 5, 50 e 10.

Nella figura seguente vengono illustrati tipici volumi locali (per il server) con conservazione a breve termine e volumi di archiviazione con conservazione a lungo termine. In questo caso, tutti i backup vengono eseguiti nel volume RAID locale (per il server). Questi backup vengono periodicamente duplicati e archiviati in un volume di archiviazione. È importante impostare le dimensioni del volume RAID locale (per il server) in modo da gestire i requisiti delle prestazioni e della capacità di conservazione a breve termine.

#### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>Esempio GFS con StorSimple come destinazione secondaria di backup

![Diagramma logico con StorSimple come destinazione secondaria di backup ](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

Nella tabella seguente viene illustrato come configurare i backup per l'esecuzione in dischi di StorSimple e locali, inclusi i requisiti di capacità singoli e totali.

#### <a name="backup-configuration-and-capacity-requirements"></a>Configurazione di backup e requisiti di capacità

| Tipo di backup e conservazione                    |Archiviazione configurata| Dimensioni (TiB) | Moltiplicatore GFS | Capacità totale (TiB)        |
|----------------------------------------------|-----|----------|----------------|------------------------|
| Settimana 1 (completo e incrementale) |Disco locale (breve termine)| 1        | 1              | 1           |
| StorSimple Settimana 2-4           |Disco StorSimple (lungo termine) | 1        | 4              | 4                   |
| Completo mensile                                 |Disco StorSimple (lungo termine) | 1        | 12             | 12                   |
| Completo annuale                               |Disco StorSimple (lungo termine) | 1        | 1              | 1                   |
|Requisiti di dimensione dei volumi GFS | |          |                | 18*|

\* La capacità totale include i 17 TiB dei dischi StorSimple e 1 TiB del volume RAID locale


#### <a name="gfs-example-schedule"></a>Pianificazione di esempio GFS

|Pianificazione della rotazione GFS settimanale, mensile e annuale|                    |                   |                   |                   |                   |                   |
|--------------------------------------------------------------------------|--------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| Settimana                                                                     | Completa               | Incrementale Giorno 1        | Incrementale Giorno 2        | Incrementale Giorno 3        | Incrementale Giorno 4        | Incrementale Giorno 5        |
| Settimana 1                                                                   | Volume RAID locale  | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale | Volume RAID locale |
| Settimana 2                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 3                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Settimana 4                                                                   | StorSimple Settimana 2-4 |                   |                   |                   |                   |                   |
| Mensile                                                                  | StorSimple Mensile |                   |                   |                   |                   |                   |
| Annuale                                                                   | StorSimple Annuale  |                   |                   |                   |                   |                   |


### <a name="assign-storsimple-volumes-to-netbackup-archiveduplication-job"></a>Assegnare volumi StorSimple a un processo di archiviazione/duplicazione di NetBackup.

Considerata la vasta gamma di opzioni per la gestione di archiviazione e supporti offerta da NetBackup, consultare Veritas o il progettista NetBackup per valutare correttamente i requisiti SLP.

1.  Dopo aver definito i pool iniziali di dischi, procedere alla definizione di 3 criteri del ciclo di vita di archiviazione:

    1.  LocalRAIDVolume

    2.  StorSimpleWeek2-4

    3.  StorSimpleMonthlyFulls

    4.  StorSimpleYearlyFulls

    Nella console di gestione, in Archiviazione, selezionare Criteri del ciclo di vita di archiviazione e Nuovi criteri del ciclo di vita di archiviazione.

    [Console di gestione NetBackup - Criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

1.  Selezionare un nome e fare clic su Aggiungi.

    ![Console di gestione NetBackup - Criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage21.png)

1.  Nella scheda delle proprietà selezionare Backup, Archiviazione di destinazione, la conservazione appropriata e quindi OK. In questo caso, LocalRAIDVolume e un periodo di conservazione di una settimana.

    ![Console di gestione NetBackup - Criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

1.  In questo modo, è stata definita la prima operazione o il primo repository di backup.

2.  Durante l'evidenziazione dell'operazione precedente fare clic su Aggiungi, selezionare Archiviazione di destinazione e la conservazione corretta. In questo caso, da LocalRAIDVolume a StorSimpleWeek2-4 e un periodo di conservazione di 1 mese.

    ![Console di gestione NetBackup - Nuovi criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

1.  Durante l'evidenziazione dell'operazione precedente selezionare Aggiungi e verranno aggiunti i backup mensili per un anno.

    ![Console di gestione NetBackup - Modifica i criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

1.  Ripetere i passaggi 5 e 6 fino a ottenere i criteri di conservazione SLP appropriati.

    ![Console di gestione NetBackup - Criteri del ciclo di vita di archiviazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

1.  Dopo aver definito i criteri di conservazione SLP appropriati, in Criteri definire i criteri di backup come illustrato nella sezione StorSimple come destinazione primaria.

1.  In Pianificazioni selezionare e fare clic con il pulsante destro del mouse su Completa, quindi scegliere Modifica.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

1.  Scegliere Sostituisci selezione archiviazione criteri e selezionare i criteri SLP creati nei passaggi 1-8.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

1.  Fare clic su OK, quindi ripetere la procedura per la Pianificazione del backup incrementale.

    ![Console di gestione NetBackup - Modifica pianificazione](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)

1.  Fare clic su OK, quindi ripetere la procedura per la pianificazione del backup incrementale.

| Conservazione per tipo di backup | Dimensioni TiB | Moltiplicatore GFS\*                                       | Capacità totale TiB          |
|-----------------------|----------|--------------------------------------------------------|-----------------------------|
| Completo settimanale           | 1        | 4                                                      | 4                           |
| Incrementale giornaliero     | 0,5      | 20 (il numero dei cicli è uguale al numero di settimane al mese) | 12 (2 per quota aggiuntiva) |
| Completo mensile          | 1        | 12                                                     | 12                          |
| Completo annuale           | 1        | 10                                                     | 10                          |
| Requisito GFS       |          |                                                        | 38                          |
| Quota aggiuntiva      | 4        |                                                        | 42 (requisito GFS totale)   |
\*Il moltiplicatore GFS è il numero di copie che è necessario proteggere e mantenere per soddisfare i criteri di backup.

### <a name="storsimple-cloud-snapshots"></a>Snapshot cloud StorSimple

Gli snapshot cloud StorSimple proteggono i dati che si trovano nel dispositivo StorSimple. Ciò equivale alla spedizione di nastri a una struttura esterna e, se si usa l'archiviazione con ridondanza geografica di Azure (GRS), alla spedizione di nastri a più siti. Se è stato necessario un ripristino del dispositivo in una situazione di emergenza, è possibile portare online un altro dispositivo StorSimple ed eseguire un failover. Dopo il failover sarà possibile accedere ai dati alle velocità del cloud dallo snapshot cloud più recente.

La sezione seguente illustra come creare un breve script per attivare ed eliminare gli snapshot cloud StorSimple durante la post-elaborazione dei backup.

> [!NOTE]
> Gli snapshot creati manualmente o a livello di codice non seguono i criteri di scadenza degli snapshot StorSimple. Devono essere eliminati manualmente o a livello di codice.

### <a name="start-delete-cloud-snapshots-with-a-script"></a>Snapshot cloud Start-Delete con uno script

> [!NOTE]
> Valutare attentamente la conformità e le ripercussioni della conservazione dei dati prima di eliminare uno snapshot StorSimple. Per altre informazioni su come eseguire uno script di post-backup, fare riferimento alla [documentazione di NetBackup](https://www.veritas.com/support/article.000094423).

#### <a name="backup-lifecycle"></a>Ciclo di vita del backup

![Diagramma del ciclo di vita del backup](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

#### <a name="requirements"></a>Requisiti:

-   Il server che esegue lo script deve avere accesso al cloud Azure.

-   L'account utente deve avere le autorizzazioni necessarie.

-   Un criterio di backup StorSimple con i volumi StorSimple associati configurato ma non abilitato.

-   Il nome della risorsa StorSimple, la chiave di registrazione, il nome del dispositivo e l'ID dei criteri di backup.

#### <a name="steps"></a>Passaggi:

1.  [Installare Azure PowerShell](/powershell-install-configure/).

2.  [Scaricare e importare le impostazioni di pubblicazione e le informazioni sulla sottoscrizione.](https://msdn.microsoft.com/library/dn385850.aspx)

3.  Nel portale di Azure classico ottenere il nome della risorsa e la [chiave di registrazione per il servizio StorSimple Manager](storsimple-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

4.  Nel server che esegue lo script eseguire Windows PowerShell come amministratore. Digitare:

    -   `Get-AzureStorSimpleDeviceBackupPolicy –DeviceName <device name>`

    Prendere nota dell'ID dei criteri di backup.

5.  Nel Blocco note creare un nuovo script di Windows PowerShell e salvarlo nella stessa posizione in cui sono state salvate le impostazioni di pubblicazione di Azure. Ad esempio: `C:\CloudSnapshot\StorSimpleCloudSnapshot.ps1`.

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

6.  Aggiungere lo script al processo di backup in NetBackup, modificando i comandi di pre-post relativi alle opzioni del processo di NetBackup.

> [!NOTE]
> È consigliabile eseguire i criteri di backup degli snapshot cloud StorSimple alla fine del processo di backup giornaliero come script di post-elaborazione. Per altre informazioni su come eseguire il backup e il ripristino dell'ambiente dell'applicazione di backup per soddisfare gli obiettivi RPO/RTO, consultare il progettista di backup.

## <a name="storsimple-as-a-restore-source"></a>StorSimple come origine di ripristino


I ripristini da StorSimple funzionano in modo analogo ai ripristini da qualsiasi dispositivo di archiviazione a blocchi. Quando si ripristinano i dati suddivisi in livelli nel cloud, i ripristini vengono eseguiti alle velocità del cloud. Per i dati locali, i ripristini vengono eseguiti alla velocità del disco locale del dispositivo. Per informazioni su come eseguire un ripristino, fare riferimento alla [documentazione di NetBackup](https://www.veritas.com/support/article.000094423) e rispettare le procedure consigliate di ripristino di NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>Failover e ripristino di emergenza per StorSimple

> [!NOTE]
> Per scenari di destinazione di backup, StorSimple Cloud Appliance non è supportato come destinazione di ripristino.

Una situazione di emergenza può essere causata da vari fattori. La tabella seguente elenca gli scenari di ripristino di emergenza più comuni.

| Scenario                                                                    | Impatto                                             | Come ripristinare                                                                                                                                                                               | Note                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Errore del dispositivo StorSimple                                                | Le operazioni di backup e ripristino verranno interrotte. | Sostituire il dispositivo con errore ed eseguire le procedure di [failover e ripristino di emergenza per StorSimple](storsimple-device-failover-disaster-recovery.md) | Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo, pertanto tutte le operazioni verranno eseguite alla velocità del cloud. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale e questo processo può richiedere molto tempo.                                                 |
| Errore del server di NetBackup                                              | Le operazioni di backup e ripristino vengono interrotte. | Ricompilare il server di backup ed eseguire il ripristino del database                                                                                                                                       | Il server di NetBackup dovrà essere ricompilato o ripristinato nel sito del ripristino di emergenza. Il database deve essere ripristinato al punto più recente. Se il database di NetBackup ripristinato non è sincronizzato con i processi di backup più recenti, è necessario eseguire l'indicizzazione e la catalogazione. Il processo di ripetizione dell'indicizzazione e della catalogazione può causare l'analisi e l'estrazione di tutti i set di backup dal livello cloud al livello del dispositivo locale. Il tempo sarà quindi un fattore ancora più importante. |
| Errore del sito che comporta la perdita del server di backup e di StorSimple. | Le operazioni di backup e ripristino vengono interrotte. | Ripristinare innanzitutto StorSimple e quindi NetBackup.                                                                                                                                          | Ripristinare innanzitutto StorSimple e quindi NetBackup.                                                                 Se è necessario eseguire un ripristino dopo il recupero del dispositivo, i working set di dati completi vengono recuperati dal cloud al nuovo dispositivo. Tutte le operazioni verranno eseguite pertanto alla velocità del cloud.|

## <a name="references"></a>Riferimenti

In questo articolo si è fatto riferimento ai documenti seguenti:

- [Installazione MPIO per StorSimple](storsimple-configure-mpio-windows-server.md)

- [Scenari di archiviazione: thin provisioning](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)

- [Uso di unità GPT](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)

- [Abilitare e configurare le copie shadow di cartelle condivise](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni:

- Informazioni su come [ripristinare un volume StorSimple da un set di backup](storsimple-restore-from-backup-set-u2.md).
- Informazioni su come eseguire le procedure di [failover e ripristino di emergenza di un dispositivo](storsimple-device-failover-disaster-recovery.md).



<!--HONumber=Dec16_HO2-->


