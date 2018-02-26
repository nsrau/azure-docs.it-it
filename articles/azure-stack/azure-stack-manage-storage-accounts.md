---
title: Gestire gli account di archiviazione di Azure Stack | Documenti Microsoft
description: Informazioni su come trovare, gestire, ripristinare e recuperare gli account di archiviazione di Azure Stack
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: brenduns
ms.reviewer: anirudha
ms.openlocfilehash: 3ef9a66095d0ed5fc865dc3c22961f9f7bdcedd9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gestire gli account di archiviazione in Azure Stack
Informazioni su come gestire gli account di archiviazione nello Stack di Azure per trovare, ripristinare e recuperare la capacità di archiviazione in base alle esigenze aziendali.

## <a name="find"></a>Trovare un account di archiviazione
L'elenco di account di archiviazione nell'area può essere visualizzato nello Stack di Azure per:

1. In un browser Internet, passare a https://adminportal.local.azurestack.external.
2. Accedere al portale di amministrazione di Azure Stack come un operatore cloud (utilizzando le credenziali specificate durante la distribuzione)
3. Nel dashboard predefinito: trovare il **Gestione area** elenco e fare clic sull'area in cui si desidera esplorare. Ad esempio **(locale**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Selezionare **archiviazione** dal **i provider di risorse** elenco.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. A questo punto, nel Pannello di amministrazione Provider di risorse di archiviazione: scorrere verso il basso il **gli account di archiviazione** scheda e farvi clic sopra.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Nella pagina risultante è l'elenco di account di archiviazione in tale area.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Per impostazione predefinita, vengono visualizzati i primi 10 conti. È possibile scegliere di recuperare informazioni facendo clic di **caricare più** collegamento nella parte inferiore dell'elenco.

Oppure

Se si è interessati a un determinato account di archiviazione, è possibile **filtrare e recuperare gli account rilevanti** solo.


**Per filtrare per gli account:**

1. Fare clic su **filtro** nella parte superiore del pannello.
2. Nel pannello filtro, consente di specificare **nome account**, **ID sottoscrizione** o **stato** per ottimizzare l'elenco di account di archiviazione da visualizzare. Li utilizzi come appropriato.
3. Fare clic su **Update**. L'elenco è necessario aggiornare di conseguenza.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Per reimpostare il filtro: fare clic su **filtro**, cancellare le selezioni e aggiornare.

La casella di testo di ricerca (nella parte superiore nel Pannello di elenco degli account di archiviazione) consente di evidenziare il testo selezionato nell'elenco di account. Questo è molto utile nel caso quando il nome completo o l'id non è facilmente disponibile.

È possibile utilizzare qui testo libero per individuare l'account a cui che si è interessati.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Esaminare i dettagli dell'account
Dopo aver individuato gli account che si desidera visualizzare, è possibile scegliere l'account specifico per visualizzare determinate informazioni. Apre un nuovo pannello con i dettagli dell'account, ad esempio: il tipo dell'account, l'ora di creazione, posizione, e così via.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Ripristinare un account eliminato
Potrebbe essere in una situazione in cui è necessario ripristinare un account eliminato.

Nello Stack di Azure è un modo molto semplice per eseguire questa operazione:

1. Selezionare l'elenco di account di archiviazione. Vedere [trovare un account di archiviazione](#find) in questo argomento per ulteriori informazioni.
2. Individuare l'account specifico nell'elenco. Potrebbe essere necessario filtrare.
3. Controllare il *stato* dell'account. Dovrebbe essere visualizzato **Deleted**.
4. Fare clic sull'account che verrà aperto il pannello dei dettagli account.
5. Nella parte superiore di questo pannello, individuare il **ripristinare** pulsante e farvi clic sopra.
6. Fare clic su **Sì** per confermare.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. È ora il ripristino *elaborare... Attendere* per un'indicazione che sia stato eseguito correttamente.
   È anche possibile fare clic sull'icona "campanello" nella parte superiore del portale per visualizzare le indicazioni di stato di avanzamento.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Una volta che l'account ripristinato correttamente è sincronizzato, può essere usata nuovamente.

### <a name="some-gotchas"></a>Alcuni problemi
* L'account eliminato Mostra lo stato come **fuori memorizzazione**.
  
  Ciò significa che l'account eliminato ha superato il periodo di memorizzazione e potrebbe non essere ripristinabile.
* L'account eliminato non viene visualizzato nell'elenco degli account.
  
  Ciò vuol dire che l'account eliminato è già stato sottoposto a garbage collection. In questo caso non può essere recuperato. Vedere [recuperare capacità](#reclaim) in questo argomento.

## <a name="set-the-retention-period"></a>Impostare il periodo di memorizzazione
L'impostazione del periodo di conservazione consente un operatore di cloud specificare un periodo di tempo in giorni (compreso tra 0 e 9999 giorni) durante il quale può essere recuperato potenzialmente qualsiasi account eliminato. Periodo di memorizzazione predefinito è impostato su 15 giorni. Impostazione del valore su "0", tutti gli account eliminati immediatamente fuori memorizzazione che sia contrassegnata per periodicamente operazioni di garbage collection.

