---
title: Gestire le condivisioni sull'array virtuale StorSimple | Documentazione Microsoft
description: Descrive Gestione dispositivi StorSimple e illustra come usare questo servizio per gestire le condivisioni sull'array virtuale StorSimple.
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Usare il servizio Gestione dispositivi StorSimple per visualizzare le condivisioni sull'array virtuale StorSimple

## <a name="overview"></a>Panoramica

Questa esercitazione illustra come usare il servizio Gestione dispositivi StorSimple per creare e gestire le condivisioni sull'array virtuale StorSimple.

Il servizio Gestione dispositivi StorSimple è un'estensione del portale di Azure che consente di gestire la soluzione StorSimple da un'unica interfaccia Web. Oltre alla gestione di condivisioni e volumi, è possibile usare il servizio Gestione dispositivi StorSimple per visualizzare e gestire dispositivi, visualizzare avvisi, gestire criteri di backup e il catalogo di backup.

## <a name="share-types"></a>Tipi di condivisione

Le condivisioni StorSimple possono essere:

* **Aggiunto in locale**: i dati in queste condivisioni rimangono sempre nell'array e non vengono distribuiti nel cloud.
* **A livelli**: i dati in queste condivisioni possono essere distribuiti nel cloud. Quando si crea una condivisione a livelli, viene eseguito il provisioning di circa il 10% dello spazio a livello locale e del 90% dello spazio nel cloud. Ad esempio, se si esegue il provisioning di una condivisione da 1 TB, 100 GB si trovano nello spazio locale e 900 GB vengono usati nel cloud quando i dati sono disposti a livelli. Questo implica che, se si esaurisce tutto lo spazio locale nel dispositivo, non è possibile eseguire il provisioning di una condivisione a livelli perché il 10% necessario a livello locale non è disponibile.

### <a name="provisioned-capacity"></a>Capacità con provisioning

Fare riferimento alla tabella seguente per conoscere la capacità massima di cui viene eseguito il provisioning per ogni tipo di condivisione.

| **Identificatore limite** | **Limite** |
| --- | --- |
| Dimensione minima di una condivisione a livelli |500 GB |
| Dimensione massima di una condivisione a livelli |20 TB |
| Dimensione minima di una condivisione aggiunta in locale |50 GB |
| Dimensione massima di una condivisione aggiunta in locale |2 TB |

## <a name="the-shares-blade"></a>Il pannello Condivisioni

Il menu **Condivisioni** nel pannello di riepilogo servizio StorSimple visualizza l'elenco di condivisioni di archiviazione su un determinato array StorSimple e ne consente la gestione.

