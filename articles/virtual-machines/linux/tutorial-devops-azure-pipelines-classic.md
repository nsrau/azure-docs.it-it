---
title: 'Esercitazione: DevOps integrato per IaaS e PaaS in Azure'
description: Questa esercitazione illustra come configurare l'integrazione continua (CI) e la distribuzione continua (CD) di un'app in VM di Azure usando le pipeline di Azure.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912529"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Esercitazione: DevOps integrato per IaaS e PaaS in Azure

Le soluzioni end-to-end di Azure permettono ai team di implementare procedure consigliate della metodologia DevOps in ogni fase del ciclo di vita delle applicazioni: pianificazione, sviluppo, distribuzione e utilizzo. 

Di seguito sono illustrati alcuni dei servizi di Azure che semplificano i carichi di lavoro cloud e possono essere combinati per consentire scenari incredibilmente efficaci.
Queste tecnologie, nelle mani delle persone e rafforzate dai processi, consentono ai team di offrire un valore costante ai clienti. 

- Azure: https://portal.azure.com - Portale per la creazione di carichi di lavoro cloud. Consente di gestire e monitorare praticamente tutto, da semplici app Web fino a complesse applicazioni cloud. 
- Azure DevOps: https://dev.azure.com - Offre un insieme di servizi di sviluppo moderni per pianificare in modo più intelligente, migliorare la collaborazione e velocizzare la distribuzione. 
- Azure Machine Learning Studio: https://ml.azure.com - Consente di preparare i dati e di eseguire il training e distribuire i modelli di Machine Learning. 
 

Azure DevOps è un servizio integrato di Azure che automatizza ogni parte del processo di DevOps con l'integrazione continua e il recapito continuo per qualsiasi risorsa di Azure.
Indipendentemente dalle risorse usate dall'app (macchine virtuali, app Web, Kubernetes o altro), è possibile implementare funzionalità quali infrastruttura come codice, integrazione continua, test continuo, recapito continuo e monitoraggio continuo con Azure e Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Configurare CI/CD 
Azure Pipelines offre un set completo di strumenti di automazione CI/CD per le distribuzioni in macchine virtuali. È possibile configurare una pipeline di recapito continuo per una macchina virtuale di Azure direttamente dal portale di Azure. Questo documento contiene i passaggi associati alla configurazione di una pipeline CI/CD per eseguire distribuzioni in più macchine virtuali tramite il portale di Azure. Configurare CI/CD su macchine virtuali.

Le macchine virtuali possono essere aggiunte come destinazioni a un [gruppo di distribuzione](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) e possono essere destinate ad aggiornamenti in sequenza in più computer. Le visualizzazioni della cronologia delle distribuzioni all'interno dei gruppi di distribuzione forniscono la tracciabilità dalla macchina virtuale alla pipeline e quindi al commit. 
 
