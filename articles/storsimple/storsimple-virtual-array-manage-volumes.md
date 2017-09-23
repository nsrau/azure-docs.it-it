---
title: Gestire i volumi sull'array virtuale StorSimple | Documentazione Microsoft
description: Descrive Gestione dispositivi StorSimple e illustra come usare questo servizio per gestire i volumi sull'array virtuale StorSimple.
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.contentlocale: it-it
ms.lasthandoff: 01/26/2017

---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Usare il servizio Gestione dispositivi StorSimple per visualizzare i volumi sull'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare il servizio Gestione dispositivi StorSimple per creare e gestire i volumi nell'array virtuale StorSimple.

Il servizio Gestione dispositivi StorSimple è un'estensione del portale di Azure che consente di gestire la soluzione StorSimple da un'unica interfaccia Web. Oltre alla gestione di condivisioni e volumi, è possibile usare il servizio Gestione dispositivi StorSimple per visualizzare e gestire dispositivi, visualizzare avvisi, visualizzare e gestire criteri di backup e il catalogo di backup.

## <a name="volume-types"></a>Tipi di volume

I volumi di StorSimple possono essere:

* **Aggiunto in locale**: i dati in questi volumi rimangono sempre nell'array e non vengono distribuiti nel cloud.
* **A livelli**: i dati in questi volumi possono essere distribuiti nel cloud. Quando si crea un volume a livelli, viene eseguito il provisioning di circa il 10% dello spazio a livello locale e del 90% dello spazio nel cloud. Ad esempio, se si esegue il provisioning di un volume da 1 TB, 100 GB si trovano nello spazio locale e 900 GB vengono usati nel cloud quando i dati sono disposti a livelli. Questo implica che, se si esaurisce tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una volume a livelli (perché il 10% necessario a livello locale non è disponibile).

### <a name="provisioned-capacity"></a>Capacità con provisioning
Fare riferimento alla tabella seguente per conoscere la capacità massima di cui viene eseguito il provisioning per ogni tipo di volume.

| **Identificatore limite**                                       | **Limite**     |
|------------------------------------------------------------|---------------|
| Dimensione minima di un volume a livelli                            | 500 GB        |
| Dimensione massima di un volume a livelli                            | 5 TB          |
| Dimensione minima di un volume aggiunto in locale                    | 50 GB         |
| Dimensione massima di un volume aggiunto in locale                    | 500 GB        |

## <a name="the-volumes-blade"></a>Il pannello Volumi
Il menu **Volumi** nel pannello di riepilogo servizio StorSimple visualizza l'elenco di volumi di archiviazione su un determinato array StorSimple e ne consente la gestione.

