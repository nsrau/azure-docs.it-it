<properties
   pageTitle="Gestire volumi StorSimple | Microsoft Azure"
   description="Viene illustrato come aggiungere, modificare, monitorare ed eliminare i volumi StorSimple e come portarli offline, se necessario."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="v-sharos" />

# Per gestire il volume, è possibile usare il servizio StorSimple Manager.

## Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager per creare e gestire i volumi nel dispositivo StorSimple e nel dispositivo virtuale StorSimple.

Il servizio StorSimple Manager è un’estensione del portale di Azure classico che permette di gestire la soluzione StorSimple da una singola interfaccia Web. Oltre che per la gestione dei volumi, è possibile usare il servizio StorSimple Manager per creare e gestire servizi StorSimple, visualizzare e gestire dispositivi, visualizzare avvisi, visualizzare e gestire i criteri di backup e il catalogo di backup.

> [AZURE.NOTE]StorSimple di Azure consente di creare solo volumi di thin provisioning. Non è possibile creare volumi con provisioning completo o parziale in un sistema StorSimple di Azure.
>
> Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo risorse di archiviazione sufficienti, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire le risorse di archiviazione nel cloud in base alle variazioni nella domanda.

## La pagina Volumi

La pagina **Volumi** consente di gestire i volumi di archiviazione forniti nel dispositivo StorSimple di Microsoft Azure per gli iniziatori (server). La pagina riporta l'elenco dei volumi nel dispositivo StorSimple.

 ![Pagina dei volumi](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Un volume è costituito da una serie di attributi:

- **Nome** : un nome descrittivo che deve essere univoco e consente di identificare il volume. Questo nome viene utilizzato anche nei rapporti di monitoraggio quando di applica un filtro su un volume specifico.

- **Stato**: online oppure offline. Se un volume è offiline, non è visibile agli iniziatori (server) a cui è consentito l'accesso per utilizzare il volume.

- **Capacità**: specifica le dimensioni del volume percepite dall'iniziatore (server). La capacità specifica la quantità totale di dati che può essere archiviata dall’iniziatore (server). Viene eseguito il thin provisioning del volumi e i dati vengono deduplicati. Ciò implica che il dispositivo non prealloca la capacità di archiviazione fisica internamente o nel cloud in base alle capacità del volume configurato. La capacità del volume viene allocata e utilizzata su richiesta.

- **Accesso**: specifica gli iniziatori (server) a cui è consentito l'accesso a questo volume. Agli iniziatori che non sono membri del record di controllo di accesso (ACR) associato al volume non verrà visualizzato il volume.

- **Monitoraggio**: specifica se un volume viene monitorato. Un volume avrà il monitoraggio abilitato per impostazione predefinita quando viene creato. Il monitoraggio sarà però disabilitato per un clone del volume. Per abilitare il monitoraggio per un volume, seguire le istruzioni riportare in Monitorare un volume.

Le attività più comuni associate a un volume sono:

- Aggiungere un volume 
- Modificare un volume 
- Eliminare un volume 
- Portare un volume offline 
- Monitorare a volume 

## Aggiungere un volume

Il [volume è stato creato](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) durante la distribuzione della soluzione StorSimple. L’aggiunta di un volume è una procedura simile.

### Per aggiungere un volume

1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.

2. Selezionare un contenitore di volumi e fare clic sulla freccia nella riga corrispondente per accedere ai volumi associati al contenitore.

3. Fare clic su **Add** nella parte inferiore della pagina. Verrà avviata la procedura guidata Aggiungi volume.

     ![Aggiungi procedura guidata del volume Impostazioni di base](./media/storsimple-manage-volumes/AddVolume1.png)

4. Nella procedura guidata Aggiungi un volume, in **Impostazioni di base**, procedere come segue:

  1. Digitare un **Nome** per il volume.
  2. Specificare la **Capacità fornita** per il volume in GB o TB. La capacità deve essere compresa tra 1 GB e 64 TB per un dispositivo fisico. La capacità massima che è possibile fornire per un volume su un dispositivo virtuale StorSimple è di 30 TB.
  3. Nell'elenco a discesa selezionare il **Tipo di utilizzo** per il volume. Se si usa questo volume per dati di archivio, selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**. Per tutti gli altri casi, selezionare semplicemente **Volume a livelli**. (I volumi a livelli erano in precedenza denominati volumi principali.)
  5. Fare clic sull'icona freccia ![Icona freccia](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)per passare alla pagina **Impostazioni aggiuntive**.

        ![Add Volume wizard Additional Settings](./media/storsimple-manage-volumes/AddVolume2.png)
   
5. Nella finestra di dialogo **Impostazioni aggiuntive**, aggiungere un nuovo record di controllo di accesso (ACR):
  
  1. Selezionare un record di controllo di accesso (ACR) dall'elenco a discesa. In alternativa, è possibile aggiungere un nuovo ACR. Gli ACR determinano quali host possono accedere ai volumi creando una corrispondenza tra il nome qualificato ISCSI dell'host e quello elencato nel record.
  2. È consigliabile abilitare un backup predefinito tramite la selezione della casella di controllo **Abilita un criterio di backup predefinito per questo volume**.
   3. Fare clic sull'icona del segno di spunta ![Icona del segno di spunta](./media/storsimple-manage-volumes/HCS_CheckIcon.png) per creare il volume con le impostazioni specificate.

Il volume è pronto per l'utilizzo.

## Modificare un volume

Modificare un volume quando occorre espanderlo o modificare gli host che vi accedono.

> [AZURE.IMPORTANT]
>
> - Se si modifica la dimensione del volume nel dispositivo, è necessario modificare anche le esigenze di dimensioni del volume sull’host. 
> - Le procedure host-side descritte di seguito sono per Windows Server 2012 (2012R2). Procedure per Linux o altri sistemi operativi host saranno diverse. Quando si modifica il volume di un host che esegue un altro sistema operativo, fare riferimento alle istruzioni del sistema operativo dell’host. 

### Per modificare un volume

1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**. Nella pagina vengono elencati in formato tabulare tutti i contenitori di volumi associati al dispositivo.

2. Selezionare un contenitore di volumi e fare clic per visualizzare l'elenco di tutti i volumi all'interno del contenitore.

3. Nella pagina **Volumi**, selezionare un volume e fare clic su **Modifica**.

4. Nella procedura guidata Aggiungi volume, in **Impostazioni di base**, procedere come segue:

  - Modificare il **Nome** e il **Tipo di applicazione**.
  - Aumentare la **Capacità fornita**. La **Capacità fornita** può essere solo aumentata. Non è possibile ridurre un volume dopo averlo creato.

    > [AZURE.NOTE]Non è possibile modificare un contenitore di volumi dopo che è stato assegnato a un volume.

5. In **Impostazioni aggiuntive**, procedere come segue:

  - Modificare gli ACR, purché il volume sia offline. Se il volume è online, sarà necessario portarlo prima offline. Fare riferimento ai passaggi riportati in [Portare un volume offline](#take-a-volume-offline) prima di modificare gli ACR.
  - Modificare l’elenco di ACR dopo aver portato il volume offiline.
 
    > [AZURE.NOTE]Non è possibile modificare l’opzione **Abilita backup predefinito per questo volume** per il volume.

6. Salvare le modifiche scegliendo l'icona del segno di spunta ![icona del segno di spunta](./media/storsimple-manage-volumes/HCS_CheckIcon.png). Il portale di Azure classico mostrerà un messaggio di volume dell’aggiornamento. Quando il volume è stato aggiornato verrà mostrato un messaggio di conferma.

7. Se si sta espandendo un volume, completare i passaggi seguenti nel computer host Windows:

   1. Andare a **Gestione Computer** ->**Gestione disco**.
   2. Fare doppio clic con il pulsante destro del mouse su **Gestione disco** e selezionare **Ripeti analisi dischi**.
   3. Nell'elenco dei dischi, selezionare il volume che è stato aggiornato, fare clic con il pulsante destro del mouse e quindi selezionare **Estendi Volume**. Verrà avviata la procedura guidata Estendi volume. Fare clic su **Avanti**.
   4. Completare la procedura guidata accettando i valori predefiniti. Dopo aver completato la procedura guidata, il volume dovrebbe mostrare l'aumento delle dimensioni.

![Video disponibile](./media/storsimple-manage-volumes/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come espandere un volume, fare clic [qui](http://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume).

## Portare un volume offline

Potrebbe essere necessario portare un volume offline per modificarlo o eliminarlo. Quando un volume è offline, non è disponibile per l'accesso in lettura/scrittura. È necessario portare offline il volume nell'host e sul dispositivo. Per portare un volume offline, eseguire i passaggi seguenti:

### Per portare un volume offline

1. Assicurarsi che il volume in questione non sia in uso prima di portarlo offline.

2. Portare offline il volume prima nell'host. Ciò consente di eliminare qualsiasi potenziale rischio di danneggiamento dei dati nel volume. Per i passaggi specifici, vedere le istruzioni per il sistema operativo dell’host.

3. Quando l'host è offline, portare il volume nel dispositivo offline eseguendo i passaggi seguenti:

  1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**. Nella scheda **Contenitori di volumi** vengono elencati in formato tabulare tutti i contenitori di volumi associati al dispositivo.
  2. Selezionare un contenitore di volumi e fare clic per visualizzare l'elenco di tutti i volumi all'interno del contenitore.
  3. Selezionare un volume e fare clic su **Offline**.
  4. Alla richiesta di conferma fare clic su **Sì**. Il volume sarà offline.

    Quando un volume è offline, l’opzione **Porta online** diventa disponibile.

> [AZURE.NOTE]Il comando **Porta offline** invia al dispositivo una richiesta di portare il volume offline. Se gli host stanno ancora utilizzando il volume, le connessioni saranno interrotte ma il volume sarà portato offline.

## Eliminare un volume

> [AZURE.IMPORTANT]È possibile eliminare un volume solo se è offline.

Completare la procedura seguente per eliminare un volume.

### Per eliminare un volume

1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.

2. Selezionare il contenitore di volumi contenente il volume che si desidera eliminare. Fare clic sul contenitore di volumi per accedere alla pagina **Volumi**.

3. Tutti i volumi associati a questo contenitore vengono visualizzati in formato tabulare. Verificare lo stato del volume che si desidera eliminare. Se il volume che si desidera eliminare non è offline, portarlo innanzitutto offline seguendo i passaggi indicati in [Portare un volume offline](#take-a-volume-offline).

4. Quando il volume è offline, fare clic su **Elimina** in fondo alla pagina.

5. Alla richiesta di conferma fare clic su **Sì**. Il volume verrà eliminato e nella pagina **Volumi** sarà visualizzato l’elenco aggiornato di volumi presenti nel contenitore.

## Monitorare a volume

Il monitoraggio del volume consente di raccogliere le statistiche correlate all’I/O per un volume. Il monitoraggio è abilitato per impostazione predefinita per i primi 32 volumi creati dall'utente. Il monitoraggio di ulteriori volumi è disabilitato per impostazione predefinita. Il monitoraggio di volumi clonati è disabilitato per impostazione predefinita.

Per abilitare o disabilitare il monitoraggio per un volume, eseguire i passaggi seguenti.

### Per abilitare o disabilitare il monitoraggio del volume

1. Nella pagina **Dispositivi**, selezionare il dispositivo, fare doppio clic su esso e quindi sulla scheda **Contenitori di volumi**.

2. Selezionare il contenitore del volume in cui risiede il volume, quindi fare clic sul contenitore del volume per accedere alla pagina **Volumi**.

3. Tutti i volumi associati a questo contenitore vengono elencati in formato tabulare. Fare clic e selezionare il volume o un clone del volume.

4. Nella parte inferiore della pagina fare clic su **Modifica**.

5. Nella procedura guidata Modifica volume in **Impostazioni di base**, selezionare **Abilita** o **Disabilita** dall’elenco a discesa **Monitoraggio**.

    ![Modificare le impostazioni di base di un volume](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)


## Passaggi successivi

- Informazioni su come [clonare un volume StorSimple](storsimple-clone-volume.md)
- Informazioni su come [utilizzare il servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_1203_2015-->