---
title: Gestire i volumi StorSimple (aggiornamento 3) | Microsoft Docs
description: Viene illustrato come aggiungere, modificare, monitorare ed eliminare i volumi StorSimple e come portarli offline, se necessario.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: c9c575f42e6c8730b9404c62fb60e710d9d3bc80
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Per gestire il volume è possibile usare il servizio Gestione dispositivi StorSimple (aggiornamento 3 o successivo)

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come usare il servizio Gestione dispositivi StorSimple per creare e gestire i volumi nel dispositivo StorSimple serie 8000 con aggiornamento 3 o successivo.

Il servizio Gestione dispositivi StorSimple è un'estensione del portale di Azure che consente di gestire la soluzione StorSimple da un'unica interfaccia Web. Usare il portale di Azure per gestire volumi su tutti i dispositivi. È anche possibile creare e gestire i servizi StorSimple, gestire i dispositivi, i criteri di backup e il catalogo di backup e visualizzare gli avvisi.

## <a name="volume-types"></a>Tipi di volume

I volumi di StorSimple possono essere:

* **Volumi aggiunti in locale**: i dati in questi volumi rimangono sempre nel dispositivo StorSimple locale.
* **Volumi a livelli**: i dati in questi volumi essere distribuiti nel cloud.

Un volume di archivio è un tipo di volume a livelli. La maggiore dimensione del blocco di deduplicazione usata per i volumi di archivio consente al dispositivo di trasferire nel cloud segmenti di dati più grandi.

