---
title: Caricare le macchine virtuali Azure Stack in Sentinel di Azure | Microsoft Docs
description: Questo articolo illustra come eseguire il provisioning dell'estensione macchina virtuale di monitoraggio, aggiornamento e gestione della configurazione di Azure in Azure Stack macchine virtuali e iniziare a monitorarli con Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: rkarlin
ms.openlocfilehash: caaf708b3efd8ffbe059f8ad249b7945d31fd7cc
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601164"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Connetti Azure Stack macchine virtuali ad Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Con Azure Sentinel è possibile monitorare le macchine virtuali in esecuzione in Azure e Azure Stack in un'unica posizione. Per caricare i computer Azure Stack in Sentinel di Azure, è prima di tutto necessario aggiungere l'estensione della macchina virtuale alle macchine virtuali Azure Stack esistenti. 

Dopo la connessione Azure Stack computer, scegliere da una raccolta di dashboard che emergono informazioni dettagliate in base ai dati. Questi dashboard possono essere facilmente personalizzati in base alle esigenze.



## <a name="add-the-virtual-machine-extension"></a>Aggiungere l'estensione della macchina virtuale 

Aggiungere l'estensione di macchina virtuale di **monitoraggio, aggiornamento e gestione della configurazione di Azure** alle macchine virtuali in esecuzione nel Azure stack. 

1. In una nuova scheda del browser accedere al [portale di Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Passare alla pagina **macchine virtuali** , selezionare la macchina virtuale che si vuole proteggere con Azure Sentinel. Per informazioni su come creare una macchina virtuale in Azure Stack, vedere [creare una VM Windows Server con il portale di Azure stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) o [creare una VM Server Linux usando il portale di Azure stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
4. Fare clic sulla scheda **Add** (Aggiungi). Viene visualizzato il pannello del menu **Nuova risorsa** che contiene un elenco delle estensioni macchina virtuale disponibili. 
5. Selezionare l'estensione **monitoraggio di Azure, aggiornamento e gestione della configurazione** e fare clic su **Crea**. Viene visualizzata la finestra di configurazione dell' **estensione di installazione** .

   ![Impostazioni di gestione di monitoraggio, aggiornamento e configurazione di Azure](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se il monitoraggio di Azure, l'estensione di **Gestione aggiornamenti e configurazione** non è elencato nel Marketplace, contattare l'operatore Azure stack per renderlo disponibile.

6. Nel menu Azure Sentinel selezionare impostazioni dell' **area di lavoro** , quindi **Avanzate**e copiare l' **ID** dell'area di lavoro e la chiave dell' **area di lavoro (chiave primaria)** . 
1. Nella finestra Azure Stack **Installa estensione** , incollarli nei campi indicati e fare clic su **OK**.
1. Al termine dell'installazione dell'estensione, il relativo stato viene visualizzato come provisioning **riuscito**. La visualizzazione della macchina virtuale nel portale di Azure Sentinel potrebbe richiedere fino a un'ora.

Per ulteriori informazioni sull'installazione e la configurazione dell'agente per Windows, vedere [connettere computer Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Per risolvere i problemi relativi agli agenti Linux, vedere [Risoluzione dei problemi relativi all'agente Linux di Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Nel portale di Azure Sentinel in Azure, in **macchine virtuali**, è presente una panoramica di tutte le macchine virtuali e dei computer insieme al relativo stato. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non serve più, è possibile rimuovere l'estensione dalla macchina virtuale tramite il portale di Azure Stack.

Per rimuovere l'estensione:

1. Aprire il **portale di Azure Stack**.
2. Passare alla pagina **Macchine virtuali** e selezionare la macchina virtuale da cui si vuole rimuovere l'estensione.
3. Selezionare **Estensioni** e quindi l'estensione **Microsoft.EnterpriseCloud.Monitoring**.
4. Fare clic su **Disinstalla**e confermare la selezione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats.md).
- Trasmettere i dati dalle [Appliance comuni in formato errore](connect-common-event-format.md) in Sentinel di Azure.
