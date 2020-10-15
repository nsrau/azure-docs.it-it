---
title: Creare il peering di replica per Azure NetApp Files | Microsoft Docs
description: Viene descritto come creare il peering della replica del volume per Azure NetApp Files per configurare la replica tra aree.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708826"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>Creare il peering di replica per Azure NetApp Files

> [!IMPORTANT]
> La funzionalità di replica tra aree è attualmente disponibile in anteprima pubblica. È necessario inviare una richiesta di attesa per l'accesso alla funzionalità tramite la pagina Azure NetApp Files l'invio dell'oggetto di attesa per la [replica tra aree](https://aka.ms/anfcrrpreviewsignup). Attendere un messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files prima di usare la funzionalità di replica tra aree.

Questo articolo illustra come configurare la replica tra aree creando il peering di replica. 

La configurazione del peering di replica consente di replicare in modo asincrono i dati da un volume Azure NetApp Files (origine) a un altro volume Azure NetApp Files (destinazione). Il volume di origine e il volume di destinazione devono essere distribuiti in aree separate. Il livello di servizio per il pool di capacità di destinazione può corrispondere a quello del pool di capacità di origine oppure è possibile selezionare un livello di servizio diverso.   

Azure NetApp Files replica non supporta attualmente più sottoscrizioni. tutte le repliche devono essere eseguite in una singola sottoscrizione.

Prima di iniziare, assicurarsi di aver esaminato i [requisiti e le considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Individuare l'ID di risorsa del volume di origine  

È necessario ottenere l'ID risorsa del volume di origine che si vuole replicare. 

1. Passare al volume di origine e selezionare **Proprietà** in impostazioni per visualizzare l'ID di risorsa del volume di origine.   
    ![Individuare l'ID risorsa del volume di origine](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Copiare l'ID risorsa negli Appunti.  Sarà necessario più avanti.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Creare il volume di replica dei dati (volume di destinazione)

È necessario creare un volume di destinazione in cui si desidera replicare i dati del volume di origine.  Prima di poter creare un volume di destinazione, è necessario avere un account NetApp e un pool di capacità nell'area di destinazione. 

1. L'account di destinazione deve trovarsi in un'area diversa dall'area del volume di origine. Se necessario, creare un account NetApp nell'area di Azure da usare per la replica attenendosi alla procedura illustrata in [creare un account NetApp](azure-netapp-files-create-netapp-account.md).   
È anche possibile selezionare un account NetApp esistente in un'area diversa.  

2. Se necessario, creare un pool di capacità nell'account NetApp appena creato attenendosi alla procedura descritta in [configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).   

    È anche possibile selezionare un pool di capacità esistente per ospitare il volume di destinazione della replica.  

    Il livello di servizio per il pool di capacità di destinazione può corrispondere a quello del pool di capacità di origine oppure è possibile selezionare un livello di servizio diverso.

3. Delegare una subnet nell'area da usare per la replica attenendosi alla procedura descritta in [delegare una subnet a Azure NetApp files](azure-netapp-files-delegate-subnet.md).

4. Creare il volume di replica dei dati selezionando **volumi** in servizio di archiviazione nell'account NetApp di destinazione. Fare quindi clic sul pulsante **+ Aggiungi replica dati** .  

    ![Aggiungere la replica dei dati](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Nella pagina Crea un volume visualizzato, completare i campi seguenti nella scheda **nozioni di base** :
    * Nome del volume
    * Pool di capacità
    * Quota volume
        > [!NOTE] 
        > Si consiglia di rispecchiare le dimensioni della quota del volume del volume di origine.
    * Rete virtuale 
    * Subnet

    Per informazioni dettagliate sui campi, vedere [creare un volume NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. Nella scheda **protocollo** selezionare lo stesso protocollo del volume di origine.  
Per il protocollo NFS, assicurarsi che le regole dei criteri di esportazione soddisfino i requisiti di tutti gli host nella rete remota che accedono all'esportazione.  

7. Nella scheda **tag** creare coppie chiave/valore in base alle esigenze.  

8. Nella scheda **replica** incollare l'ID di risorsa del volume di origine ottenuto in [individuare l'ID di risorsa del volume di origine](#locate-the-source-volume-resource-id), quindi selezionare la pianificazione della replica desiderata. Le opzioni per la pianificazione della replica includono: ogni 10 minuti, orari, giornalieri, settimanali e mensili.  

    ![Creazione della replica del volume](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Fare clic su **Verifica + crea**e quindi su **Crea** per creare il volume di replica dei dati.   

    ![Esaminare e creare la replica](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autorizzare la replica dal volume di origine  

Per autorizzare la replica, è necessario ottenere l'ID risorsa del volume di destinazione della replica e incollarlo nel campo autorizzazione del volume di origine della replica. 

1. Nella portale di Azure passare a Azure NetApp Files.

2. Passare all'account NetApp di destinazione e al pool di capacità di destinazione in cui si trova il volume di destinazione della replica.

3. Selezionare il volume di destinazione della replica, passare a **Proprietà** in impostazioni e individuare l' **ID risorsa** del volume di destinazione. Copiare l'ID di risorsa del volume di destinazione negli Appunti.

    ![ID risorsa proprietà](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. In Azure NetApp Files passare all'account di origine della replica e al pool di capacità di origine. 

5. Individuare il volume di origine della replica e selezionarlo. Passare a **replica** in servizio di archiviazione e fare clic su **autorizza**.

    ![Autorizzare la replica](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. Nel campo autorizzazione incollare l'ID di risorsa del volume di replica di destinazione ottenuto nel passaggio 3, quindi fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Gestire il ripristino di emergenza](cross-region-replication-manage-disaster-recovery.md)
* [Risolvere i problemi relativi alla replica tra aree](troubleshoot-cross-region-replication.md)

