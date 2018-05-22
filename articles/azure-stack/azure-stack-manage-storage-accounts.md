---
title: Gestire gli account di archiviazione di Azure Stack | Documenti Microsoft
description: Informazioni su come trovare, gestire, ripristinare e recuperare gli account di archiviazione di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Gestire gli account di archiviazione in Azure Stack
Informazioni su come gestire gli account di archiviazione nello Stack di Azure per trovare, ripristinare e recuperare la capacità di archiviazione in base alle esigenze aziendali.

## <a name="find"></a>Trovare un account di archiviazione
L'elenco di account di archiviazione nell'area può essere visualizzato nello Stack di Azure per:

1. In un browser Internet, passare a https://adminportal.local.azurestack.external.
2. Accedere al portale di amministrazione di Azure Stack come un operatore cloud (utilizzando le credenziali specificate durante la distribuzione)
3. Nel dashboard predefinito: trovare il **la gestione delle regioni** elenco e selezionare l'area che si desidera esplorare, ad esempio **(locale**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Selezionare **archiviazione** dal **i provider di risorse** elenco.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. A questo punto, nel riquadro amministrazione Provider di risorse di archiviazione: scorrere verso il basso il **gli account di archiviazione** scheda e selezionarlo.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Nella pagina risultante è l'elenco di account di archiviazione in tale area.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Per impostazione predefinita, vengono visualizzati i primi 10 conti. È possibile scegliere di recuperare informazioni facendo clic di **caricare più** collegamento nella parte inferiore dell'elenco.

Oppure

Se si è interessati a un determinato account di archiviazione, è possibile **filtrare e recuperare gli account rilevanti** solo.


**Per filtrare per gli account:**

1. Selezionare **filtro** nella parte superiore del riquadro.
2. Nel riquadro filtro consente di specificare **nome account**, * * ID sottoscrizione, o **stato** per ottimizzare l'elenco di account di archiviazione da visualizzare. Li utilizzi come appropriato.
3. Selezionare **Aggiorna**. L'elenco è necessario aggiornare di conseguenza.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Per reimpostare il filtro: selezionare **filtro**, cancellare le selezioni e aggiornare.

La casella di testo di ricerca (nella parte superiore nel riquadro elenco gli account di archiviazione) consente di evidenziare il testo selezionato nell'elenco di account. È possibile utilizzare questo quando il nome completo o l'ID non è facilmente disponibile.

È possibile utilizzare qui testo libero per individuare l'account a cui che si è interessati.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Esaminare i dettagli dell'account
Dopo aver individuato gli account a cui che si è interessati a visualizzare, è possibile selezionare l'account specifico per visualizzare determinati dettagli. Apre un nuovo riquadro con i dettagli dell'account, ad esempio: il tipo dell'account, l'ora di creazione, posizione, e così via.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Ripristinare un account eliminato
Potrebbe essere in una situazione in cui è necessario ripristinare un account eliminato.

Nello Stack di Azure è un modo semplice per eseguire questa operazione:

1. Selezionare l'elenco di account di archiviazione. Vedere [trovare un account di archiviazione](#find) in questo argomento per ulteriori informazioni.
2. Individuare l'account specifico nell'elenco. Potrebbe essere necessario filtrare.
3. Controllare il *stato* dell'account. Dovrebbe essere visualizzato **Deleted**.
4. Selezionare l'account, che consente di aprire il riquadro dei dettagli account.
5. Nella parte superiore di questo riquadro, individuare il **ripristinare** pulsante e selezionarlo.
6. Selezionare **Sì** per confermare.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. È ora il ripristino *elaborare... Attendere* per un'indicazione che sia stato eseguito correttamente.
   È anche possibile selezionare l'icona di "controllo del segnale acustico" nella parte superiore del portale per visualizzare le indicazioni di stato di avanzamento.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Una volta che l'account ripristinato correttamente è sincronizzato, può essere usata nuovamente.