**Per modificare il periodo di memorizzazione:**

1. In un browser internet, passare a https://adminportal.local.azurestack.external.
2. Accedere al portale di amministrazione di Azure Stack come un operatore cloud (utilizzando le credenziali specificate durante la distribuzione)
3. Nel dashboard predefinito: trovare il **Gestione area** elenco e fare clic sull'area in cui si desidera esplorare, ad esempio **(locale**).
4. Selezionare **archiviazione** dal **i provider di risorse** elenco.
5. Fare clic su **impostazioni** nella parte superiore per aprire il pannello di impostazione.
6. Fare clic su **configurazione** quindi modificare il valore del periodo di memorizzazione.

   Imposta il numero di giorni e quindi salvarlo.
   
   Questo valore è immediatamente effettivo ed è impostato per l'intera area.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperare la capacità
Uno degli effetti collaterali del periodo di memorizzazione è che un account eliminato continua a consumare capacità fino a quando non si tratta di fuori del periodo di memorizzazione. Come operatore cloud potrebbe essere necessario un modo per recuperare lo spazio di account eliminato, anche se il periodo di memorizzazione non è ancora scaduto.

È possibile recuperare capacità tramite il portale o PowerShell.

**Per recuperare la capacità di utilizzo del portale:**
1. Passare al pannello degli account di archiviazione. Vedere [trovare un account di archiviazione](#find).
2. Fare clic su **recuperare spazio** nella parte superiore del pannello.
3. Leggere il messaggio e quindi fare clic su **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Attendere per vedere l'icona di campanello nel portale di notifica di esito positivo.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aggiornare la pagina account di archiviazione. Gli account eliminati non vengono più visualizzati nell'elenco perché sono stati eliminati.

È possibile inoltre utilizzare PowerShell per eseguire l'override in modo esplicito il periodo di memorizzazione e recuperare immediatamente la capacità.

**Per recuperare la capacità con PowerShell:**   

1. Verificare di disporre di Azure PowerShell installato e configurato. In caso contrario, utilizzare le istruzioni seguenti: 
   * Per installare la versione più recente di Azure PowerShell e associarlo a una sottoscrizione di Azure, vedere [come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Per ulteriori informazioni sui cmdlet di gestione risorse di Azure, vedere [tramite Azure PowerShell con Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Eseguire il cmdlet seguente:

> [!NOTE]
> Se si esegue questo cmdlet eliminare definitivamente l'account e il relativo contenuto. e non è più recuperabile. Utilizzarlo con cautela.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Per ulteriori informazioni, vedere [documentazione di powershell Azure Stack.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Eseguire la migrazione di un contenitore
A causa di archiviazione non uniformi utilizzato dai tenant, un operatore di cloud può trovare una o più sottostante tenant condivisioni con più spazio rispetto ad altri utenti. In questo caso, l'operatore cloud possibile tentare di liberare spazio nella condivisione accentata eseguendo manualmente la migrazione di alcuni contenitori di blob in un'altra condivisione. 

È necessario utilizzare PowerShell per eseguire la migrazione di contenitori.
> [!NOTE]
>Non supporta la migrazione in tempo reale la migrazione di contenitore di BLOB e attualmente è un'operazione offline. Durante la migrazione e fino al completamento dei BLOB presenti nel contenitore sottostanti non è possibile usare sono "offline". 

**Per eseguire la migrazione di contenitori con PowerShell:**

1. Verificare di disporre di Azure PowerShell installato e configurato. In caso contrario, utilizzare le istruzioni seguenti:
    * Per installare la versione più recente di Azure PowerShell e associarlo a una sottoscrizione di Azure, vedere [come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Per ulteriori informazioni sui cmdlet di gestione risorse di Azure, vedere [tramite Azure PowerShell con Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Ottenere il nome della farm: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Ottenere le condivisioni: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Ottenere i contenitori per una determinata condivisione. Si noti che conteggio e delle relative finalità parametri facoltativi:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Esaminare quindi $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Ottenere le migliori condivisioni di destinazione per la migrazione del contenitore:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Esaminare quindi $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Avviare la migrazione di un contenitore, questa è un'implementazione asincrona, in modo sia possibile ciclo in una condivisione di tutti i contenitori e tenere traccia dello stato utilizzando l'id di processo restituito.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Esaminare quindi $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Controllare lo stato del processo di migrazione dall'id processo. Al termine della migrazione di contenitore, MigrationStatus è impostato su "Completato".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. È possibile annullare un processo di migrazione in corso. Questo nuovo è un'operazione asincrona e può essere rilevato tramite $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    È possibile controllare nuovamente lo stato dell'operazione di annullamento della migrazione:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  