Se necessario, è possibile passare da un tipo di volume locale a uno a livelli o viceversa. Per altre informazioni, vedere [Modificare il tipo di volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumi aggiunti in locale

I volumi aggiunti in locale sono volumi con provisioning completo che non dispongono i dati su livelli nel cloud, assicurando così garanzie locali per i dati primari, indipendentemente dalla connettività cloud. I dati nei volumi aggiunti in locale non vengono deduplicati e compressi, mentre gli snapshot dei volumi aggiunti in locale vengono deduplicati. 

Poiché viene effettuato il provisioning completo dei volumi aggiunti in locale, è necessario avere spazio sufficiente nel dispositivo quando li si crea. È possibile effettuare il provisioning dei volumi aggiunti in locale fino a una dimensione massima di 8 TB nel dispositivo StorSimple 8100 e di 20 TB nel dispositivo 8600. StorSimple riserva lo spazio locale restante nel dispositivo a snapshot, metadati ed elaborazione dati. È possibile aumentare la dimensione di un volume aggiunto in locale fino alla massima quantità di spazio disponibile, ma non è possibile ridurre la dimensione di un volume una volta creato.

Quando si crea un volume aggiunto in locale, viene ridotto lo spazio disponibile per la creazione di volumi a livelli. È anche vero il contrario: se esistono già volumi a livelli, lo spazio disponibile per la creazione di volumi aggiunti in locale sarà inferiore ai limiti massimi definiti prima. Per altre informazioni sui volumi locali, vedere le [domande frequenti sui volumi aggiunti in locale](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Volumi a livelli

I volumi a livelli sono volumi con thin provisioning in cui i dati a cui si accede più spesso rimangono in locale nel dispositivo, mentre quelli usati meno spesso vengono automaticamente disposti su livelli nel cloud. Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo spazio di archiviazione sufficiente, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire lo spazio cloud in base alle variazioni nella domanda.

Se si usa il volume a livelli per i dati di archiviazione, selezionando la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente** si porta a 512 KB la dimensione del blocco di deduplicazione per il volume. Se non si seleziona questa opzione, il volume a livelli corrispondente utilizzerà una dimensione del blocco di 64 KB. Una dimensione maggiore del blocco di deduplicazione consente al dispositivo di accelerare il trasferimento dei dati di archivio di grandi dimensioni nel cloud.


### <a name="provisioned-capacity"></a>Capacità con provisioning

Fare riferimento alla tabella seguente per conoscere la capacità massima di cui viene effettuato il provisioning per ogni tipo di dispositivo e di volume. Si noti che i volumi aggiunti in locale non sono disponibili in un dispositivo virtuale.

|  | Dimensione massima volume a livelli | Dimensione massima volume aggiunto in locale |
| --- | --- | --- |
| **Dispositivi fisici** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivi virtuali** | | |
| 8010 |30 TB |N/D |
| 8020 |64 TB |N/D |

## <a name="the-volumes-blade"></a>Il pannello Volumi

Il pannello **Volumi** consente di gestire i volumi di archiviazione forniti nel dispositivo Microsoft Azure StorSimple per gli iniziatori (server). Visualizza l'elenco di volumi sui dispositivi StorSimple connessi al servizio.

 ![Pagina dei volumi](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Un volume è costituito da una serie di attributi:

* **Nome volume** : un nome descrittivo che deve essere univoco e consente di identificare il volume. Questo nome viene utilizzato anche nei rapporti di monitoraggio quando di applica un filtro su un volume specifico. Una volta creato, il volume non può essere rinominato.
* **Stato** : online oppure offline. Se un volume è offline, non è visibile agli iniziatori (server) che possono accedervi per usarlo.
* **Capacità**: specifica la quantità totale di dati che può essere archiviata dall'iniziatore (server). Dei volumi aggiunti in locale viene effettuato il provisioning completo. Tali volumi sono nel dispositivo StorSimple. Dei volumi a livelli Viene effettuato il thin provisioning e i dati vengono deduplicati. Con i volumi con thin provisioning, il dispositivo non prealloca la capacità di archiviazione fisica internamente o nel cloud in base alle capacità del volume configurato. La capacità del volume viene allocata e utilizzata su richiesta.
* **Tipo**: indica se il volume è **A livelli** (impostazione predefinita) o **Aggiunto in locale**.

Usare le istruzioni di questa esercitazione per eseguire le attività seguenti:

* Aggiungere un volume 
* Modificare un volume 
* Modificare il tipo di volume
* Eliminare un volume 
* Portare un volume offline 
* Monitorare a volume 

## <a name="add-a-volume"></a>Aggiungere un volume

Il [volume è stato creato](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) durante la distribuzione del dispositivo StorSimple serie 8000. L’aggiunta di un volume è una procedura simile.

#### <a name="to-add-a-volume"></a>Per aggiungere un volume

1. Nell'elenco tabulare dei dispositivi del pannello **Dispositivi** selezionare il dispositivo. Fare clic su **+ Aggiungi volume**.

    ![Aggiungere un nuovo volume](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. Nel pannello **Aggiungi un volume**:
   
    1. Nel campo **Seleziona dispositivo** viene automaticamente inserito il dispositivo corrente.

    2. Nell'elenco a discesa selezionare il contenitore del volume in cui è necessario aggiungere un volume.

    3.  Digitare un **Nome** per il volume. Non è possibile rinominare un volume dopo che il volume è stato creato.

    4. Nell'elenco a discesa selezionare il **tipo** per il volume. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni più elevate, selezionare un volume **aggiunto in locale** . Per tutti gli altri dati, selezionare un volume **a livelli** . Se si usa questo volume per dati di archivio, selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**.
      
       Per un volume a livelli viene effettuato il thin provisioning e la creazione può essere rapida. Selezionando **Usare questo volume per i dati di archivio a cui si accede non di frequente** per un volume a livelli interessato da modifiche dei dati di archivio, le dimensioni del blocco di deduplicazione per il volume verranno portate a 512 KB. Se questo campo non è selezionato, il volume a livelli corrispondente utilizzerà dimensioni del blocco pari a 64 KB. Una dimensione maggiore del blocco di deduplicazione consente al dispositivo di accelerare il trasferimento dei dati di archivio di grandi dimensioni nel cloud.
       
       Per un volume aggiunto in locale viene eseguito il thick provisioning, per garantire che i dati primari rimangano a livello locale per il dispositivo e non a livello cloud.  Se si crea un volume aggiunto in locale, il dispositivo cercherà lo spazio disponibile nei livelli locali per il provisioning del volume delle dimensioni richieste. L'operazione di creazione di un volume aggiunto in locale potrebbe comportare la distribuzione dei dati esistenti dal dispositivo al cloud e il tempo impiegato per creare il volume potrebbe essere lungo. Il tempo totale dipende dalle dimensioni del volume di cui è stato eseguito il provisioning, dalla larghezza di banda di rete disponibile e dai dati sul dispositivo.

    5. Specificare la **Capacità fornita** per il volume. Prendere nota della capacità disponibile in base al tipo di volume selezionato. Le dimensioni del volume specificato non devono superare lo spazio disponibile.
      
       È possibile effettuare il provisioning di volumi aggiunti in locale fino a 8,5 TB oppure di volumi a livelli fino a 200 TB nel dispositivo 8100. Nel dispositivo 8600 più grande è possibile effettuare il provisioning di volumi aggiunti in locale fino a 22,5 TB o di volumi a livelli fino a 500 TB. Poiché è necessario spazio locale sul dispositivo per ospitare il working set di volumi a livelli, la creazione di volumi aggiunti in locale influirà sullo spazio disponibile per il provisioning di volumi a livelli. Creando un volume aggiunto in locale, viene quindi ridotto lo spazio disponibile per la creazione di volumi a livelli. Analogamente, creando un volume a livelli verrà ridotto lo spazio disponibile per la creazione di volumi aggiunti in locale.
      
       Se nel dispositivo 8100 si effettua il provisioning di un volume aggiunto in locale di 8,5 TB, ovvero le dimensioni massime consentite, si esaurisce tutto lo spazio locale disponibile nel dispositivo. Non è possibile creare volumi a livelli da quel punto in poi, perché non è disponibile spazio locale sul dispositivo per ospitare il working set del volume a livelli. Anche i volumi a livelli esistenti influiscono sullo spazio disponibile. Ad esempio, se nel dispositivo 8100 sono già presenti volumi a livelli di circa 106 TB, saranno disponibili solo 4 TB di spazio per i volumi aggiunti in locale.

    6. Nel campo **Host connessi** fare clic sulla freccia. Nel pannello **Host connessi** scegliere un record di controllo di accesso esistente o aggiungerne uno nuovo. Se si sceglie un nuovo record, fornire un **nome** del record e l'**iSCSI Qualified Name** (IQN) dell'host Windows. Se non si dispone del nome qualificato iSCSI, andare a [Ottenere il nome qualificato iSCSI di un host di Windows Server](#get-the-iqn-of-a-windows-server-host). Fare clic su **Crea**. Verrà creato un volume con le impostazioni specificate.

        ![Click Create](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Il volume è pronto per l'utilizzo.

> [!NOTE]
> Se si crea un volume aggiunto in locale e quindi se ne crea un altro subito dopo, i processi di creazione dei volumi vengono eseguiti in sequenza. Il primo processo di creazione del volume deve terminare prima che possa iniziare quello successivo.

## <a name="modify-a-volume"></a>Modificare un volume

Modificare un volume quando occorre espanderlo o modificare gli host che vi accedono.

> [!IMPORTANT]
> * Se si modifica la dimensione del volume nel dispositivo, è necessario modificare anche le esigenze di dimensioni del volume sull’host.
> * Le procedure host-side descritte di seguito sono per Windows Server 2012 (2012R2). Procedure per Linux o altri sistemi operativi host saranno diverse. Quando si modifica il volume di un host che esegue un altro sistema operativo, fare riferimento alle istruzioni del sistema operativo dell’host.

#### <a name="to-modify-a-volume"></a>Per modificare un volume

1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Dall'elenco tabulare dei dispositivi, selezionare il dispositivo con il volume che si desidera modificare. Fare clic su **Impostazioni > Volumi**.

    ![Passare al pannello Volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Dall'elenco tabulare dei volumi, selezionare il volume e fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida. Selezionare **Take offline** (Mettere offline) per portare offline il volume che verrà modificato.

    ![Selezionare e portare offline i volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Nel pannello **Take offline** (Mettere offline), esaminare l'impatto del portare offline il volume e selezionare la casella di controllo corrispondente. Verificare innanzitutto che il volume corrispondente nell'host sia offline. Per informazioni su come portare un volume offline nel server host connesso a StorSimple, fare riferimento alle istruzioni specifiche del sistema operativo. Fare clic su **Porta offline**.

    ![Esaminare l'impatto del portare un volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Una volta portato il volume offline (come illustrato nello stato del volume), selezionare il volume e fare clic con il pulsante destro del mouse su di esso per richiamare il menu di scelta rapida. Selezionare **Modifica volume**.

    ![Selezionare Modifica volume](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Nel pannello **Modifica volume** è possibile apportare le modifiche seguenti:
   
   1. Il **Nome** del volume non può essere modificato.
   2. Convertire il **Tipo** da aggiunto in locale a quello a livelli o viceversa. Per altre informazioni, vedere [Change the volume type](#change-the-volume-type) (Modificare il tipo di volume).
   3. Aumentare la **Capacità fornita**. La **Capacità fornita** può essere solo aumentata. Non è possibile ridurre un volume dopo averlo creato.
   4. In **Host connessi** è possibile modificare i record di controllo di accesso. Per modificare un record, il volume deve essere offline.

       ![Esaminare l'impatto del portare un volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Fare clic su **Salva** per salvare le modifiche. Alla richiesta di conferma fare clic su **Sì**. Il portale di Azure mostrerà un messaggio relativo all'aggiornamento del volume. Quando il volume è stato aggiornato verrà mostrato un messaggio di conferma.

    ![Esaminare l'impatto del portare un volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Se si sta espandendo un volume, completare i passaggi seguenti nel computer host Windows:
   
   1. Accedere a **Gestione Computer** ->**Gestione disco**.
   2. Fare clic con il pulsante destro del mouse su **Gestione disco** e selezionare **Rescan Disks** (Ripeti analisi dischi).
   3. Nell'elenco dei dischi, selezionare il volume che è stato aggiornato, fare clic con il pulsante destro del mouse e quindi selezionare **Estendi Volume**. Verrà avviata la procedura guidata Estendi volume. Fare clic su **Avanti**.
   4. Completare la procedura guidata accettando i valori predefiniti. Dopo aver completato la procedura guidata, il volume dovrebbe mostrare l'aumento delle dimensioni.
      
      > [!NOTE]
      > Se si espande un volume aggiunto in locale e quindi se ne espande un altro subito dopo, i processi di espansione dei volumi vengono eseguiti in sequenza. Il primo processo di espansione del volume deve terminare prima che possa iniziare quello successivo.
      

## <a name="change-the-volume-type"></a>Modificare il tipo di volume

È possibile passare da un tipo di volume a livelli a uno aggiunto in locale o viceversa. Questa conversione non deve tuttavia essere eseguita con frequenza.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Considerazioni sulla conversione di un volume da livelli a locale

Di seguito sono elencati alcuni motivi per convertire un volume a livelli in uno aggiunto in locale:

* Garanzie locali relative alla disponibilità e alle prestazioni dei dati
* Eliminazione di latenze cloud e di problemi di connettività cloud

Si tratta in genere di piccoli volumi esistenti a cui è necessario accedere spesso. Quando un volume aggiunto in locale viene creato, ne viene effettuato il provisioning completo. 

Se si sta convertendo un volume a livelli in un volume aggiunto in locale, StorSimple verifica che lo spazio sul dispositivo sia sufficiente prima di avviare la conversione. Se lo spazio non è sufficiente, verrà visualizzato un errore e l'operazione verrà annullata. 

> [!NOTE]
> Prima di iniziare una conversione da un volume a livelli a uno aggiunto in locale, considerare i requisiti di spazio degli altri carichi di lavoro. 

La conversione da un volume a livelli a un volume aggiunto in locale può influire negativamente sulle prestazioni dei dispositivi. Inoltre, i seguenti fattori possono aumentare il tempo necessario per la conversione:

* Larghezza di banda insufficiente.
* Nessun backup corrente disponibile.

Per ridurre al minimo gli effetti di questi fattori:

* Rivedere i criteri di limitazione della larghezza di banda e verificare che sia disponibile una larghezza di banda dedicata di 40 Mbps.
* Pianificare la conversione in orari di scarso traffico.
* Acquisire uno snapshot cloud prima di iniziare la conversione.

Se si desidera convertire più volumi che supportano diversi carichi di lavoro, è necessario specificare le priorità della conversione in modo che i volumi a priorità più elevata vengano convertiti per primi. Ad esempio, è necessario convertire i volumi che ospitano macchine virtuali o con carichi di lavoro SQL prima dei volumi con carichi di lavoro di condivisione file.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Considerazioni sulla conversione di un volume da livelli a locale

È possibile, ad esempio, passare da un volume aggiunto in locale a un volume a livelli se è necessario spazio aggiuntivo per il provisioning di altri volumi. Quando si converte il volume aggiunto in locale in uno a livelli, la capacità disponibile nel dispositivo aumenta della dimensione della capacità rilasciata. Se problemi di connettività impediscono la conversione di un volume dal tipo locale al tipo a livelli, il volume locale presenterà le proprietà di un volume a livelli fino al completamento della conversione. Infatti è possibile che alcuni dati siano stati distribuiti nel cloud. Questi dati trasferiti continueranno a occupare lo spazio locale nel dispositivo, che non può essere liberato fino al riavvio e al completamento dell'operazione.

> [!NOTE]
> La conversione di un volume può richiedere tempo e non è possibile annullarla una volta avviata. Il volume rimane online durante la conversione ed è possibile eseguire i backup, ma non è possibile espandere o ripristinare il volume mentre la conversione è in corso.


#### <a name="to-change-the-volume-type"></a>Per modificare il tipo di volume

1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Dall'elenco tabulare dei dispositivi, selezionare il dispositivo con il volume che si desidera modificare. Fare clic su **Impostazioni > Volumi**.

    ![Passare al pannello Volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Dall'elenco tabulare dei volumi, selezionare il volume e fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida. Selezionare **Modifica**.

    ![Seleziona Elimina dal menu di scelta rapida](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Nel pannello **Modifica volume**, modificare il tipo di volume selezionandone uno nuovo nell'elenco a discesa **Tipo**.
   
   * Se si imposta il tipo su **Volume aggiunto**, StorSimple verificherà se la capacità è sufficiente.
   * Se si intende impostare il tipo su **A livelli** e usare questo volume per i dati di archivio, selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**.
   * Se si sta configurando un volume aggiunto in locale in volume a livelli o _viceversa_, verrà visualizzato il messaggio seguente.
   
    ![Messaggio relativo alla modifica del tipo di volume](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. È consigliabile fare clic su **Salva** per salvare le modifiche. Quando viene richiesta la conferma, fare clic su **Sì** per avviare la procedura di conversione. 

    ![Salvare e confermare](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Il portale di Azure consente di visualizzare una notifica per la creazione del processo che aggiorna il volume. Fare clic sulla notifica per monitorare lo stato del processo di conversione del volume.

    ![Processo di conversione del volume](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Portare un volume offline

Potrebbe essere necessario portare un volume offline per modificarlo o eliminarlo. Quando un volume è offline, non è disponibile per l'accesso in lettura/scrittura. Portare offline il volume prima nell'host e nel dispositivo.

#### <a name="to-take-a-volume-offline"></a>Per portare un volume offline

1. Assicurarsi che il volume in questione non sia in uso prima di portarlo offline.
2. Portare offline il volume prima nell'host. Ciò consente di eliminare qualsiasi potenziale rischio di danneggiamento dei dati nel volume. Per i passaggi specifici, vedere le istruzioni per il sistema operativo dell’host.
3. Quando l'host è offline, portare il volume nel dispositivo offline eseguendo i passaggi seguenti:
   
    1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Dall'elenco tabulare dei dispositivi, selezionare il dispositivo con il volume che si desidera modificare. Fare clic su **Impostazioni > Volumi**.

        ![Passare al pannello Volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Dall'elenco tabulare dei volumi, selezionare il volume e fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida. Selezionare **Take offline** (Mettere offline) per portare offline il volume che verrà modificato.

        ![Selezionare e portare offline i volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Nel pannello **Take offline** (Mettere offline), esaminare l'impatto del portare offline il volume e selezionare la casella di controllo corrispondente. Fare clic su **Porta offline**. 

    ![Esaminare l'impatto del portare un volume offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Si riceverà una notifica quando il volume è offline. Anche lo stato del volume si aggiorna su Offline.
      
4. Dopo che un volume è stato portato offline, se si seleziona il volume e si fa clic su di esso con il pulsante destro del mouse, l'opzione **Porta online** diventa disponibile nel menu di scelta rapida.

> [!NOTE]
> Il comando **Porta offline** invia al dispositivo una richiesta di portare il volume offline. Se gli host stanno ancora utilizzando il volume, le connessioni saranno interrotte ma il volume sarà portato offline.

## <a name="delete-a-volume"></a>Eliminare un volume

> [!IMPORTANT]
> È possibile eliminare un volume solo se è offline.

Completare la procedura seguente per eliminare un volume.

#### <a name="to-delete-a-volume"></a>Per eliminare un volume

1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Dall'elenco tabulare dei dispositivi, selezionare il dispositivo con il volume che si desidera modificare. Fare clic su **Impostazioni > Volumi**.

    ![Passare al pannello Volumi](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Verificare lo stato del volume che si desidera eliminare. Se il volume da eliminare non è offline, portarlo prima offline. Seguire la procedura illustrata in [Portare un volume offline](#take-a-volume-offline).
4. Una volta portato il volume offline, selezionarlo, fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida e quindi selezionare **Elimina**.

    ![Seleziona Elimina dal menu di scelta rapida](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Nel pannello **Elimina**, esaminare e selezionare la casella di controllo in relazione all'impatto dell'eliminazione di un volume. Quando si elimina un volume, tutti i dati che si trovano nel volume andranno persi. 

    ![Salvare e confermare le modifiche](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Una volta eliminato il volume, l'elenco tabulare dei volumi viene aggiornato per indicare l'eliminazione.

    ![Elenco aggiornato dei volumi](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Se si elimina un volume aggiunto in locale, lo spazio disponibile per i nuovi volumi potrebbe non essere immediatamente aggiornato. Il servizio Gestione dispositivi StorSimple aggiorna periodicamente lo spazio locale disponibile. È consigliabile attendere alcuni minuti prima di creare il nuovo volume.
   >
   > Se si elimina un volume aggiunto in locale e quindi se ne elimina un altro subito dopo, i processi di eliminazione dei volumi vengono eseguiti in sequenza. Il primo processo di eliminazione del volume deve terminare prima che possa iniziare quello successivo.

## <a name="monitor-a-volume"></a>Monitorare a volume

Il monitoraggio del volume consente di raccogliere le statistiche correlate all’I/O per un volume. Il monitoraggio è abilitato per impostazione predefinita per i primi 32 volumi creati dall'utente. Il monitoraggio di ulteriori volumi è disabilitato per impostazione predefinita. 

> [!NOTE]
> Il monitoraggio di volumi clonati è disabilitato per impostazione predefinita.


Per abilitare o disabilitare il monitoraggio per un volume, eseguire i passaggi seguenti.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Per abilitare o disabilitare il monitoraggio del volume

1. Passare al servizio Gestione dispositivi StorSimple, quindi fare clic su **Dispositivi**. Dall'elenco tabulare dei dispositivi, selezionare il dispositivo con il volume che si desidera modificare. Fare clic su **Impostazioni > Volumi**.
2. Dall'elenco tabulare dei volumi, selezionare il volume e fare clic con il pulsante destro del mouse per richiamare il menu di scelta rapida. Selezionare **Modifica**.
3. Nel pannello **Modifica volume**, per **Monitoraggio** selezionare **Abilita** o **Disabilita** per abilitare o disabilitare il monitoraggio.

    ![Disabilitare il monitoraggio](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Alla richiesta di conferma fare clic su **Salva** e quindi su **Sì**. Il portale di Azure consente di visualizzare una notifica per l'aggiornamento del volume e quindi un messaggio di conferma, dopo che il volume è stato aggiornato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [clonare un volume StorSimple](storsimple-8000-clone-volume-u2.md)
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