### <a name="some-gotchas"></a>Alcuni problemi
* L'account eliminato Mostra lo stato come **fuori memorizzazione**.
  
  Fuori memorizzazione significa che l'account eliminato ha superato il periodo di memorizzazione e potrebbe non essere ripristinabile.
* L'account eliminato non viene visualizzato nell'elenco degli account.
  
  L'account in uso non vengano visualizzate nell'elenco di account quando l'account eliminato è già stato sottoposto a garbage collection. In questo caso, non può essere recuperato. Vedere [recuperare capacità](#reclaim) in questo argomento.

## <a name="set-the-retention-period"></a>Impostare il periodo di memorizzazione
L'impostazione del periodo di conservazione consente un operatore di cloud specificare un periodo di tempo in giorni (compreso tra 0 e 9999 giorni) durante il quale può essere recuperato potenzialmente qualsiasi account eliminato. Periodo di memorizzazione predefinito è impostato su 0 giorni. Impostazione del valore su "0", tutti gli account eliminati immediatamente fuori memorizzazione che sia contrassegnata per periodicamente operazioni di garbage collection.

**Per modificare il periodo di memorizzazione:**

1. In un browser internet, passare a https://adminportal.local.azurestack.external.
2. Accedere al portale di amministrazione di Azure Stack come un operatore cloud (utilizzando le credenziali specificate durante la distribuzione)
3. Nel dashboard predefinito: trovare il **la gestione delle regioni** elenco e selezionare l'area che si desidera esplorare: ad esempio **(locale**).
4. Selezionare **archiviazione** dal **i provider di risorse** elenco.
5. Selezionare **impostazioni** nella parte superiore per aprire il riquadro di impostazione.
6. Selezionare **configurazione** quindi modificare il valore del periodo di memorizzazione.

   Imposta il numero di giorni e quindi salvarlo.
   
   Questo valore è immediatamente effettivo ed è impostato per l'intera area.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperare la capacità
Uno degli effetti collaterali del periodo di memorizzazione è che un account eliminato continua a consumare capacità fino a quando non si tratta di fuori del periodo di memorizzazione. Come operatore cloud potrebbe essere necessario un modo per recuperare lo spazio di account eliminato, anche se il periodo di memorizzazione non è ancora scaduto.

È possibile recuperare capacità tramite il portale o PowerShell.

**Per recuperare la capacità di utilizzo del portale:**
1. Passare al riquadro di account di archiviazione. Vedere [trovare un account di archiviazione](#find).
2. Selezionare **recuperare spazio** nella parte superiore del riquadro.
3. Leggere il messaggio e quindi selezionare **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Attendere per vedere l'icona di campanello nel portale di notifica di esito positivo.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aggiornare la pagina account di archiviazione. Gli account eliminati non vengono più visualizzati nell'elenco perché sono stati eliminati.

È possibile inoltre utilizzare PowerShell per eseguire l'override in modo esplicito il periodo di memorizzazione e recuperare immediatamente la capacità.

**Per recuperare la capacità con PowerShell:**   

1. Verificare di disporre di Azure PowerShell installato e configurato. In caso contrario, utilizzare le istruzioni seguenti: 
   * Per installare la versione più recente di Azure PowerShell e associarlo a una sottoscrizione di Azure, vedere [come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Per ulteriori informazioni sui cmdlet di gestione risorse di Azure, vedere [tramite Azure PowerShell con Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Eseguire i cmdlet seguenti:

> [!NOTE]
> Se si eseguono questi cmdlet, verrà eliminato in modo permanente l'account e il relativo contenuto. e non è più recuperabile. Utilizzarlo con cautela.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Per altre informazioni, vedere [documentazione di Azure PowerShell dello Stack.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>Passaggi successivi

 - Per informazioni sulla gestione delle autorizzazioni, vedere [controllo di accesso Manage Role-Based](azure-stack-manage-permissions.md).
 - Per informazioni su gestire la capacità di archiviazione per lo Stack di Azure, vedere [gestire la capacità di archiviazione per lo Stack di Azure](azure-stack-manage-storage-shares.md).