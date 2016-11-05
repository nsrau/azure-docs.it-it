---
title: Gestire i volumi StorSimple (aggiornamento 2) | Microsoft Docs
description: Viene illustrato come aggiungere, modificare, monitorare ed eliminare i volumi StorSimple e come portarli offline, se necessario.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/21/2016
ms.author: alkohli

---
# Per gestire il volume è possibile usare il servizio StorSimple Manager (aggiornamento 2)
[!INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## Overview
Questa esercitazione illustra come usare il servizio StorSimple Manager per creare e gestire i volumi nel dispositivo StorSimple e nel dispositivo virtuale StorSimple con l'aggiornamento 2 installato.

Il servizio StorSimple Manager è un'estensione del portale di Azure classico che permette di gestire la soluzione StorSimple da un'unica interfaccia Web. Oltre che per la gestione dei volumi, è possibile usare il servizio StorSimple Manager per creare e gestire servizi StorSimple, visualizzare e gestire dispositivi, visualizzare avvisi, visualizzare e gestire i criteri di backup e il catalogo di backup.

## Tipi di volume
I volumi di StorSimple possono essere:

* **Volumi aggiunti in locale**: i dati in questi volumi rimangono sempre nel dispositivo StorSimple locale.
* **Volumi a livelli**: i dati in questi volumi essere distribuiti nel cloud.

Un volume di archivio è un tipo di volume a livelli. La maggiore dimensione del blocco di deduplicazione usata per i volumi di archivio consente al dispositivo di trasferire nel cloud segmenti di dati più grandi.

Se necessario, è possibile passare da un tipo di volume locale a uno a livelli o viceversa. Per altre informazioni, vedere [Modificare il tipo di volume](#change-the-volume-type).

### Volumi aggiunti in locale
I volumi aggiunti in locale sono volumi con provisioning completo che non dispongono i dati su livelli nel cloud, assicurando così garanzie locali per i dati primari, indipendentemente dalla connettività cloud. I dati nei volumi aggiunti in locale non vengono deduplicati e compressi, mentre gli snapshot dei volumi aggiunti in locale vengono deduplicati.

Poiché viene effettuato il provisioning completo dei volumi aggiunti in locale, è necessario avere spazio sufficiente nel dispositivo quando li si crea. È possibile effettuare il provisioning dei volumi aggiunti in locale fino a una dimensione massima di 8 TB nel dispositivo StorSimple 8100 e di 20 TB nel dispositivo 8600. StorSimple riserva lo spazio locale restante nel dispositivo a snapshot, metadati ed elaborazione dati. È possibile aumentare la dimensione di un volume aggiunto in locale fino alla massima quantità di spazio disponibile, ma non è possibile ridurre la dimensione di un volume una volta creato.

Quando si crea un volume aggiunto in locale, viene ridotto lo spazio disponibile per la creazione di volumi a livelli. È anche vero il contrario: se esistono già volumi a livelli, lo spazio disponibile per la creazione di volumi aggiunti in locale sarà inferiore ai limiti massimi definiti prima. Per altre informazioni sui volumi locali, vedere le [domande frequenti sui volumi aggiunti in locale](storsimple-local-volume-faq.md).

### Volumi a livelli
I volumi a livelli sono volumi con thin provisioning in cui i dati a cui si accede più spesso rimangono in locale nel dispositivo, mentre quelli usati meno spesso vengono automaticamente disposti su livelli nel cloud. Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo spazio di archiviazione sufficiente, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire lo spazio cloud in base alle variazioni nella domanda.

Se si usa il volume a livelli per i dati di archiviazione, selezionando la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente** si porta a 512 KB la dimensione del blocco di deduplicazione per il volume. Se non si seleziona questa opzione, il volume a livelli corrispondente utilizzerà una dimensione del blocco di 64 KB. Una dimensione maggiore del blocco di deduplicazione consente al dispositivo di accelerare il trasferimento dei dati di archivio di grandi dimensioni nel cloud.

> [!NOTE]
> I volumi di archivio creati con una versione precedente all'aggiornamento 2 di StorSimple verranno importati a livelli con la casella di controllo dell'archivio selezionata.
> 
> 

### Capacità con provisioning
Fare riferimento alla tabella seguente per conoscere la capacità massima di cui viene effettuato il provisioning per ogni tipo di dispositivo e di volume. Si noti che i volumi aggiunti in locale non sono disponibili in un dispositivo virtuale.

|  | Dimensione massima volume a livelli | Dimensione massima volume aggiunto in locale |
| --- | --- | --- |
| **Dispositivi fisici** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Dispositivi virtuali** | | |
| 8010 |30 TB |N/D |
| 8020 |64 TB |N/D |

## La pagina Volumi
La pagina **Volumi** consente di gestire i volumi di archiviazione forniti nel dispositivo StorSimple di Microsoft Azure per gli iniziatori (server). La pagina riporta l'elenco dei volumi nel dispositivo StorSimple.

 ![Pagina dei volumi](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volume è costituito da una serie di attributi:

* **Nome volume**: un nome descrittivo che deve essere univoco e consente di identificare il volume. Questo nome viene utilizzato anche nei rapporti di monitoraggio quando di applica un filtro su un volume specifico.
* **Stato**: online oppure offline. Se un volume è offiline, non è visibile agli iniziatori (server) a cui è consentito l'accesso per utilizzare il volume.
* **Capacità**: specifica la quantità totale di dati che può essere archiviata dall'iniziatore (server). Dei volumi aggiunti in locale viene effettuato il provisioning completo. Tali volumi sono nel dispositivo StorSimple. Dei volumi a livelli Viene effettuato il thin provisioning e i dati vengono deduplicati. Con i volumi con thin provisioning, il dispositivo non prealloca la capacità di archiviazione fisica internamente o nel cloud in base alle capacità del volume configurato. La capacità del volume viene allocata e utilizzata su richiesta.
* **Tipo**: indica se il volume è **A livelli** (impostazione predefinita) o **Volume aggiunto**.
* **Backup**: indica se esistono criteri di backup predefiniti per il volume.
* **Accesso**: specifica gli iniziatori (server) a cui è consentito l'accesso a questo volume. Agli iniziatori che non sono membri del record di controllo di accesso (ACR) associato al volume non verrà visualizzato il volume.
* **Monitoraggio**: specifica se un volume viene monitorato. Un volume avrà il monitoraggio abilitato per impostazione predefinita quando viene creato. Il monitoraggio sarà però disabilitato per un clone del volume. Per abilitare il monitoraggio per un volume, seguire le istruzioni riportate in [Monitorare un volume](#monitor-a-volume).

Usare le istruzioni di questa esercitazione per eseguire le attività seguenti:

* Aggiungere un volume
* Modificare un volume
* Modificare il tipo di volume
* Eliminare un volume
* Portare un volume offline
* Monitorare a volume

## Aggiungere un volume
Il [volume è stato creato](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante la distribuzione della soluzione StorSimple. L’aggiunta di un volume è una procedura simile.

#### Per aggiungere un volume
1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.
2. Selezionare un contenitore di volumi nell'elenco e fare doppio clic su di esso per accedere ai volumi associati al contenitore.
3. Fare clic su **Add** nella parte inferiore della pagina. Verrà avviata la procedura guidata Aggiungi volume.
   
     ![Aggiungi procedura guidata del volume Impostazioni di base](./media/storsimple-manage-volumes-u2/TieredVolEx.png)
4. Nella procedura guidata Aggiungi un volume, in **Impostazioni di base**, procedere come segue:
   
   1. Digitare un **Nome** per il volume.
   2. Selezionare un **Tipo di utilizzo** nell'elenco a discesa. Per i carichi di lavoro per cui è necessario che i dati siano sempre disponibili nel dispositivo, selezionare **Volume aggiunto**. Per tutti gli altri tipi di dati, selezionare **A livelli**. **A livelli** è l'impostazione predefinita.
   3. Se nel passaggio 2 è stato selezionato **A livelli**, è possibile selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente** per configurare un volume di archivio.
   4. Immettere la **Capacità provisioning** per il volume in GB o TB. Vedere [Capacità con provisioning](#provisioned-capacity) per conoscere le dimensioni massime per ogni tipo di dispositivo e di volume. Esaminare la **Capacità disponibile** per determinare quanto spazio di archiviazione sia attualmente disponibile nel dispositivo.
5. Fare clic sull'icona a forma di freccia ![Icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se si sta configurando un volume aggiunto in locale, verrà visualizzato il messaggio seguente.
   
    ![Messaggio relativo alla modifica del tipo di volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
6. Fare di nuovo clic sull'icona a forma di freccia ![Icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) per passare alla pagina **Impostazioni aggiuntive**.
   
    ![Aggiungi procedura guidata del volume Impostazioni aggiuntive](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>
7. Nella finestra di dialogo **Impostazioni aggiuntive**, aggiungere un nuovo record di controllo di accesso (ACR):
   
   1. Selezionare un record di controllo di accesso (ACR) dall'elenco a discesa. In alternativa, è possibile aggiungere un nuovo ACR. Gli ACR determinano quali host possono accedere ai volumi creando una corrispondenza tra il nome qualificato ISCSI dell'host e quello elencato nel record. Se non si specifica un ACR, verrà visualizzato il messaggio seguente.
      
        ![Specify ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)
   2. È consigliabile selezionare la casella di controllo **Abilita un criterio di backup predefinito per questo volume**.
   3. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) per creare il volume con le impostazioni specificate.

Il volume è pronto per l'utilizzo.

> [!NOTE]
> Se si crea un volume aggiunto in locale e quindi se ne crea un altro subito dopo, i processi di creazione dei volumi vengono eseguiti in sequenza. Il primo processo di creazione del volume deve terminare prima che possa iniziare quello successivo.
> 
> 

## Modificare un volume
Modificare un volume quando occorre espanderlo o modificare gli host che vi accedono.

> [!IMPORTANT]
> * Se si modifica la dimensione del volume nel dispositivo, è necessario modificare anche le esigenze di dimensioni del volume sull’host.
> * Le procedure host-side descritte di seguito sono per Windows Server 2012 (2012R2). Procedure per Linux o altri sistemi operativi host saranno diverse. Quando si modifica il volume di un host che esegue un altro sistema operativo, fare riferimento alle istruzioni del sistema operativo dell’host.
> 
> 

#### Per modificare un volume
1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.
2. Selezionare un contenitore di volumi nell'elenco e fare doppio clic su di esso per visualizzare i volumi associati al contenitore.
3. Selezionare un volume e nella parte inferiore della pagina fare clic su **Modifica**. Verrà avviata la procedura guidata per la modifica del volume.
4. Nella procedura guidata Aggiungi volume, in **Impostazioni di base**, procedere come segue:
   
   * Modificare il **Nome**.
   * Convertire il **Tipo di utilizzo** da aggiunto in locale a quello a livelli o viceversa. Per altre informazioni, vedere [Modificare il tipo di volume](#change-the-volume-type).
   * Aumentare la **Capacità fornita**. La **Capacità fornita** può essere solo aumentata. Non è possibile ridurre un volume dopo averlo creato.
5. In **Impostazioni aggiuntive** è possibile modificare l'ACR, purché il volume sia offline. Se il volume è online, sarà necessario portarlo prima offline. Fare riferimento ai passaggi riportati in [Portare un volume offline](#take-a-volume-offline) prima di modificare gli ACR.
   
   > [!NOTE]
   > Non è possibile modificare l’opzione **Abilita backup predefinito** per il volume.
   > 
   > 
6. Salvare le modifiche scegliendo l'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Il portale di Azure classico mostrerà un messaggio di volume dell’aggiornamento. Quando il volume è stato aggiornato verrà mostrato un messaggio di conferma.
7. Se si sta espandendo un volume, completare i passaggi seguenti nel computer host Windows:
   
   1. Andare a **Gestione Computer** ->**Gestione disco**.
   2. Fare doppio clic con il pulsante destro del mouse su **Gestione disco** e selezionare **Ripeti analisi dischi**.
   3. Nell'elenco dei dischi, selezionare il volume che è stato aggiornato, fare clic con il pulsante destro del mouse e quindi selezionare **Estendi Volume**. Verrà avviata la procedura guidata Estendi volume. Fare clic su **Avanti**.
   4. Completare la procedura guidata accettando i valori predefiniti. Dopo aver completato la procedura guidata, il volume dovrebbe mostrare l'aumento delle dimensioni.
      
      > [!NOTE]
      > Se si espande un volume aggiunto in locale e quindi se ne espande un altro subito dopo, i processi di espansione dei volumi vengono eseguiti in sequenza. Il primo processo di espansione del volume deve terminare prima che possa iniziare quello successivo.
      > 
      > 

![Video disponibile](./media/storsimple-manage-volumes-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come espandere un volume, fare clic [qui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## Modificare il tipo di volume
È possibile passare da un tipo di volume a livelli a uno aggiunto in locale o viceversa. Questa conversione non deve tuttavia essere eseguita con frequenza. Di seguito sono elencati alcuni motivi per convertire un volume a livelli in uno aggiunto in locale:

* Garanzie locali relative alla disponibilità e alle prestazioni dei dati
* Eliminazione di latenze cloud e di problemi di connettività cloud

Si tratta in genere di piccoli volumi esistenti a cui è necessario accedere spesso. Quando un volume aggiunto in locale viene creato, ne viene effettuato il provisioning completo. Se si sta convertendo un volume a livelli in un volume aggiunto in locale, StorSimple verifica che lo spazio sul dispositivo sia sufficiente prima di avviare la conversione. Se lo spazio non è sufficiente, verrà visualizzato un errore e l'operazione verrà annullata.

> [!NOTE]
> Prima di iniziare una conversione da un volume a livelli a uno aggiunto in locale, considerare i requisiti di spazio degli altri carichi di lavoro.
> 
> 

È possibile, ad esempio, passare da un volume aggiunto in locale a un volume a livelli se è necessario spazio aggiuntivo per il provisioning di altri volumi. Quando si converte il volume aggiunto in locale in uno a livelli, la capacità disponibile nel dispositivo aumenta della dimensione della capacità rilasciata. Se problemi di connettività impediscono la conversione di un volume dal tipo locale al tipo a livelli, il volume locale presenterà le proprietà di un volume a livelli fino al completamento della conversione. Infatti è possibile che alcuni dati siano stati distribuiti nel cloud. Questi dati trasferiti continueranno a occupare lo spazio locale nel dispositivo, che non può essere liberato fino al riavvio e al completamento dell'operazione.

> [!NOTE]
> La conversione di un volume può richiedere tempo e non è possibile annullarla una volta avviata. Il volume rimane online durante la conversione ed è possibile eseguire i backup, ma non è possibile espandere o ripristinare il volume mentre la conversione è in corso.
> 
> 

La conversione da un volume a livelli a un volume aggiunto in locale può influire negativamente sulle prestazioni dei dispositivi. Inoltre, i seguenti fattori possono aumentare il tempo necessario per la conversione:

* Larghezza di banda insufficiente.
* Nessun backup corrente disponibile.

Per ridurre al minimo gli effetti di questi fattori:

* Rivedere i criteri di limitazione della larghezza di banda e verificare che sia disponibile una larghezza di banda dedicata di 40 Mbps.
* Pianificare la conversione in orari di scarso traffico.
* Acquisire uno snapshot cloud prima di iniziare la conversione.

Se si desidera convertire più volumi che supportano diversi carichi di lavoro, è necessario specificare le priorità della conversione in modo che i volumi a priorità più elevata vengano convertiti per primi. Ad esempio, è necessario convertire i volumi che ospitano macchine virtuali o con carichi di lavoro SQL prima dei volumi con carichi di lavoro di condivisione file.

#### Per modificare il tipo di volume
1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.
2. Selezionare un contenitore di volumi nell'elenco e fare doppio clic su di esso per visualizzare i volumi associati al contenitore.
3. Selezionare un volume e nella parte inferiore della pagina fare clic su **Modifica**. Verrà avviata la procedura guidata per la modifica del volume.
4. Nella pagina **Impostazioni di base** modificare il tipo di utilizzo selezionando il nuovo tipo nell'elenco a discesa **Tipo di utilizzo**.
   
   * Se si imposta il tipo su **Volume aggiunto**, StorSimple verificherà se la capacità è sufficiente.
   * Se si imposta il tipo su **A livelli** e questo volume verrà usato per dati di archivio, selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**.
     
       ![Casella di controllo per l'archivio](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)
5. Fare clic sull'icona freccia ![Icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) per passare alla pagina **Impostazioni aggiuntive**. Se si sta configurando un volume aggiunto in locale, verrà visualizzato il messaggio seguente.
   
    ![Messaggio relativo alla modifica del tipo di volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)
6. Fare di nuovo clic sull'icona a freccia ![Icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) per continuare.
7. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) per avviare il processo di conversione. Il portale di Azure mostrerà un messaggio relativo all'aggiornamento del volume. Quando il volume è stato aggiornato verrà mostrato un messaggio di conferma.

## Portare un volume offline
Potrebbe essere necessario portare un volume offline per modificarlo o eliminarlo. Quando un volume è offline, non è disponibile per l'accesso in lettura/scrittura. È necessario portare offline il volume nell'host e sul dispositivo.

#### Per portare un volume offline
1. Assicurarsi che il volume in questione non sia in uso prima di portarlo offline.
2. Portare offline il volume prima nell'host. Ciò consente di eliminare qualsiasi potenziale rischio di danneggiamento dei dati nel volume. Per i passaggi specifici, vedere le istruzioni per il sistema operativo dell’host.
3. Quando l'host è offline, portare il volume nel dispositivo offline eseguendo i passaggi seguenti:
   
   1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**. Nella scheda **Contenitori di volumi** vengono elencati in formato tabulare tutti i contenitori di volumi associati al dispositivo.
   2. Selezionare un contenitore di volumi e fare clic per visualizzare l'elenco di tutti i volumi all'interno del contenitore.
   3. Selezionare un volume e fare clic su **Offline**.
   4. Alla richiesta di conferma fare clic su **Sì**. Il volume sarà offline.
      
      Quando un volume è offline, l’opzione **Porta online** diventa disponibile.

> [!NOTE]
> Il comando **Porta offline** invia al dispositivo una richiesta di portare il volume offline. Se gli host stanno ancora utilizzando il volume, le connessioni saranno interrotte ma il volume sarà portato offline.
> 
> 

## Eliminare un volume
> [!IMPORTANT]
> È possibile eliminare un volume solo se è offline.
> 
> 

Completare la procedura seguente per eliminare un volume.

#### Per eliminare un volume
1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.
2. Selezionare il contenitore di volumi contenente il volume che si desidera eliminare. Fare clic sul contenitore di volumi per accedere alla pagina **Volumi**.
3. Tutti i volumi associati a questo contenitore vengono visualizzati in formato tabulare. Verificare lo stato del volume che si desidera eliminare. Se il volume che si desidera eliminare non è offline, portarlo innanzitutto offline seguendo i passaggi indicati in [Portare un volume offline](#take-a-volume-offline).
4. Quando il volume è offline, fare clic su **Elimina** in fondo alla pagina.
5. Alla richiesta di conferma fare clic su **Sì**. Il volume verrà eliminato e nella pagina **Volumi** sarà visualizzato l’elenco aggiornato di volumi presenti nel contenitore.
   
   > [!NOTE]
   > Se si elimina un volume aggiunto in locale, lo spazio disponibile per i nuovi volumi potrebbe non essere immediatamente aggiornato. Il servizio StorSimple Manager aggiorna periodicamente lo spazio locale disponibile. È consigliabile attendere alcuni minuti prima di creare il nuovo volume.<br> Se si elimina un volume aggiunto in locale e quindi se ne elimina un altro subito dopo, i processi di eliminazione dei volumi vengono eseguiti in sequenza. Il primo processo di eliminazione del volume deve terminare prima che possa iniziare quello successivo.
   > 
   > 

## Monitorare a volume
Il monitoraggio del volume consente di raccogliere le statistiche correlate all’I/O per un volume. Il monitoraggio è abilitato per impostazione predefinita per i primi 32 volumi creati dall'utente. Il monitoraggio di ulteriori volumi è disabilitato per impostazione predefinita. Il monitoraggio di volumi clonati è disabilitato per impostazione predefinita.

Per abilitare o disabilitare il monitoraggio per un volume, eseguire i passaggi seguenti.

#### Per abilitare o disabilitare il monitoraggio del volume
1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.
2. Selezionare il contenitore del volume in cui risiede il volume, quindi fare clic sul contenitore del volume per accedere alla pagina **Volumi**.
3. Tutti i volumi associati a questo contenitore vengono elencati in formato tabulare. Fare clic e selezionare il volume o un clone del volume.
4. Nella parte inferiore della pagina fare clic su **Modifica**.
5. Nella procedura guidata Modifica volume in **Impostazioni di base**, selezionare **Abilita** o **Disabilita** dall’elenco a discesa **Monitoraggio**.

## Passaggi successivi
* Informazioni su come [clonare un volume StorSimple](storsimple-clone-volume.md)
* Informazioni su come [usare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0921_2016-->