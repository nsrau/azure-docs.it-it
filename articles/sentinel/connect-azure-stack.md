---
title: Eseguire l'onboarding delle macchine virtuali di Azure Stack in Azure Sentinel . Documenti Microsoft
description: Questo articolo illustra come eseguire il provisioning dell'estensione della macchina virtuale Monitoraggio, aggiornamento e gestione configurazione di Azure nelle macchine virtuali di Azure Stack e avviare il monitoraggio con Sentinel.This article shows you how to provision the Azure Monitor, Update, and Configuration Management virtual machine extension on Azure Stack virtual machines and start monitoring them with Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588519"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Connettere le macchine virtuali di Azure Stack ad Azure SentinelConnect Azure Stack virtual machines to Azure Sentinel




Con Azure Sentinel è possibile monitorare le macchine virtuali in esecuzione in Azure e Azure Stack in un'unica posizione. Per eseguire l'onboarding delle macchine di Azure Stack in Azure Sentinel, è innanzitutto necessario aggiungere l'estensione della macchina virtuale alle macchine virtuali di Azure Stack esistenti. 

Dopo aver connesso i computer di Azure Stack, scegliere da una raccolta di dashboard che espongono le informazioni dettagliate in base ai dati. Questi dashboard possono essere facilmente personalizzati in base alle proprie esigenze.



## <a name="add-the-virtual-machine-extension"></a>Aggiungere l'estensione della macchina virtualeAdd the virtual machine extension 

Aggiungere l'estensione della macchina virtuale Monitoraggio, aggiornamento e gestione configurazione di Azure alle macchine virtuali in esecuzione nello stack di Azure.Add the **Azure Monitor, Update,** and Configuration Management virtual machine extension to the virtual machines running on your Azure Stack. 

1. In una nuova scheda del browser accedere al [portale di Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)
2. Passare alla pagina Macchine virtuali e selezionare la macchina virtuale da proteggere con Azure Sentinel.Go to the **Virtual machines** page, select the virtual machine that you want to protectwith with Azure Sentinel. Per informazioni su come creare una macchina virtuale in Azure Stack, vedere Creare una macchina virtuale del server Windows con il portale di Azure Stack o Creare una macchina virtuale del server Linux usando il portale di Azure Stack.For information on how to create a virtual machine on Azure Stack, see Create a [Windows server VM with the Azure Stack portal](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) or Create a Linux server VM by using the Azure Stack [portal.](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
4. Fare clic sulla scheda **Aggiungi.** Viene visualizzato il pannello del menu **Nuova risorsa** e viene visualizzato l'elenco delle estensioni di macchine virtuali disponibili. 
5. Selezionare l'estensione **Monitoraggio di Azure, aggiornamenti e gestione configurazione e** fare clic su **Crea**. Viene visualizzata la finestra di configurazione **dell'estensione di** installazione.

   ![Impostazioni di gestione di Monitoraggio di Azure, aggiornamenti e configurazioneAzure Monitor, Update, and Configuration Management Settings](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se nell'elenco di **Azure Monitor,** dell'estensione Gestione aggiornamenti e configurazione non è presente nell'marketplace, contattare l'operatore di Azure Stack per renderla disponibile.

6. Nel menu Sentinel di Azure selezionare **Impostazioni area di lavoro** seguite da **Avanzate**e copiare l'ID area **di lavoro** e la **chiave dell'area di lavoro (chiave primaria).** 
1. Nella finestra di **estensione Installazione** stack di Azure incollarli nei campi indicati e fare clic su **OK**.
1. Al termine dell'installazione dell'estensione, il relativo stato sarà **Provisioning completato**. La visualizzazione della macchina virtuale nel portale di Azure Sentinel potrebbe richiedere fino a un'ora.

Per ulteriori informazioni sull'installazione e la configurazione dell'agente per Windows, vedere [Connettere computer Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Per risolvere i problemi relativi agli agenti Linux, vedere [Risoluzione dei problemi relativi all'agente Linux di Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Nel portale di Azure Sentinel in Azure, in **Macchine virtuali,** è possibile fare una panoramica di tutte le macchine virtuali e i computer insieme al relativo stato. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non serve più, è possibile rimuovere l'estensione dalla macchina virtuale tramite il portale di Azure Stack.

Per rimuovere l'estensione:

1. Aprire il **portale di Azure Stack**.
2. Passare alla pagina **Macchine virtuali** e selezionare la macchina virtuale da cui si vuole rimuovere l'estensione.
3. Selezionare **Estensioni** e quindi l'estensione **Microsoft.EnterpriseCloud.Monitoring**.
4. Fare clic su **Disinstalla**e confermare la selezione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- Trasmettere i dati da [appliance di Common Error Format](connect-common-event-format.md) in Azure Sentinel.
