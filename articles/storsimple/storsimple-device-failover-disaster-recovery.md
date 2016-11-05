---
title: Failover e ripristino di emergenza per StorSimple | Microsoft Docs
description: Informazioni su come eseguire il failover del dispositivo StorSimple su se stesso, su un altro dispositivo fisico o su un dispositivo virtuale.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: alkohli

---
# Failover e ripristino di emergenza per il dispositivo StorSimple
## Panoramica
Questa esercitazione descrive i passaggi necessari per eseguire il failover di un dispositivo StorSimple in caso di emergenza. Un failover consentirà di migrare i dati da un dispositivo di origine nel centro dati a un altro dispositivo fisico o virtuale che si trova nella stessa o in un’altra area geografica.

Il ripristino di emergenza viene gestito tramite la funzionalità di failover del dispositivo e viene inizializzato nella pagina **Dispositivi**. In questa pagina sono riportati tutti i dispositivi StorSimple connessi al servizio StorSimple Manager. Per ogni dispositivo, vengono visualizzati nome descrittivo, stato, capacità fornita e massima, tipo e modello.

![Pagina Dispositivi](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Le indicazioni fornite in questa esercitazione si applicano ai dispositivi fisici e virtuali StorSimple in tutte le versioni del software.

## Ripristino di emergenza (DR) e failover del dispositivo
In uno scenario di ripristino di emergenza, il dispositivo principale smette di funzionare. In questo caso, è possibile spostare i dati del cloud associati al dispositivo guasto in un altro dispositivo utilizzando il dispositivo principale come *origine* e specificando l’altro dispositivo come *destinazione*. È possibile selezionare uno o più contenitori di volumi per eseguire la migrazione al dispositivo di destinazione. Questo processo viene definito *failover*.

Durante il failover, i contenitori di volumi del dispositivo di origine cambiano proprietà e vengono trasferiti al dispositivo di destinazione. Una volta modificata la proprietà dei contenitori di volumi, questi vengono eliminati dal dispositivo di origine. Al termine dell'eliminazione, è possibile eseguire il failback del dispositivo di destinazione.

In genere, dopo un ripristino di emergenza, il backup più recente viene usato per ripristinare i dati nel dispositivo di destinazione. Tuttavia, se sono presenti più criteri di backup per lo stesso volume, viene scelto il criterio di backup con il maggior numero di volumi e per il ripristino dei dati nel dispositivo di destinazione viene usato il backup più recente per tale criterio.

Ad esempio, si supponga che esistano due criteri di backup (uno predefinito e uno personalizzato) *criterioPredefinito*, *criterioPersonalizzato* con i dettagli seguenti:

* *criterioPredefinito*: un solo volume, *vol1*, con esecuzione giornaliera a partire dalle 22.30.
* *criterioPersonalizzato*: quattro volumi, *vol1*, *vol2*, *vol3*, *vol4*, con esecuzione giornaliera a partire dalle 22.00.

In questo caso verrà usato *criterioPersonalizzato* perché include più volumi e viene data priorità alla coerenza per arresto anomalo del sistema. Per il ripristino dei dati viene usato il backup più recente per questo criterio.

## Considerazioni per il failover del dispositivo
In caso di emergenza, è possibile eseguire il failover del dispositivo StorSimple:

* In un dispositivo fisico
* Nel dispositivo stesso
* In un dispositivo virtuale

Per qualsiasi tipo di failover del dispositivo, tenere presente quanto segue:

* Per il ripristino di emergenza è necessario che tutti i volumi all'interno dei contenitori di volumi siano offline e che i contenitori di volumi siano associati a uno snapshot nel cloud.
* I dispositivi di destinazione disponibili per il ripristino di emergenza sono dispositivi con spazio sufficiente ad accogliere i contenitori di volumi selezionati.
* I dispositivi connessi al servizio che non soddisfano i criteri di spazio sufficiente non saranno disponibili come dispositivi di destinazione.
* Dopo un ripristino di emergenza, per un intervallo di tempo limitato, le prestazioni di accesso ai dati possono essere influenzate in modo significativo, dal momento che il dispositivo dovrà accedere ai dati dal cloud e memorizzarli in locale.

#### Failover del dispositivo in tutte le versioni del software
Un servizio StorSimple Manager in una distribuzione potrebbe avere più dispositivi, fisici e virtuali, che eseguono diverse versioni del software. In base alla versione del software, i tipi di volume nei dispositivi possono essere diversi. Ad esempio, un dispositivo che esegue l’Aggiornamento 2 o versione successiva avrebbe volumi aggiunti in locale e a livelli (con archiviazione in un sottoinsieme a livelli). Un dispositivo che esegue un pre-aggiornamento 2 d'altra parte può avere volumi di archiviazione a livelli.

Utilizzare la seguente tabella per determinare se è possibile eseguire il failover a un altro dispositivo che esegue una versione diversa del software e il comportamento dei tipi di volume durante il ripristino di emergenza.

| Failover da | Consentito per il dispositivo fisico | Consentito per il dispositivo virtuale |
| --- | --- | --- |
| Aggiornamento 2 a pre-aggiornamento 1 (versione, 0.1, 0.2, 0.3) |No |No |
| Aggiornamento 2 a Aggiornamento 1 (1, 1.1, 1.2) |Sì <br></br>se utilizza volumi aggiunti localmente o a livelli o una combinazione dei due, il failover dei volumi viene sempre eseguito come un volume a livelli. |Sì<br></br>se utilizza volumi aggiunti in locale, il failover viene eseguito come volume a livelli. |
| Aggiornamento 2 a Aggiornamento 2 (versione successiva) |Sì<br></br>se utilizza volumi aggiunti in locale o a livelli o una combinazione dei due, il failover viene sempre eseguito come il tipo di volume iniziale: volume a livelli come volume a livelli e volume aggiunto localmente come volume aggiunto localmente. |Sì<br></br>se utilizza volumi aggiunti in locale, il failover viene eseguito come volume a livelli. |

#### Failover parziale in tutte le versioni del software
Se si prevede di eseguire un failover parziale usando un dispositivo di origine StorSimple che esegue il pre-aggiornamento 1 in una destinazione che esegue Aggiornamento 1 o versione successiva, attenersi alle istruzioni che seguono.

| Origine del failover parziale | Consentito per il dispositivo fisico | Consentito per il dispositivo virtuale |
| --- | --- | --- |
| Da pre-aggiornamento 1 (versione 0.1, 0.2, 0.3) a Aggiornamento 1 o versione successiva |Sì, vedere la procedura consigliata di seguito. |Sì, vedere la procedura consigliata di seguito. |

> [!TIP]
> In Aggiornamento 1 e versioni successive è stata eseguita una modifica ai metadati del cloud e al formato dati. Per questa ragione, non è consigliabile eseguire un failover parziale da pre-aggiornamento 1 a Aggiornamento 1 o versioni successive. Se è necessario eseguire un failover parziale, è consigliabile applicare prima Aggiornamento 1 o versioni successive in entrambi i dispositivi (origine e destinazione) e quindi procedere con il failover.
> 
> 

## Failover su un altro dispositivo fisico
Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo fisico di destinazione.

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.
2. Nella pagina **Dispositivi** fare clic sulla scheda **Contenitori di volumi**.
3. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.
4. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.
5. Nella pagina **Dispositivi**, fare clic su **Failover**.
6. Nella procedura guidata visualizzata, in **Scegliere i contenitori dei volumi per il failover**:
   
   1. Nell'elenco dei contenitori di volumi, selezionare i contenitori di volumi di cui si desidera eseguire il failover. **Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**
   2. In **Scegli un dispositivo di destinazione** per i volumi dei contenitori selezionati, selezionare un dispositivo di destinazione dall'elenco a discesa dei dispositivi disponibili. Solo i dispositivi dotati di capacità disponibile vengono visualizzati nell'elenco a discesa.
   3. Infine, esaminare tutte le impostazioni di failover in **Conferma failover**. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. Viene creato un processo di failover che può essere monitorato tramite la pagina **Processi**. Se nel contenitore del volume di cui è stato eseguito il failover sono presenti volumi locali, i singoli processi di ripristino per ogni volume locale (non per i volumi a livelli) saranno visibili nel contenitore. Per il completamento di questi processi di ripristino può essere necessario molto tempo. È probabile che venga completato prima il processo di failover. Si noti che questi volumi presentano garanzie locali solo dopo il completamento dei processi di ripristino. Dopo aver completato il failover, andare alla pagina **Dispositivi**.
   
   1. Selezionare il dispositivo utilizzato come dispositivo di destinazione per il processo di failover.
   2. Andare alla pagina **Contenitori di volumi**. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

## Failover con un solo dispositivo
Se si dispone di un solo dispositivo ed è necessario eseguire un failover, eseguire i passaggi seguenti.

1. Acquisire snapshot nel cloud di tutti i volumi nel dispositivo.
2. Ripristinare le impostazioni predefinite del dispositivo. Seguire le istruzioni dettagliate in [come ripristinare le impostazioni predefinite di un dispositivo StorSimple](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Configurare il dispositivo e registrarlo nuovamente con il servizio StorSimple Manager.
4. Nella pagina **Dispositivi**, il dispositivo precedente dovrà essere visualizzato come **Offline**. Il dispositivo appena registrato deve essere visualizzato come **Online**.
5. Per il nuovo dispositivo, completare innanzitutto la configurazione minima del dispositivo.
   
   > [!IMPORTANT]
   > **Se non viene prima completata la configurazione minima, il ripristino di emergenza avrà esito negativo a causa di un bug nell'implementazione corrente. Questo problema verrà risolto in una versione successiva.**
   > 
   > 
6. Selezionare il dispositivo precedente (stato offline) e fare clic su **Failover**. Nella procedura guidata che viene visualizzata, eseguire il failover di questo dispositivo e specificare come dispositivo di destinazione il dispositivo appena registrato. Per istruzioni dettagliate, fare riferimento a [Failover su un altro dispositivo fisico](#fail-over-to-another-physical-device).
7. Verrà creato un processo di ripristino del dispositivo che è possibile monitorare da **Processi**.
8. Al termine il processo, accedere al nuovo dispositivo e andare alla pagina **Contenitori di volumi**. Tutti i contenitori di volumi del dispositivo precedente dovrebbero essere stati migrati al nuovo dispositivo.

## Failover su un dispositivo virtuale StorSimple
Prima di eseguire questa procedura, è necessario disporre di un dispositivo StorSimple virtuale creato e configurato. Se esegue l'Aggiornamento 2, considerare l'utilizzo di un dispositivo virtuale 8020 per il ripristino di emergenza che dispone di 64 TB e utilizza l'archiviazione Premium.

Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo virtuale StorSimple di destinazione.

1. Verificare che il contenitore del volume di cui si desidera eseguire il failover sia associato alle snapshot cloud.
2. Nella pagina **Dispositivi** fare clic sulla scheda **Contenitori di volumi**.
3. Selezionare un contenitore di volumi di cui si desidera eseguire il failover a un altro dispositivo. Fare clic sul contenitore di volumi per visualizzare l'elenco dei volumi presenti nel contenitore. Selezionare un volume e fare clic su **Offline** per passare il volume offline. Ripetere questo processo per tutti i volumi nel contenitore di volumi.
4. Ripetere il passaggio precedente per tutti i contenitori di volumi di cui si desidera eseguire il failover a un altro dispositivo.
5. Nella pagina **Dispositivi**, fare clic su **Failover**.
6. Nella procedura guidata visualizzata, sotto **Scegli contenitore di volumi per il failover**, completare le operazioni seguenti:
   
    a. Nell'elenco dei contenitori di volumi, selezionare i contenitori di volumi di cui si desidera eseguire il failover.
   
    **Vengono visualizzati solo i contenitori di volumi con gli snapshot del cloud e i volumi offline associati.**
   
    b. In **Scegli un dispositivo di destinazione per i volumi dei contenitori selezionati**, selezionare un dispositivo virtuale dall'elenco a discesa dei dispositivi disponibili. **Solo i dispositivi dotati di capacità sufficiente vengono visualizzati nell'elenco a discesa.**
7. Infine, esaminare tutte le impostazioni di failover in **Conferma failover**. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. Dopo aver completato il failover, andare alla pagina **Dispositivi**.
   
    a. Selezionare il dispositivo virtuale StorSimple utilizzato come dispositivo di destinazione per il processo di failover.
   
    b. Andare alla pagina **Contenitori di volumi**. Dovrebbero essere elencati tutti i contenitori di volumi, insieme ai volumi del dispositivo precedente.

![Video disponibile](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come è possibile ripristinare un dispositivo fisico in cui si è verificato un errore in un dispositivo virtuale nel cloud, fare clic [qui](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## Failback
Nell'aggiornamento 3 e versioni successive, StorSimple supporta anche il failback. Al completamento del processo di failover, si verifica quanto segue:

* I contenitori di volumi oggetto di failover vengono eliminati dal dispositivo di origine.
* Sul dispositivo di origine viene avviato un processo in background per ogni contenitore del volume (failover). Se si tenta di eseguire il failback mentre è in corso il processo, verrà visualizzata una notifica. Per avviare il failback, è necessario attendere il completamento del processo.
  
    Il tempo necessario per completare l'eliminazione dei contenitori dei volumi dipende da vari fattori, ad esempio la quantità e la data dei dati, il numero di backup e la larghezza di banda disponibile per l'operazione. Se si prevede di effettuare failover/failback di test, si consiglia di testare i contenitori dei volumi con meno dati (GB). Nella maggior parte dei casi è possibile avviare il failback 24 ore dopo il completamento del failover.

## Domande frequenti
D: **Cosa accade se il ripristino di emergenza non riesce o viene eseguito solo parzialmente?**

A. Se il ripristino di emergenza non riesce, si consiglia di tentare nuovamente. La seconda volta, il ripristino di emergenza conosce le operazioni eseguite e il punto in cui il processo si è bloccato. Il processo viene quindi ripreso da quel punto in poi.

D: **È possibile eliminare un dispositivo mentre è in corso il failover?**

A. Non è possibile eliminare un dispositivo durante un ripristino di emergenza. Il dispositivo può essere eliminato solo al termine del processo.

D: **Quando viene avviata l'operazione di Garbage Collection nel dispositivo di origine, in modo da eliminare i dati locali sul dispositivo di origine?**

A. L'operazione di Garbage Collection sul dispositivo di origine verrà attivata solo una volta ripulito il dispositivo. La pulizia riguarda gli oggetti sottoposti a failover dal dispositivo di origine, come volumi, oggetti di backup (non dati), contenitori di volumi e criteri.

D: **Cosa accade se il processo di eliminazione associato ai contenitori di volumi nel dispositivo di origine non riesce?**

A. In caso di errori del processo di eliminazione, è necessario attivare l'eliminazione dei contenitori di volumi manualmente. Nella pagina dedicata ai **dispositivi** selezionare il dispositivo di origine e fare clic su **Contenitori dei volumi**. Selezionare i contenitori di volumi precedentemente sottoposti a failover e fare clic sul pulsante di **eliminazione** in fondo alla pagina. Dopo avere eliminato dal dispositivo di origine tutti i contenitori dei volumi sottoposti a failover, è possibile avviare il failback.

## Ripristino di emergenza di continuità aziendale (BCDR)
Uno scenario di ripristino di emergenza di continuità aziendale (BCDR) si verifica quando l'intero data center di Azure smette di funzionare. Può influire sul servizio StorSimple Manager e sui dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima del verificarsi di un problema grave, per questi dispositivi StorSimple potrebbe essere necessario eseguire una reimpostazione di fabbrica. Dopo l'emergenza, il dispositivo StorSimple verrà visualizzato come offline. Il dispositivo StorSimple deve essere eliminato dal portale e deve essere eseguita una reimpostazione di fabbrica, seguita da una registrazione aggiornata.

## Passaggi successivi
* Dopo aver eseguito un failover, può essere necessario [disattivare o eliminare il dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).
* Per informazioni sull’utilizzo del servizio StorSimple Manager, passare a[utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0921_2016-->