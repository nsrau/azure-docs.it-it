---
title: 'Esercitazione: Configurare le distribuzioni canary per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di recapito continuo (CD) che aggiorna un gruppo di macchine virtuali Linux di Azure usando la strategia di distribuzione canary
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: b51b4aed85f737e436565ce8ba1ab4a295714734
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120529"
---
# <a name="tutorial---configure-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione canary per le macchine virtuali Linux di Azure


## <a name="iaas---configure-cicd"></a>IaaS - Configurare CI/CD 
Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di recapito continuo per una macchina virtuale di Azure direttamente dal portale di Azure. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD che usa la strategia canary per eseguire distribuzioni in più macchine virtuali. È anche possibile esaminare altre strategie, ad esempio [in sequenza](https://aka.ms/AA7jlh8) e [blu-verde](https://aka.ms/AA83fwu), che sono supportate per impostazione predefinita dal portale di Azure. 


**Configurare CI/CD su macchine virtuali**

Le macchine virtuali possono essere aggiunte come destinazioni in un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) per la distribuzione degli aggiornamenti in più macchine virtuali. Dopo la distribuzione, la **cronologia di distribuzione** all'interno del gruppo di distribuzione fornisce la tracciabilità dalla macchina virtuale alla pipeline e quindi al commit. 
 
  
**Distribuzioni canary**: una distribuzione canary riduce il rischio con una lenta implementazione delle modifiche in un sottoinsieme limitato di utenti. Man mano che si acquisisce maggiore familiarità con la nuova versione, è possibile iniziare a rilasciarla a più server dell'infrastruttura e indirizzarvi più utenti. È possibile configurare le distribuzioni canary nelle macchine virtuali tramite il portale di Azure usando l'opzione di recapito continuo. Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Nel riquadro sinistro della macchina virtuale passare al menu  **Recapito continuo** . Fare clic su **Configure**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Nel pannello di configurazione fare clic su **Organizzazione di Azure DevOps** per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui si vuole configurare la pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un gruppo di distribuzione è un set logico di computer di destinazione della distribuzione che rappresentano gli ambienti fisici, ad esempio "Dev", "Test", "UAT" e "Production". È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente. 
5. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Si noti che il pacchetto pubblicato deve includere uno script di distribuzione _deploy.ps1_ o _deploy.sh_ nella cartella `deployscripts` nella radice del pacchetto. Questo script di distribuzione verrà eseguito dalla pipeline Azure DevOps in fase di esecuzione.
6. Selezionare la strategia di distribuzione desiderata. Selezionare **Canary**.
7. Aggiungere il tag "canary" alle VM da includere nelle distribuzioni canary e il tag "prod" alle VM incluse nelle distribuzioni dopo il completamento della distribuzione canary. I tag consentiranno di indicare come destinazione solo le macchine virtuali con un ruolo specifico.
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

8. Fare clic su **OK** nella finestra di dialogo per configurare la pipeline di recapito continuo. A questo punto, è disponibile una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


9. Fare clic su **Modifica** per la pipeline di versione in Azure DevOps per visualizzare la configurazione della pipeline. La pipeline è costituita da tre fasi. La prima riguarda il gruppo di distribuzione ed esegue la distribuzione nelle macchine virtuali con tag _canary_. La seconda fase sospende la pipeline e attende l'intervento manuale per riprendere l'esecuzione. Quando l'utente è soddisfatto della stabilità della distribuzione canary, può riprendere l'esecuzione della pipeline. Verrà quindi eseguita la terza fase che esegue la distribuzione nelle macchine virtuali con tag _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

10. Prima di riprendere l'esecuzione della pipeline, verificare che almeno una macchina virtuale sia contrassegnata come _prod_. Nella terza fase della pipeline, l'applicazione verrà distribuita solo nelle macchine virtuali con il tag _prod_.

11. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) eseguirà lo script di distribuzione _deploy.ps1_ o _deploy.sh_ che si trova nella cartella 'deployscripts' nella directory radice del pacchetto pubblicato. Assicurarsi che la pipeline di compilazione selezionata esegua la pubblicazione nella cartella radice del pacchetto. 
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione
- [Configurare la strategia di distribuzione in sequenza](https://aka.ms/AA7jlh8)
- [Configurare la strategia di distribuzione blu-verde](https://aka.ms/AA83fwu)

## <a name="azure-devops-project"></a>Progetto DevOps di Azure 
Imparare a usare Azure in modo efficace è più facile che mai.
 
Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure: selezionare un linguaggio dell'applicazione, un runtime e un servizio di Azure.
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Risorse aggiuntive 
- [Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
