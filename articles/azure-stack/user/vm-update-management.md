---
title: Macchina virtuale di aggiornamento e la gestione con Azure Stack | Microsoft Docs
description: Informazioni su come usare le soluzioni gestione aggiornamenti, rilevamento modifiche e inventario in automazione di Azure per gestire le macchine virtuali Linux distribuite in Azure Stack e Windows.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 4683b6f63af9fe0081911db9914f04b1c90f9d23
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819446"
---
# <a name="azure-stack-vm-update-and-management"></a>Aggiornamento dello Stack della macchina virtuale e gestione di Azure
È possibile usare le seguenti funzionalità di soluzione di automazione di Azure per gestire le macchine virtuali Linux distribuite con Azure Stack e Windows:

- **[Gestione aggiornamenti](https://docs.microsoft.com/azure/automation/automation-update-management)**. Con la soluzione gestione aggiornamenti, è possibile valutare lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire rapidamente il processo di installazione degli aggiornamenti necessari per queste macchine virtuali di Linux e Windows.

- **[Il rilevamento delle modifiche](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Le modifiche al software installato, i servizi Windows, del Registro di sistema di Windows e i file e ai daemon Linux nei server monitorati vengono inviate al servizio di monitoraggio di Azure nel cloud per l'elaborazione. Viene applicata la logica ai dati ricevuti, quindi questi ultimi vengono registrati nel servizio cloud. Usando le informazioni nel dashboard Change Tracking, è possibile visualizzare facilmente le modifiche apportate all'infrastruttura del server.

- **[Inventory](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. Il monitoraggio dell'inventario per una macchina virtuale di Azure Stack offre un'interfaccia utente basata sul browser per l'installazione e configurazione di raccolta dell'inventario. 

> [!IMPORTANT]
> Queste soluzioni sono uguali a quelli usati per gestire macchine virtuali di Azure. Azure e macchine virtuali di Azure Stack vengono gestite nello stesso modo, dalla stessa interfaccia, usando gli stessi strumenti. Le macchine virtuali di Azure Stack sono inoltre hanno lo stesso prezzo come macchine virtuali di Azure quando si usa la gestione degli aggiornamenti, rilevamento modifiche e inventario soluzioni con Azure Stack.

## <a name="prerequisites"></a>Prerequisiti
Prima di usare queste funzionalità per aggiornare e gestire macchine virtuali di Azure Stack è necessario soddisfare alcuni prerequisiti. Questi includono i passaggi da effettuare nel portale di Azure, nonché nel portale di amministrazione di Azure Stack.

### <a name="in-the-azure-portal"></a>Nel portale di Azure
Per usare l'inventario, rilevamento modifiche e le funzionalità di automazione di Azure di gestione aggiornamenti per le macchine virtuali di Azure Stack, è necessario innanzitutto abilitare queste soluzioni in Azure.

> [!TIP]
> Se hai già queste funzionalità abilitate per le macchine virtuali di Azure, è possibile usare le credenziali dell'area di lavoro di log Analytics già esistente. Se hai già un LogAnalytics WorkspaceID e chiave primaria a cui si desidera utilizzare, passare a [nella sezione successiva](./vm-update-management.md#in-the-azure-stack-administration-portal). In caso contrario, continuare in questa sezione per creare un nuovo account di automazione e l'area di lavoro di log Analytics.

Il primo passaggio dell'abilitazione di queste soluzioni consiste [creare un'area di lavoro di log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) nella sottoscrizione di Azure. Un'area di lavoro di Log Analitica è un ambiente di log univoco di monitoraggio di Azure con il proprio repository dei dati, origini dati e soluzioni. Dopo aver creato un'area di lavoro, tenere presente la chiave e l'ID area di lavoro. Per visualizzare queste informazioni, passare al pannello dell'area di lavoro, fare clic su **impostazioni avanzate**ed esaminare le **ID area di lavoro** e **Primary Key** valori. 

Successivamente, è necessario [creare un Account di automazione](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Un Account di automazione è un contenitore per le risorse di automazione di Azure. Fornisce un modo per separare gli ambienti o organizzare ulteriormente i flussi di lavoro di automazione e le risorse. Dopo aver creato l'account di automazione, è necessario abilitare l'inventario, rilevamento modifiche e aggiornare le funzionalità di gestione. A tale scopo, seguire questi passaggi per abilitare ogni funzionalità:

1. Nel portale di Azure, passare all'Account di automazione da usare.

2. Selezionare la soluzione per abilitare (entrambe **inventario**, **rilevamento modifiche**, o **gestione aggiornamenti**).

3. Usare il **selezionare l'area di lavoro...**  elenco a discesa per selezionare l'area di lavoro di Log Analitica da utilizzare.

4. Verificare che tutte le informazioni rimanenti siano corrette e quindi fare clic su **abilitare** per abilitare la soluzione.

5. Ripetere i passaggi 2-4 per abilitare tutte le tre soluzioni. 

   [![](media/vm-update-management/1-sm.PNG "Abilitare le funzionalità di account di automazione")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>Nel portale di amministrazione di Azure Stack
Dopo aver abilitato le soluzioni di automazione di Azure nel portale di Azure, è quindi necessario accedere al portale di amministrazione di Azure Stack come amministratore del cloud e scaricare il **aggiornamento di Azure e la gestione della configurazione** e il  **Aggiornamento e la gestione della configurazione per Linux Azure** elementi del marketplace Azure Stack estensione. 

   ![Azure Aggiorna e la configurazione Gestione estensione elemento del marketplace](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Abilitare gestione aggiornamenti per le macchine virtuali di Azure Stack
Seguire questi passaggi per abilitare gestione aggiornamenti per le macchine virtuali di Azure Stack.

1. Accedere al portale per gli utenti Azure Stack.

2. In Azure Stack-portale per gli utenti, passare al pannello estensioni delle macchine virtuali per cui si desidera abilitare queste soluzioni, fare clic su **+ Aggiungi**, selezionare la **aggiornamento di Azure e la gestione della configurazione** estensione e fare clic su **Create**:

   [![](media/vm-update-management/3-sm.PNG "Pannello di estensione della macchina virtuale")](media/vm-update-management/3-lg.PNG#lightbox)

3. Fornire l'ID area di lavoro e chiave primaria creata in precedenza per collegare l'agente con l'area di lavoro di log Analytics e fare clic su **OK** per distribuire l'estensione.

   [![](media/vm-update-management/4-sm.PNG "Fornire l'ID area di lavoro e una chiave")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Come descritto nel [documentazione di gestione aggiornamenti di automazione](https://docs.microsoft.com/azure/automation/automation-update-management), è necessario abilitare la soluzione gestione aggiornamenti per ogni macchina virtuale che si desidera gestire. Per abilitare la soluzione per tutte le macchine virtuali creazione di report all'area di lavoro, selezionare **gestione aggiornamenti**, fare clic su **gestire macchine**, quindi selezionare il **Abilita in tutti i computer disponibili e futuri** opzione.

   [![](media/vm-update-management/5-sm.PNG "Fornire l'ID area di lavoro e una chiave")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Ripetere questo passaggio per abilitare ogni soluzione per le VM di Azure Stack report all'area di lavoro. 
  
Dopo l'aggiornamento di Azure e la gestione della configurazione l'estensione è abilitata, viene eseguita un'analisi due volte al giorno per ogni macchina virtuale gestita. L'API viene chiamato ogni 15 minuti per eseguire query per l'ora dell'ultimo aggiornamento determinare se lo stato è stato modificato. Se lo stato è cambiato, viene avviata un'analisi della conformità.

Dopo che le macchine virtuali vengono analizzate, verranno visualizzati nell'account di automazione di Azure nella soluzione di gestione degli aggiornamenti: 

   [![](media/vm-update-management/6-sm.PNG "Fornire l'ID area di lavoro e una chiave")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

Le macchine virtuali di Azure Stack può ora essere inclusi nelle distribuzioni di aggiornamenti pianificate insieme alle macchine virtuali di Azure.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Abilitare gestione aggiornamenti usando un modello di Resource Manager
Se si dispone di un numero elevato di macchine virtuali di Azure Stack, è possibile usare [questo modello di Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) per distribuire più facilmente la soluzione in macchine virtuali. Il modello distribuisce l'estensione Microsoft Monitoring Agent in una VM di Azure Stack esistente e lo aggiunge a un'area di lavoro di log Analytics di Azure esistente.
 
## <a name="next-steps"></a>Passaggi successivi
[Ottimizzare le prestazioni delle VM di SQL Server](azure-stack-sql-server-vm-considerations.md)