**Aggiornamenti in sequenza**: una distribuzione in sequenza sostituisce le istanze della versione precedente di un'applicazione con le istanze della nuova versione dell'applicazione in un set fisso di macchine virtuali (set in sequenza) in ogni iterazione. Di seguito viene illustrato in dettaglio come configurare un aggiornamento in sequenza su macchine virtuali.  
È possibile configurare gli aggiornamenti in sequenza nelle proprie "**macchine virtuali**" all'interno del portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale. 
2. Nel riquadro sinistro della VM passare al menu  **Recapito continuo** , quindi fare clic su  **Configura**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. Nel pannello di configurazione fare clic su "Organizzazione di Azure DevOps" per selezionare un account esistente o crearne uno. Selezionare quindi il progetto in cui si vuole configurare la pipeline.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Un gruppo di distribuzione è un set logico di computer di destinazione della distribuzione che rappresentano gli ambienti fisici, ad esempio "Dev", "Test", "UAT" e "Production". È possibile creare un nuovo gruppo di distribuzione o selezionarne uno esistente. Facoltativamente, è possibile contrassegnare il computer con il tag del ruolo, ad esempio "Web", "DB" e così via.  
5. Fare clic su **OK** nella finestra di dialogo per configurare la pipeline di recapito continuo. 
6. Al termine, si avrà una pipeline di recapito continuo configurata per la distribuzione nella macchina virtuale.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Si noterà che la distribuzione nella macchina virtuale è in corso. È possibile fare clic sul collegamento per passare alla pipeline. Fare clic su **Versione-1** per visualizzare la distribuzione. In alternativa, è possibile fare clic su **Modifica** per modificare la definizione della pipeline di versione. 
8. Se occorre configurare più VM, ripetere i passaggi da 2 a 5 per le altre VM da aggiungere al gruppo di distribuzione. 
9. Al termine, fare clic sulla definizione della pipeline, passare all'organizzazione di Azure DevOps e fare clic su **Modifica** per modificare la pipeline di versione. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Fare clic sul collegamento **1 processo, 1 attività** nella fase **sviluppo**. Fare clic sulla fase **Distribuzione**.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. Come si può vedere nel riquadro di configurazione, per impostazione predefinita la pipeline è configurata in modo da eseguire un aggiornamento in sequenza in tutte le destinazioni in parallelo. È possibile configurare le distribuzioni in modo che vengano eseguite una alla volta o in termini di percentuale usando il dispositivo di scorrimento.  
  
  
**Canary** riduce il rischio con una lenta implementazione delle modifiche a un sottoinsieme limitato di utenti. Man mano che si acquisisce maggiore familiarità con la nuova versione, è possibile iniziare a rilasciarla a più server dell'infrastruttura e indirizzarvi più utenti. È possibile configurare le distribuzioni canary nelle proprie "**macchine virtuali**" con il portale di Azure usando l'opzione di recapito continuo. Ecco la procedura dettagliata. 
1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Seguire i passaggi da 2 a 5 descritti nella sezione precedente per aggiungere più macchine virtuali al gruppo di distribuzione. 
3. Aggiungere un tag personalizzato alle VM che devono far parte delle distribuzioni canary, ad esempio "canary".
4. Una volta configurata la pipeline per le VM, fare clic sulla pipeline, avviare l'organizzazione di Azure DevOps, selezionare **Modifica** per modificare la pipeline e passare alla fase di **sviluppo**. Aggiungere un tag al filtro "canary". 
5. Aggiungere un'altra fase del gruppo di distribuzione e configurarla con i tag per fare riferimento alle macchine virtuali rimanenti nel gruppo di distribuzione.  
6. Si può anche configurare un passaggio di convalida manuale che accetti o rifiuti le distribuzioni canary. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

Il tipo **blu-verde** riduce il tempo di inattività della distribuzione con ambienti di standby identici. Uno degli ambienti è attivo in qualsiasi momento. Mentre si prepara una nuova versione, si esegue la fase finale di test nell'ambiente verde. Una volta che il software è in esecuzione nell'ambiente verde, commutare il traffico in modo che tutte le richieste in ingresso vengano indirizzate all'ambiente verde. L'ambiente blu è ora inattivo.
È possibile configurare le distribuzioni di tipo blu-verde nelle proprie "**macchine virtuali**" tramite il portale di Azure usando l'opzione di recapito continuo. 

Ecco la procedura dettagliata. 

1. Accedere al portale di Azure e passare a una macchina virtuale 
2. Seguire i passaggi da 2 a 5 descritti nella sezione **Aggiornamenti in sequenza** per aggiungere più macchine virtuali al gruppo di distribuzione. Aggiungere un tag personalizzato alle VM che devono far parte delle distribuzioni di tipo blu-verde, ad esempio "blu" o "verde" per le macchine virtuali che devono avere il ruolo di standby. 
3. Una volta configurata la pipeline per le VM, fare clic sulla pipeline, avviare l'organizzazione di Azure DevOps, selezionare **Modifica** per modificare la pipeline e quindi passare alla fase di **sviluppo**. Aggiungere un tag al filtro "verde". 
4. Aggiungere una fase senza agente, configurarla con un passaggio di convalida manuale e un passaggio di richiamo dell'API REST per scambiare i tag. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Progetto DevOps di Azure 
Imparare a usare Azure in modo efficace è più facile che mai.
 
Con DevOps Projects bastano tre passaggi per iniziare a eseguire la propria applicazione in qualsiasi servizio di Azure: selezionare un linguaggio dell'applicazione, un runtime e un servizio di Azure.
 
[Altre informazioni](https://azure.microsoft.com/features/devops-projects/ )
 
## <a name="additional-resources"></a>Risorse aggiuntive 
- [Distribuire un'app ASP.NET in Macchine virtuali di Azure usando Azure DevOps Projects](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementare la distribuzione continua dell'app in un set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
