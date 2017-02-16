---
title: Distribuire il servizio Gestione dispositivi StorSimple | Documentazione Microsoft
description: Descrive le procedure per creare ed eliminare il servizio Gestione dispositivi StorSimple nel portale di Azure e per gestire la chiave di registrazione del servizio.
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: 1881a0625b107ae1a90e5b772f5296a4d728973d

---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Distribuire il servizio Gestione dispositivi StorSimple per l'array virtuale StorSimple
## <a name="overview"></a>Overview

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Dopo aver creato il servizio, è possibile usarlo per gestire i dispositivi tramite il portale di Microsoft Azure in esecuzione in un browser. Ciò consente di monitorare tutti i dispositivi connessi al servizio Gestione dispositivi StorSimple da un'unica posizione centrale, con una conseguente riduzione del carico amministrativo.

Le attività comuni correlate a un servizio Gestione dispositivi StorSimple sono:

* Creare un servizio
* Eliminare un servizio
* Ottenere la chiave di registrazione del servizio
* Rigenerare la chiave di registrazione del servizio

Questa esercitazione descrive come eseguire ognuna delle attività precedenti. Le informazioni contenute in questo articolo si applicano solo all'array virtuale StorSimple. Per altre informazioni su StorSimple serie 8000, andare a [Distribuire il servizio StorSimple Manager](storsimple-manage-service.md).

## <a name="create-a-service"></a>Creare un servizio

Per creare un servizio, è necessario avere:

* Una sottoscrizione con un contratto Enterprise Agreement
* Un account di archiviazione di Microsoft Azure attivo
* Le informazioni di fatturazione usate per la gestione degli accessi

È inoltre possibile scegliere di generare un account di archiviazione al momento della creazione del servizio.

Un singolo servizio può gestire più dispositivi, ma un dispositivo non può estendersi a più servizi. Una grande impresa può avere più istanze del servizio per lavorare con diverse sottoscrizioni, organizzazioni o anche percorsi di distribuzione.

> [!NOTE]
> Per gestire dispositivi StorSimple serie 8000 e array virtuali StorSimple sono necessarie istanze separate del servizio Gestione dispositivi StorSimple.


Per creare un servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminare un servizio

Prima di eliminare un servizio, verificare che non sia usato da dispositivi connessi. Se il servizio è in uso, disattivare i dispositivi connessi. L'operazione di disattivazione interromperà la connessione tra il dispositivo e il servizio mantenendo i dati del dispositivo nel cloud.

> [!IMPORTANT]
> Dopo che un servizio è stato eliminato, l'operazione non può essere annullata. Sarà necessario ripristinare le impostazioni predefinite di ogni dispositivo che usa questo servizio prima che il dispositivo possa essere usato con un altro servizio. In questo scenario i dati locali sul dispositivo e la configurazione andranno persi.
 

Per eliminare un servizio, attenersi alla procedura seguente.

#### <a name="to-delete-a-service"></a>Per eliminare un servizio

1. Passare a **Tutte le risorse**. Ricercare il servizio Gestione dispositivi StorSimple. Selezionare il servizio da eliminare.
   
    ![Selezionare il servizio da eliminare](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Passare al dashboard del servizio per verificare che non siano presenti dispositivi connessi al servizio. Se non sono presenti dispositivi registrati con questo servizio, verrà visualizzato un messaggio di intestazione in proposito. Fare clic su **Elimina**.
   
    ![Delete service](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Quando viene richiesta la conferma, fare clic su **Sì** nella notifica di conferma. 
   
    ![Confermare l'eliminazione del servizio](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. L'eliminazione del servizio può richiedere alcuni minuti. Dopo aver eliminato correttamente il servizio, si riceverà una notifica.
   
    ![Eliminazione del servizio riuscita](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

L'elenco dei servizi verrà aggiornato.

 ![Elenco aggiornato dei servizi](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio
Dopo aver creato un servizio, è necessario registrare il dispositivo StorSimple. Per registrare il primo dispositivo StorSimple, è necessaria la chiave di registrazione del servizio. Per registrare altri dispositivi con un servizio StorSimple esistente, sono necessarie la chiave di registrazione e la chiave DEK del servizio (che viene generata durante la registrazione sul primo dispositivo). Per altre informazioni sulla chiave DEK del servizio, vedere [Sicurezza in StorSimple](storsimple-security.md). È possibile ottenere la chiave di registrazione mediante il pannello **Chiavi** per il servizio.

Per ottenere la chiave di registrazione del servizio, attenersi alla procedura seguente.

#### <a name="to-get-the-service-registration-key"></a>Per ottenere la chiave di registrazione del servizio
1. In **Gestione dispositivi StorSimple** passare a **Gestione&gt;** **Chiavi**.
   
   ![Pannello Chiavi](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Nel pannello **Chiavi** viene visualizzata la chiave di registrazione del servizio. Copiare la chiave di registrazione usando l'icona di copia. 

Conservare la chiave di registrazione del servizio in una posizione sicura. Questa chiave e la chiave DEK del servizio saranno necessarie per registrare altri dispositivi con il servizio. Dopo aver ottenuto la chiave di registrazione del servizio, è necessario configurare il dispositivo tramite l'interfaccia di Windows PowerShell per StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio
La rigenerazione di una chiave di registrazione del servizio deve essere effettuata quando è necessario eseguire la rotazione delle chiavi o se l'elenco di amministratori del servizio è stato modificato. Quando si rigenera la chiave, quest'ultima viene usata solo per registrare dispositivi successivi. I dispositivi già registrati non sono interessati da questo processo.

Per rigenerare una chiave di registrazione del servizio, attenersi alla procedura seguente.

#### <a name="to-regenerate-the-service-registration-key"></a>Per rigenerare la chiave di registrazione del servizio
1. In **Gestione dispositivi StorSimple** passare a **Gestione&gt;** **Chiavi**.
   
   ![Pannello Chiavi](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Nel pannello **Chiavi** fare clic su **Rigenera**.
   
   ![Fare clic su Rigenera](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Nel pannello **Rigenera la chiave di registrazione del servizio** esaminare l'azione necessaria quando le chiavi vengono rigenerate. Tutti i dispositivi successivi registrati con questo servizio useranno la nuova chiave di registrazione. Fare clic su **Rigenera** per confermare. Al completamento della registrazione si riceverà una notifica.
   
   ![Confermare la chiave di rigenerazione](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Verrà visualizzata una nuova chiave di registrazione del servizio.
   
    ![Confermare la chiave di rigenerazione](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Copiare la chiave e salvarla per registrare eventuali nuovi dispositivi con il servizio.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [iniziare a usare](storsimple-virtual-array-deploy1-portal-prep.md) un array virtuale StorSimple.
* Informazioni su come [amministrare il dispositivo StorSimple](storsimple-ova-web-ui-admin.md).




<!--HONumber=Jan17_HO4-->