![Pannello Condivisioni](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Una condivisione è costituita da una serie di attributi:

* **Nome condivisione**: un nome descrittivo che deve essere univoco e identifica la condivisione.
* **Stato** : online oppure offline. Se una condivisione è offline, gli utenti della condivisione non saranno in grado di accedervi.
* **Tipo**: indica se la condivisione è **A livelli** (impostazione predefinita) o **Aggiunto in locale**.
* **Capacità**: specifica la quantità di dati usati rispetto alle quantità totale di dati che possono essere archiviati nella condivisione.
* **Descrizione**: impostazione facoltativa che consente di descrivere la condivisione.
* **Autorizzazioni**: autorizzazioni NTFS per la condivisione che possono essere gestite tramite Esplora risorse.
* **Backup**: nel caso dell'array virtuale StorSimple, tutte le condivisioni vengono abilitate automaticamente per il backup.

![Dettagli sulle condivisioni](./media/storsimple-virtual-array-manage-shares/share-details.png)

Usare le istruzioni di questa esercitazione per eseguire le attività seguenti:

* Aggiungere una condivisione
* Modificare una condivisione
* Portare offline una condivisione
* Eliminare una condivisione

## <a name="add-a-share"></a>Aggiungere una condivisione

1. Nel pannello di riepilogo servizio StorSimple fare clic su **+ Aggiungi condivisione** dalla barra dei comandi. Verrà visualizzato il pannello **Aggiungi condivisione**.

    ![Aggiungere la condivisione](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Nel pannello **Aggiungi condivisione** eseguire le operazioni seguenti:
   
    1. Nel campo **Nome della condivisione** immettere un nome univoco per la condivisione. Il nome deve essere una stringa contenente da 3 a 127 caratteri.

    2. Una **Descrizione** facoltativa per la condivisione. La descrizione consente di identificare i proprietari della condivisione.

    3. Nell'elenco a discesa **Tipo** specificare se creare una condivisione **A livelli** o **Aggiunto in locale**. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni di livello superiore, selezionare la condivisione **Aggiunto in locale**. Per tutti gli altri dati, selezionare una condivisone **A livelli** .

    4. Nel campo **Capacità** specificare le dimensioni della condivisione. Una condivisione a livelli deve essere compresa tra 500 GB e 20 TB e una condivisione aggiunta in locale deve essere compresa tra 50 GB e 2 TB.

    5. Nel campo **Set default full permissions to** (Imposta le autorizzazioni complete predefinite su) assegnare le autorizzazioni all'utente o al gruppo che avrà accesso a questa condivisione. Specificare il nome dell'utente o del gruppo di utenti nel formato _john@contoso.com_. Si consiglia di usare un gruppo di utenti (anziché un singolo utente) per consentire ai privilegi amministratore di accedere a queste condivisioni. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.
3. Al termine della configurazione della condivisione fare clic su **Crea**. Verrà creata una condivisione con le impostazioni specificate e verrà visualizzata una notifica. Per impostazione predefinita, il backup verrà abilitato per la condivisione.
4. Per verificare che la condivisione sia stata creata, passare al pannello **Condivisioni**. La condivisione viene visualizzata nell'elenco.
   
    ![Creazione della condivisione completata](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Modificare una condivisione

Modificare una condivisione quando è necessario modificarne la descrizione. Dopo la creazione della condivisione, non è possibile modificare nessuna altra proprietà della condivisione.

#### <a name="to-modify-a-share"></a>Per modificare una condivisione

1. Nell'impostazione **Condivisioni** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede la condivisione da modificare.
2. **Selezionare** la condivisione per visualizzare e modificare la descrizione corrente.
3. Salvare le modifiche facendo clic sulla barra di comando **Salva**. Verranno applicate le impostazioni specificate e verrà visualizzata una notifica.
   
    ![ Modificare la condivisione](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Portare offline una condivisione

Potrebbe essere necessario portare una condivisione offline per modificarla o eliminarla. Quando una condivisione è offline, non è disponibile per l'accesso in lettura/scrittura. È necessario portare offline la condivisione sull'host e sul dispositivo.

#### <a name="to-take-a-share-offline"></a>Per portare offline una condivisione

1. Assicurarsi che la condivisione in questione non sia in uso prima di portarla offline.
2. Portare offline la condivisione dell'array attenendosi alla procedura seguente:
   
    1. Nell'impostazione **Condivisioni** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede la condivisione da portare offline.

    2. **Selezionare** la condivisione e fare clic su **...** (in alternativa fare clic con il pulsante destro del mouse su questa riga) e selezionare **Porta offline** nel menu di scelta rapida.
     
        ![Condivisione offline](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Esaminare le informazioni nel pannello **Porta offline** e confermare l'accettazione dell'operazione. Fare clic su **Porta offline** per portare offilne la condivisione. Verrà visualizzata una notifica dell'operazione in corso.

    4. Per verificare che la condivisione sia stata portata offline, passare al pannello **Condivisioni**. Lo stato della condivisione dovrebbe essere offline.

## <a name="delete-a-share"></a>Eliminare una condivisione

> [!IMPORTANT]
> È possibile eliminare una condivisione solo se è offline.


Completare la procedura seguente per eliminare una condivisione.

#### <a name="to-delete-a-share"></a>Per eliminare una condivisione

1. Nell'impostazione **Condivisioni** del pannello di riepilogo servizio StorSimple selezionare l'array virtuale in cui risiede la condivisione da eliminare.
2. **Selezionare** la condivisione e fare clic su **...** (in alternativa fare clic con il pulsante destro del mouse su questa riga) e selezionare **Elimina** nel menu di scelta rapida.
   
    ![Eliminare la condivisione](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Verificare lo stato della condivisione che si vuole eliminare. Se la condivisione da eliminare non è offline, portarla prima offline. Seguire la procedura illustrata in [Portare offline una condivisione](#take-a-share-offline).
4. Alla richiesta di conferma nel pannello **Elimina** accettare la conferma e fare clic su **Elimina**. Verrà eliminata la condivisione; il pannello **Condivisioni** mostra l'elenco aggiornato delle condivisioni all'interno dell'array virtuale.

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [clonare una condivisione StorSimple](storsimple-virtual-array-clone.md).

