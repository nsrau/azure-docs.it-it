---
title: Gestire gli account di archiviazione di Azure Stack | Microsoft Docs
description: Informazioni su come trovare, gestire, ripristinare e recuperare gli account di archiviazione di Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 34e49060a28869015f067f058b81239ec13eff47
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226401"
---
# <a name="manage-azure-stack-storage-accounts"></a>Gestire gli account di archiviazione di Azure Stack

Informazioni su come gestire gli account di archiviazione di Azure Stack. Trovare, ripristinare e recuperare la capacità di archiviazione in base alle esigenze aziendali.

## <a name="find-a-storage-account"></a>Trovare un account di archiviazione

L'elenco degli account di archiviazione nell'area può essere visualizzato in Azure Stack da:

1. Accedi per il [del portale di amministrazione](https://adminportal.local.azurestack.external).

2. Selezionare **tutti i servizi** > **archiviazione** > **gli account di archiviazione**.

   ![Account di archiviazione di Azure Stack](media/azure-stack-manage-storage-accounts/image4.png)

Per impostazione predefinita, vengono visualizzati i primi 10 account. È possibile scegliere di recuperare informazioni facendo il **carica altro** collegamento nella parte inferiore dell'elenco.

Oppure

Se si è interessati a un account di archiviazione specifico è possibile **filtrare e recuperare l'account pertinente** solo.


**Per filtrare per gli account:**

1. Selezionare **filtro** nella parte superiore del riquadro.
2. Nel riquadro di filtro consente di specificare **nome dell'account**, **ID sottoscrizione**, o **stato** per ottimizzare l'elenco degli account di archiviazione da visualizzare. Usarli come appropriato.
3. Durante la digitazione, l'elenco viene automaticamente applicato il filtro.  .
   
    ![Filtrare gli account di archiviazione di Azure Stack](media/azure-stack-manage-storage-accounts/image5.png)

4. Per reimpostare il filtro: selezionare **filtro**, cancellare le selezioni e aggiornare.

La casella di testo di ricerca (nella parte superiore di riquadro dell'elenco di account di archiviazione) consente di evidenziare il testo selezionato nell'elenco degli account. Quando il nome completo o l'ID non è facilmente disponibile, è possibile usare questo.

È possibile usare testo libero qui per consentire di individuare l'account che si è interessati.

![Trovare gli account di archiviazione di Azure Stack](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Esaminare i dettagli dell'account
Dopo avere individuato l'account che si è interessati a visualizzare, è possibile selezionare l'account specifico per visualizzare determinate informazioni. Apre un nuovo riquadro con i dettagli dell'account, ad esempio: il tipo di account, ora di creazione, località, ecc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Ripristinare un account eliminato
Potrebbe essere in una situazione in cui è necessario ripristinare un account eliminato.

In Azure Stack è un modo semplice per farlo:

1. Passare all'elenco degli account di archiviazione. Trova un account di archiviazione in questo articolo per altre informazioni, vedere.
2. Individuare quel particolare account nell'elenco. Potrebbe essere necessario filtrare.
3. Verificare i *stato* dell'account. Dovrebbe risultare **Deleted**.
4. Selezionare l'account, che consente di aprire il riquadro dei dettagli account.
5. Nella parte superiore di questo riquadro, individuare il **ripristinare** pulsante e selezionarlo.
6. Selezionare **Sì** per confermare.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. È ora nel ripristino *processo... Attendere* per un valore che indica che sia stato eseguito correttamente.
   È anche possibile selezionare l'icona "a forma di campana" nella parte superiore del portale per visualizzare le indicazioni di stato di avanzamento.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Una volta che l'account ripristinato correttamente è sincronizzato, può essere utilizzato anche in questo caso.

### <a name="some-gotchas"></a>Problematiche
* L'account eliminato Mostra lo stato della **all'esterno di conservazione**.
  
  All'esterno di conservazione indica che l'account eliminato ha superato il periodo di conservazione e potrebbe non essere ripristinabile.
* L'account eliminato non viene visualizzato nell'elenco degli account.
  
  L'account in uso potrebbe non essere visualizzato nell'elenco degli account quando l'account eliminato è già stato sottoposto a garbage collection. In questo caso, non può essere recuperato. Visualizzare [recuperare la capacità](#reclaim) in questo articolo.

## <a name="set-the-retention-period"></a>Impostare il periodo di conservazione
L'impostazione del periodo di conservazione consente a un operatore cloud specificare un periodo di tempo in giorni (tra 0 e 9999 giorni) durante il quale possono essere ripristinati potenzialmente qualsiasi account eliminato. Periodo di memorizzazione predefinito è impostato su 0 giorni. Impostazione del valore su "0" indica che qualsiasi account eliminato immediatamente all'esterno di conservazione sia contrassegnata per periodicamente operazioni di garbage collection.

**Per modificare il periodo di conservazione:**

1. Accedi per il [del portale di amministrazione](https://adminportal.local.azurestack.external).
2. Selezionare **tutti i servizi** > **gestione delle aree** sotto **amministrazione**.
3. Selezionare **provider di risorse** > **archiviazione** > **impostazioni**. Il percorso è Home > *regione* -provider di risorse > archiviazione.
4. Selezionare **configurazione** quindi modificare il valore del periodo di conservazione.

   Impostare il numero di giorni e quindi salvare il file.
   
   Questo valore viene applicato immediatamente e viene impostato per l'intera area.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Recuperare la capacità
Uno degli effetti collaterali di avere un periodo di conservazione è che un account eliminato continua a consumare capacità fino a quando non esce dal periodo di conservazione. Come operatore cloud potrebbe essere necessario un modo per recuperare lo spazio di account eliminato, anche se il periodo di conservazione non è ancora scaduto.

È possibile recuperare la capacità usando il portale o PowerShell.

**Per recuperare la capacità usando il portale:**
1. Passare al riquadro account di archiviazione. Consultare la sezione trovare un account di archiviazione.
2. Selezionare **recuperare spazio** nella parte superiore del riquadro.
3. Leggere il messaggio e quindi selezionare **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Attendere per vedere l'icona a forma di campanello nel portale di notifica di esito positivo.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aggiornare la pagina account di archiviazione. Gli account eliminati non vengono più visualizzati nell'elenco perché sono stati eliminati.

È anche possibile usare PowerShell per eseguire l'override in modo esplicito il periodo di conservazione e recuperare immediatamente la capacità.

**Per recuperare la capacità usando PowerShell:**   

1. Verificare di avere installato e configurato Azure PowerShell. In caso contrario, seguire le istruzioni seguenti: 
   * Per installare la versione di Azure PowerShell più recente e associarla alla sottoscrizione di Azure, vedere [come installare e configurare Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Per altre informazioni sui cmdlet di Azure Resource Manager, vedere [usando Azure PowerShell con Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Eseguire i cmdlet seguenti:

> [!NOTE]  
> Se si eseguono questi cmdlet, è eliminare definitivamente l'account e il relativo contenuto. e non è più recuperabile. Utilizzarlo con cautela.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Per altre informazioni, vedere [documentazione di PowerShell per Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Passaggi successivi

 - Per informazioni sulla gestione delle autorizzazioni, vedere [controllo di accesso di accessi](azure-stack-manage-permissions.md).
 - Per informazioni sulla capacità di archiviazione di gestione per Azure Stack, vedere [gestire la capacità di archiviazione per Azure Stack](azure-stack-manage-storage-shares.md).
