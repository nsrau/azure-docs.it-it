---
title: Failover e ripristino di emergenza di StorSimple per dispositivi serie 8000| Microsoft Docs
description: Informazioni su come eseguire il failover del dispositivo StorSimple su se stesso, su un altro dispositivo fisico o su un'applicazione cloud.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover e ripristino di emergenza per dispositivi StorSimple serie 8000

## <a name="overview"></a>Panoramica

Questo articolo descrive la funzionalità di failover per i dispositivi della serie StorSimple 8000 e come questa funzionalità consente di ripristinare i dispositivi StorSimple se si verifica un'emergenza. StorSimple usa la funzionalità di failover del dispositivo per eseguire la migrazione dei dati da un dispositivo di origine nel data center a un altro dispositivo target. Le indicazioni fornite in questo articolo si applicano ai dispositivi fisici StorSimple serie 8000 e alle applicazioni cloud in cui sono installate le versioni software Update 3 e successive.

StorSimple usa il pannello **Dispositivi** per avviare la funzionalità di failover del dispositivo in caso di emergenza. Il pannello riporta l'elenco di tutti i dispositivi StorSimple connessi al servizio Gestione dispositivi StorSimple.

![Pannello Dispositivi](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Ripristino di emergenza (DR) e failover del dispositivo

In uno scenario di ripristino di emergenza, il dispositivo principale smette di funzionare. StorSimple usa il dispositivo principale come _origine_ e sposta i dati cloud associati a un altro dispositivo di _destinazione_. Questo processo viene definito *failover*. Il grafico seguente illustra la procedura di failover.

![Che cosa avviene nel failover del dispositivo?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Il dispositivo di destinazione per un failover può essere un dispositivo fisico o persino un'applicazione cloud. Il dispositivo di destinazione può trovarsi nella stessa area geografica del dispositivo di origine o in un'area diversa.

Durante il failover è possibile selezionare i contenitori dei volumi per la migrazione. StorSimple modifica quindi la proprietà di questi contenitori di volumi del dispositivo di origine per il dispositivo di destinazione. Una volta modificata la proprietà dei contenitori di volumi, questi vengono eliminati dal dispositivo di origine. Al termine dell'eliminazione è possibile eseguire il failback del dispositivo di destinazione. Il _failback_ trasferisce nuovamente la proprietà al dispositivo di origine.

### <a name="cloud-snapshot-used-during-device-failover"></a>Snapshot cloud usato durante il failover del dispositivo

Dopo un ripristino di emergenza, il backup cloud più recente viene usato per ripristinare i dati nel dispositivo di destinazione. Per altre informazioni sugli snapshot nel cloud, vedere [Use the StorSimple Device Manager service to take a manual backup](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup) (usare il servizio di gestione dispositivi StorSimple per eseguire un backup manuale).

In una serie StorSimple 8000, i criteri di backup sono associati ai backup. Se sono presenti più criteri di backup per lo stesso volume, StorSimple seleziona i criteri di backup con il maggior numero di volumi. StorSimple usa quindi il backup più recente dal criterio di backup selezionato per ripristinare i dati nel dispositivo di destinazione.

Si supponga che siano disponibili due criteri di backup, *defaultPol* e *customPol*:

* *defaultPol*: un solo volume, *vol1*, con esecuzione giornaliera a partire dalle 22:30.
* *customPol*: quattro volumi, *vol1*, *vol2*, *vol3* e *vol4*, con esecuzione giornaliera a partire dalle 22:00.

In questo caso StorSimple darà priorità alla coerenza per arresto anomalo del sistema e userà *customPol* perché include più volumi. Per il ripristino dei dati viene usato il backup più recente per questo criterio. Per altre informazioni su come creare e gestire i criteri di backup, passare a [Use the StorSimple Device Manager service to manage backup policies](storsimple-8000-manage-backup-policies-u2.md) (usare il servizio di gestione dispositivi StorSimple per gestire i criteri di backup).

## <a name="common-considerations-for-device-failover"></a>Considerazioni comuni per il failover del dispositivo

Prima di eseguire il failover di un dispositivo, consultare le informazioni seguenti:

* Prima di avviare il failover del dispositivo, tutti i volumi nei contenitori devono essere offline. In un failover non pianificato, i volumi StotSimple passeranno automaticamente alla modalità offline. Tuttavia, se si esegue un failover pianificato (per il test di un ripristino di emergenza) è necessario portare tutti i volumi offline.
* Solo i contenitori di volumi associati a uno snapshot cloud sono elencati per il ripristino di emergenza. Deve essere presente almeno un contenitore di volumi con snapshot cloud associato per ripristinare i dati.
* Se sono presenti snapshot cloud che si estendono su più contenitori di volumi, StorSimple esegue il failover di questi contenitori di volumi come set. In un'istanza rara, se sono presenti degli snapshot locali che si estendono su più contenitori di volumi ma non sono presenti snapshot cloud associati, StorSimple esegue il failover degli snapshot locali e i dati locali vanno persi dopo il ripristino di emergenza.
* I dispositivi di destinazione disponibili per il ripristino di emergenza sono dispositivi con spazio sufficiente ad accogliere i contenitori di volumi selezionati. I dispositivi che non dispongono di spazio sufficiente non sono elencati come dispositivi di destinazione.
* Dopo un ripristino di emergenza, per un intervallo di tempo limitato, le prestazioni di accesso ai dati possono essere influenzate in modo significativo, dal momento che il dispositivo dovrà accedere ai dati dal cloud e memorizzarli in locale.

#### <a name="device-failover-across-software-versions"></a>Failover del dispositivo in tutte le versioni del software

Un servizio Gestione dispositivi di StorSimple in una distribuzione potrebbe avere più dispositivi, fisici e cloud, che eseguono diverse versioni del software.

Usare la seguente tabella per determinare se è possibile eseguire il failover o il failback su un altro dispositivo che esegue una versione diversa del software e il comportamento dei tipi di volume durante il ripristino di emergenza.

#### <a name="failover-and-failback-across-software-versions"></a>Failover e failback del dispositivo in tutte le versioni del software

| Effettuare il failover/failback da | Dispositivo fisico | Appliance cloud |
| --- | --- | --- |
| Da Aggiornamento 3 ad Aggiornamento 4 |I volumi a livelli eseguono il failover su più livelli. <br></br>I volumi aggiunti in locale eseguono il failover aggiunto in locale. <br></br> Dopo un failover, quando si crea uno snapshot nel dispositivo con Aggiornamento 4 interviene il [rilevamento basato su heatmap](storsimple-update4-release-notes.md#whats-new-in-update-4). |I volumi aggiunti in locale eseguono il failover su più livelli. |
| Da Aggiornamento 4 ad Aggiornamento 3 |I volumi a livelli eseguono il failover su più livelli. <br></br>I volumi aggiunti in locale eseguono il failover aggiunto in locale. <br></br> Backup usati per ripristinare i metadati heatmap mantenuti. <br></br>Rilevamento basato su Heatmap non è disponibile in Aggiornamento 3 a seguito di un failback. |I volumi aggiunti in locale eseguono il failover su più livelli. |


## <a name="device-failover-scenarios"></a>Scenari di failover del dispositivo

In caso di emergenza è possibile eseguire il failover del dispositivo StorSimple:

* [In un dispositivo fisico](storsimple-8000-device-failover-physical-device.md).
* [Nel dispositivo stesso](storsimple-8000-device-failover-same-device.md).
* [In un'appliance cloud](storsimple-8000-device-failover-cloud-appliance.md).

Gli articoli precedenti descrivono in dettaglio i passaggi di ognuno dei casi precedenti di failover.


## <a name="failback"></a>Failback

Nell'aggiornamento 3 e versioni successive, StorSimple supporta anche il failback. Il failback è semplicemente l'opposto del failover: la destinazione diventa l'origine e il dispositivo di origine durante il failover diventa ora il dispositivo di destinazione. 

Durante il failback, StorSimple risincronizza i dati nel percorso primario, arresta le attività I/O dell'applicazione ed esegue nuovamente la transizione nel percorso originale.

Dopo aver completato un failover, StorSimple esegue le azioni seguenti:

* StorSimple elimina i contenitori di volumi oggetto di failover dal dispositivo di origine.
* StorSimple avvia un processo in background per ogni contenitore del volume (failover) sul dispositivo di origine. Se si tenta di eseguire il failback mentre è in corso il processo, verrà visualizzata una notifica. Per avviare il failback, è necessario attendere il completamento del processo.
* Il tempo necessario per completare l'eliminazione dei contenitori dei volumi dipende da vari fattori, ad esempio la quantità e la data dei dati, il numero di backup e la larghezza di banda disponibile per l'operazione.

Se si prevede di effettuare failover/failback di test, si consiglia di testare i contenitori dei volumi con meno dati (GB). Di norma è possibile avviare il failback 24 ore dopo il completamento del failover.

## <a name="frequently-asked-questions"></a>Domande frequenti

D: **Cosa accade se il ripristino di emergenza non riesce o viene eseguito solo parzialmente?**

R. Se il ripristino di emergenza non riesce, si consiglia di tentare nuovamente. Il secondo processo di failover del dispositivo è a conoscenza dello stato di avanzamento del processo precedente e si avvia da quel punto in poi.

D: **È possibile eliminare un dispositivo mentre è in corso il failover?**

R. Non è possibile eliminare un dispositivo durante un ripristino di emergenza. Il dispositivo può essere eliminato solo al termine del processo. È possibile monitorare lo stato del processo di failover del dispositivo nel pannello **Processi**.

D: **Quando viene avviata l'operazione di Garbage Collection nel dispositivo di origine, in modo da eliminare i dati locali sul dispositivo di origine?**

R. L'operazione di Garbage Collection sul dispositivo di origine verrà attivata solo una volta ripulito il dispositivo. La pulizia riguarda gli oggetti sottoposti a failover dal dispositivo di origine, come volumi, oggetti di backup (non dati), contenitori di volumi e criteri.

D: **Cosa accade se il processo di eliminazione associato ai contenitori di volumi nel dispositivo di origine non riesce?**

R.  Se il processo di eliminazione non riesce, è possibile eliminare manualmente i contenitori di volumi. Nel pannello **Dispositivi** selezionare il dispositivo di origine e fare clic su **Contenitori dei volumi**. Selezionare i contenitori di volumi precedentemente sottoposti a failover e fare clic sul pulsante di **eliminazione**in fondo al pannello. Dopo avere eliminato dal dispositivo di origine tutti i contenitori dei volumi sottoposti a failover, è possibile avviare il failback. Per altre informazioni, visitare [Eliminare un contenitore del volume](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Ripristino di emergenza di continuità aziendale (BCDR)

Uno scenario di ripristino di emergenza di continuità aziendale (BCDR) si verifica quando l'intero data center di Azure smette di funzionare. Questo scenario può influire sul servizio Gestione dispositivi StorSimple e sui dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima del verificarsi di un problema grave, per questi dispositivi potrebbe essere necessario eseguire una reimpostazione di fabbrica. Dopo l'emergenza, il dispositivo StorSimple viene visualizzato nel portale di Azure come offline. Il dispositivo deve essere eliminato dal portale. Ripristinare le impostazioni predefinite di fabbrica del dispositivo e ripetere la registrazione con il servizio.

## <a name="next-steps"></a>Passaggi successivi

Se si è pronti per eseguire un failover del dispositivo, scegliere uno degli scenari seguenti per informazioni dettagliate:

- [Failover su un altro dispositivo fisico](storsimple-8000-device-failover-physical-device.md)
- [Failover sullo stesso dispositivo](storsimple-8000-device-failover-same-device.md)
- [Failover nell'appliance cloud StorSimple](storsimple-8000-device-failover-cloud-appliance.md)

Se il failover sul dispositivo non riesce, scegliere una delle opzioni seguenti:

* [Disattivare o eliminare un dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Usare il servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

