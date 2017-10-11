---
title: Distribuire il servizio StorSimple Manager | Microsoft Docs
description: Descrizione delle procedure per creare ed eliminare il servizio StorSimple Manager nel portale di Azure classico e per gestire la chiave di registrazione del servizio.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba3637a3a8b15b45c16bf5a00c1f4225bcfc5af8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>Distribuire il servizio StorSimple Manager nel portale di Azure classico

## <a name="overview"></a>Panoramica
Il servizio StorSimple Manager viene eseguito in Microsoft Azure ed è connesso a più dispositivi di archiviazione StorSimple. Dopo aver creato il servizio, è possibile usarlo per gestire i dispositivi tramite il portale di Microsoft Azure classico in esecuzione in un browser. Questo consente di monitorare tutti i dispositivi connessi al servizio StorSimple Manager da un'unica posizione centrale, con una conseguente riduzione del carico amministrativo.

Nella pagina di destinazione di StorSimple Manager vengono elencate tutte le istanze di StorSimple Manager che è possibile usare per gestire i dispositivi di archiviazione StorSimple. Per ogni servizio, nella pagina di StorSimple Manager vengono visualizzate le informazioni seguenti:

* **Nome** : nome assegnato al servizio StorSimple Manager al momento della creazione. **Il nome non può essere modificato dopo la creazione del servizio. Questo vale anche per altre entità, ad esempio dispositivi, volumi, contenitori dei volumi e criteri di backup che non è possibile rinominare nel portale di Azure classico.**
* **Stato**: stato del servizio, che può essere **Attivo**, **Creazione in corso** oppure **Online**.
* **Posizione** : posizione geografica in cui verrà distribuito il dispositivo StorSimple.
* **Sottoscrizione** : sottoscrizione di fatturazione associata al servizio.

Nella pagina StorSimple Manager è possibile eseguire queste attività comuni:

* Creare un servizio
* Eliminare un servizio
* Ottenere la chiave di registrazione del servizio
* Rigenerare la chiave di registrazione del servizio

Questa esercitazione descrive come eseguire queste attività.

## <a name="create-a-service"></a>Creare un servizio
Usare l'opzione **Creazione rapida** per creare un servizio StorSimple Manager se si desidera distribuire il dispositivo StorSimple. Per creare un servizio, è necessario avere:

* Una sottoscrizione con un contratto Enterprise Agreement
* Un account di archiviazione di Microsoft Azure attivo
* Le informazioni di fatturazione usate per la gestione degli accessi

È inoltre possibile scegliere di generare un account di archiviazione predefinito al momento della creazione del servizio.

Un singolo servizio può gestire più dispositivi, ma un dispositivo non può estendersi a più servizi. Una grande impresa può avere più istanze del servizio per lavorare con diverse sottoscrizioni, organizzazioni o anche percorsi di distribuzione. Tenere presente che per gestire dispositivi StorSimple serie 8000 e StorSimple Virtual Array sono necessarie istanze separate del servizio StorSimple Manager.

> [!IMPORTANT] 
> Se è stato creato un servizio non usato (non sono state eseguite operazioni di dispositivo su questa risorsa) prima di agosto 2016, il servizio non può essere gestito tramite il portale di Azure o il portale di Azure classico. È consigliabile creare un nuovo servizio nel portale di Azure.

Per creare un servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminare un servizio
Prima di eliminare un servizio, verificare che non sia usato da dispositivi connessi. Se il servizio è in uso, disattivare i dispositivi connessi. L'operazione di disattivazione interromperà la connessione tra il dispositivo e il servizio mantenendo i dati del dispositivo nel cloud.

> [!IMPORTANT] 
> Dopo che un servizio è stato eliminato, l'operazione non può essere annullata. Sarà necessario ripristinare le impostazioni predefinite di ogni dispositivo che usa questo servizio prima che il dispositivo possa essere usato con un altro servizio. In questo scenario i dati locali sul dispositivo e la configurazione andranno persi.

Per eliminare un servizio, attenersi alla procedura seguente.

### <a name="to-delete-a-service"></a>Per eliminare un servizio
1. Nella pagina del **servizio StorSimple Manager** selezionare il servizio da eliminare.
2. Nella parte inferiore della pagina fare clic su **Elimina** .
3. Nel messaggio di richiesta di conferma fare clic su **Sì** . L'eliminazione del servizio può richiedere alcuni minuti.

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio
Dopo aver creato un servizio, è necessario registrare il dispositivo StorSimple. Per registrare il primo dispositivo StorSimple, è necessaria la chiave di registrazione del servizio. Per registrare altri dispositivi con un servizio StorSimple esistente, sono necessarie la chiave di registrazione e la chiave DEK del servizio (che viene generata durante la registrazione sul primo dispositivo). Per altre informazioni sulla chiave DEK del servizio, vedere [Sicurezza in StorSimple](storsimple-security.md). È possibile ottenere la chiave di registrazione mediante **Chiave di registrazione** on the **Servizi** .

Per ottenere la chiave di registrazione del servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

Conservare la chiave di registrazione del servizio in una posizione sicura. Questa chiave e la chiave DEK del servizio saranno necessarie per registrare altri dispositivi con il servizio. Dopo aver ottenuto la chiave di registrazione del servizio, è necessario configurare il dispositivo tramite l'interfaccia di Windows PowerShell per StorSimple.

Per informazioni dettagliate sull'uso della chiave di registrazione, vedere [Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio
La rigenerazione di una chiave di registrazione del servizio deve essere effettuata quando è necessario eseguire la rotazione delle chiavi o se l'elenco di amministratori del servizio è stato modificato. Quando si rigenera la chiave, quest'ultima viene usata solo per registrare dispositivi successivi. I dispositivi già registrati non sono interessati da questo processo.

Per rigenerare una chiave di registrazione del servizio, attenersi alla procedura seguente.

### <a name="to-regenerate-the-service-registration-key"></a>Per rigenerare la chiave di registrazione del servizio
1. Nella pagina del **servizio StorSimple Manager** fare clic su **Chiave di registrazione**.
2. Nella finestra di dialogo **Chiave di registrazione del servizio** fare clic su **Rigenera**.
3. Verrà visualizzato un messaggio di conferma. Fare clic su **OK** per continuare la rigenerazione.
4. Verrà visualizzata una nuova chiave di registrazione del servizio.
5. Copiare la chiave e salvarla per registrare eventuali nuovi dispositivi con il servizio.
6. Fare clic sull'icona con il segno di spunta  ![Icona del segno di spunta](./media/storsimple-manage-service/HCS_CheckIcon.png) per chiudere la finestra di dialogo.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [processo di distribuzione di StorSimple](storsimple-deployment-walkthrough-u2.md).
* Ulteriori informazioni sulla [gestione dell'account di archiviazione di StorSimple](storsimple-manage-storage-accounts.md).
* Ulteriori informazioni sull’ [utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
