---
title: 'Esercitazione: Configurare le distribuzioni canary per le macchine virtuali Linux di Azure'
description: Questa esercitazione illustra come configurare una pipeline di recapito continuo (CD) che aggiorna un gruppo di macchine virtuali di Azure usando la strategia di distribuzione blu-verde
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120549"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Esercitazione: Configurare la strategia di distribuzione blu-verde per le macchine virtuali Linux di Azure


## <a name="iaas---configure-cicd"></a>IaaS - Configurare CI/CD 
Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di recapito continuo per una macchina virtuale di Azure direttamente dal portale di Azure. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD che usa la strategia blu-verde per eseguire distribuzioni in più macchine virtuali. È anche possibile esaminare altre strategie, ad esempio [in sequenza](https://aka.ms/AA7jlh8) e [canary](https://aka.ms/AA7jdrz), che sono supportate per impostazione predefinita dal portale di Azure. 

 
 **Configurare CI/CD su macchine virtuali**

Le macchine virtuali possono essere aggiunte come destinazioni in un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) per la distribuzione degli aggiornamenti in più macchine virtuali. Dopo la distribuzione, la **cronologia di distribuzione** all'interno del gruppo di distribuzione fornisce la tracciabilità dalla macchina virtuale alla pipeline e quindi al commit. 
 
  
**Distribuzioni di tipo blu-verde**: una distribuzione di tipo blu-verde riduce il tempo di inattività con un ambiente di standby identico. Uno degli ambienti è attivo in qualsiasi momento. Mentre si prepara una nuova versione, si esegue la fase finale di test nell'ambiente verde. Una volta che il software è in esecuzione nell'ambiente verde, commutare il traffico in modo che tutte le richieste in ingresso vengano indirizzate all'ambiente verde. L'ambiente blu è ora inattivo.
È possibile configurare le distribuzioni di tipo blu-verde nelle proprie "**macchine virtuali**" tramite il portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata.

1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Nel riquadro sinistro della macchina virtuale passare a **Recapito continuo**. Fare clic su **Configura**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. Nel pannello di configurazione fare clic su **Organizzazione di Azure DevOps** per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui si vuole configurare la pipeline.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Un gruppo di distribuzione è un set logico di computer di destinazione della distribuzione che rappresentano gli ambienti fisici, ad esempio "Dev", "Test", "UAT" e "Production". È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente. 
5. Selezionare la pipeline di compilazione che pubblica il pacchetto da distribuire nella macchina virtuale. Si noti che il pacchetto pubblicato deve includere uno script di distribuzione _deploy.ps1_ o _deploy.sh_ nella cartella `deployscripts` nella radice del pacchetto. Questo script di distribuzione verrà eseguito dalla pipeline Azure DevOps in fase di esecuzione.
6. Selezionare la strategia di distribuzione desiderata. Selezionare **blu-verde**.
7. Aggiungere il tag "blu" o "verde" alle VM da includere nelle distribuzioni di tipo blu-verde. Se la macchina virtuale è destinata a un ruolo di standby, è necessario assegnarle il tag "verde", in caso contrario, contrassegnarla come "blu".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. Fare clic su **OK** per configurare la pipeline di recapito continuo. A questo punto, è disponibile una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Fare clic su **Modifica** per la pipeline di versione in Azure DevOps per visualizzare la configurazione della pipeline. La pipeline è costituita da tre fasi. La prima riguarda il gruppo di distribuzione ed esegue la distribuzione nelle macchine virtuali con tag _verde_ (macchine virtuali standby). La seconda fase sospende la pipeline e attende l'intervento manuale per riprendere l'esecuzione. Quando l'utente è soddisfatto della stabilità della distribuzione, può reindirizzare il traffico alle macchine virtuali con tag _verde_ e riprendere l'esecuzione della pipeline, che scambierà quindi i tag _blu_ e _verde_ nelle macchine virtuali. In questo modo, le VM con la versione meno recente dell'applicazione saranno contrassegnate con il tag _verde_ e alla successiva esecuzione della pipeline verrà eseguita la distribuzione in tali VM.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. Per impostazione predefinita, l'attività Execute Deploy Script (Esegui script di distribuzione) eseguirà lo script di distribuzione _deploy.ps1_ o _deploy.sh_ nella cartella `deployscripts` nella directory radice del pacchetto pubblicato. Assicurarsi che la pipeline di compilazione selezionata esegua la pubblicazione nella cartella radice del pacchetto.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>Altre strategie di distribuzione
- [Configurare la strategia di distribuzione in sequenza](https://aka.ms/AA7jlh8)
- [Configurare la strategia di distribuzione canary](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Progetto DevOps di Azure 
Imparare a usare Azure in modo efficace è più facile che mai.
 
Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure: selezionare un linguaggio dell'applicazione, un runtime e un servizio di Azure.
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Risorse aggiuntive 
- [Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