![Pannello Volumi](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Un volume è costituito da una serie di attributi:

* **Nome volume** : un nome descrittivo che deve essere univoco e consente di identificare il volume.
* **Stato** : online oppure offline. Se un volume è offline, non è visibile agli iniziatori (server) che possono accedervi per usarlo.
* **Tipo**: indica se il volume è **A livelli** (impostazione predefinita) o **Aggiunto in locale**.
* **Capacità**: specifica la quantità di dati usati rispetto alle quantità totale di dati che possono essere archiviati dall'iniziatore (server).
* **Backup**: nel caso dell'array virtuale StorSimple, tutti i volumi vengono abilitati automaticamente per il backup.
* **Host connessi**: specifica gli iniziatori (server) a cui è consentito l'accesso a questo volume.

![Dettagli sui volumi](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Usare le istruzioni di questa esercitazione per eseguire le attività seguenti:

* Aggiungere un volume
* Modificare un volume
* Portare un volume offline
* Eliminare un volume

## <a name="add-a-volume"></a>Aggiungere un volume

1. Nel pannello di riepilogo servizio StorSimple fare clic su **+ Aggiungi volume** dalla barra dei comandi. Verrà visualizzato il pannello **Aggiungi volume**.
   
    ![Aggiungi volume](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Nel pannello **Aggiungi volume** eseguire le operazioni seguenti:
   
   * Nel campo **Nome del volume** immettere un nome univoco per il volume. Il nome deve essere una stringa contenente da 3 a 127 caratteri.
   * Nell'elenco a discesa **Tipo** specificare se creare un volume **A livelli** o **Aggiunto in locale**. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni di livello superiore, selezionare il volume **Aggiunto in locale**. Per tutti gli altri dati, selezionare volume **A livelli**.
   * Nel campo **Capacità** specificare le dimensioni del volume. Un volume a livelli deve essere compreso tra 500 GB e 5 TB e un volume aggiunto in locale deve essere compreso tra 50 e 500 GB.
   * * Fare clic su **Host connessi**, selezionare un record di controllo di accesso (ACR) corrispondente all'iniziatore iSCSI a cui si desidera connettere questo volume, quindi fare clic su **Seleziona**.
3. Per aggiungere un nuovo host connesso, fare clic su **Aggiungi nuovo**, immettere un nome per l'host e il relativo nome qualificato iSCSI (IQN) e quindi fare clic su **Aggiungi**.
   
    ![Aggiungi volume](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Al termine della configurazione del volume, fare clic su **Crea**. Verrà creato un volume con le impostazioni specificate e verrà visualizzata una notifica sulla creazione. Per impostazione predefinita, il backup verrà abilitato per il volume.
5. Per verificare che il volume sia stato creato, passare al pannello **Volumi**. Il volume deve essere visualizzato nell'elenco.
   
    ![Creazione del volume completata](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Modificare un volume

Modificare un volume quando occorre modificare gli host che vi accedono. Gli altri attributi di un volume non possono essere modificati dopo aver creato il volume.

#### <a name="to-modify-a-volume"></a>Per modificare un volume

1. Nell'impostazione **Volumi** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede il volume da modificare.
2. **Selezionare** il volume e fare clic su **Host connessi** per visualizzare l'host attualmente connesso e modificarlo in un altro server.
   
    ![Modificare il volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Salvare le modifiche facendo clic sulla barra di comando **Salva**. Verranno applicate le impostazioni specificate e verrà visualizzata una notifica.

## <a name="take-a-volume-offline"></a>Portare un volume offline

Potrebbe essere necessario portare un volume offline per modificarlo o eliminarlo. Quando un volume è offline, non è disponibile per l'accesso in lettura/scrittura. È necessario portare offline il volume nell'host e sul dispositivo.

#### <a name="to-take-a-volume-offline"></a>Per portare un volume offline

1. Assicurarsi che il volume in questione non sia in uso prima di portarlo offline.
2. Portare offline il volume prima nell'host. Ciò consente di eliminare qualsiasi potenziale rischio di danneggiamento dei dati nel volume. Per i passaggi specifici, vedere le istruzioni per il sistema operativo dell’host.
3. Quando il volume sull'host è offline, portare il volume sull'array offline eseguendo i passaggi seguenti:
   
   * Nell'impostazione **Volumi** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede il volume da portare offline.
   * **Selezionare** il volume e fare clic su **...** (in alternativa fare clic con il pulsante destro del mouse su questa riga) e selezionare **Porta offline** nel menu di scelta rapida.
     
        ![Volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Esaminare le informazioni nel pannello **Porta offline** e confermare l'accettazione dell'operazione. Fare clic su **Porta offline** per portare offilne il volume. Verrà visualizzata una notifica dell'operazione in corso.
   * Per verificare se il volume è stato portato offline, passare al pannello **Volumi**. Lo stato del volume dovrebbe apparire come offline.
     
       ![Conferma volume offline](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Eliminare un volume

> [!IMPORTANT]
> È possibile eliminare un volume solo se è offline.
> 
> 

Completare la procedura seguente per eliminare un volume.

#### <a name="to-delete-a-volume"></a>Per eliminare un volume

1. Nell'impostazione **Volumi** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede il volume da eliminare.
2. **Selezionare** il volume e fare clic su **...** (in alternativa fare clic con il pulsante destro del mouse su questa riga) e selezionare **Elimina** nel menu di scelta rapida.
   
    ![Eliminare il volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Verificare lo stato del volume che si desidera eliminare. Se il volume che si desidera eliminare non è offline, portarlo innanzitutto offline seguendo i passaggi indicati in [Portare un volume offline](#take-a-volume-offline).
4. Alla richiesta di conferma nel pannello **Elimina** accettare la conferma e fare clic su **Elimina**. Il volume verrà eliminato e nella pagina **Volumi** sarà visualizzato l'elenco aggiornato di volumi presenti nell'array virtuale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [clonare un volume StorSimple](storsimple-virtual-array-clone.md)


