---
title: Configurare un Lab per insegnare Big Data Analytics con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per insegnare a Big Data Analytics con la distribuzione di Docker di Hortonworks Data Platform (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538778"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Configurare un Lab per Big Data analisi usando la distribuzione Docker di HortonWorks Data Platform

Questo articolo illustra come configurare un Lab per insegnare una classe Big Data Analytics.  Con questo tipo di classe, gli studenti imparano a gestire volumi elevati di dati e applicano algoritmi di apprendimento statistico e del computer per derivare informazioni dettagliate sui dati.  Uno degli obiettivi principali per gli studenti consiste nell'imparare a usare gli strumenti di analisi dei dati, ad esempio il [pacchetto software open source di Apache Hadoop](https://hadoop.apache.org/) , che fornisce strumenti per l'archiviazione, la gestione e l'elaborazione di Big Data.

In questo laboratorio gli studenti utilizzeranno una versione commerciale comune di Hadoop fornita da [Cloudera](https://www.cloudera.com/), denominata [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html).  In particolare, gli studenti utilizzeranno [HDP sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) , una versione semplificata e facile da usare della piattaforma, disponibile gratuitamente e destinata a apprendimento e sperimentazione.  Sebbene questa classe possa usare macchine virtuali Windows o Linux con HDP sandbox distribuito, in questo articolo viene illustrato come usare Windows.

Un altro aspetto interessante di questo Lab è che la sandbox di HDP verrà distribuita nelle macchine virtuali del Lab usando i contenitori [Docker](https://www.docker.com/) .  Ogni contenitore Docker fornisce il proprio ambiente isolato per l'esecuzione delle applicazioni software.  Concettualmente, i contenitori Docker sono come le VM annidate e possono essere usati per distribuire ed eseguire facilmente un'ampia gamma di applicazioni software basate su immagini del contenitore fornite nell' [Hub Docker](https://www.docker.com/products/docker-hub).  Lo script di distribuzione di Cloudera per HDP sandbox esegue automaticamente il pull dell' [immagine Docker HDP sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) dall'hub Docker ed esegue due contenitori docker:
  - sandbox-HDP
  - sandbox-proxy

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo Lab, è necessario disporre di una sottoscrizione di Azure e di un account Lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account Lab, vedere [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).  È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni descritte nella tabella seguente per l'account Lab. Per altre informazioni su come abilitare le immagini del Marketplace, vedere [specificare le immagini del Marketplace disponibili per gli autori del Lab](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

| Impostazione dell'account Lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine Windows 10 Pro da usare nell'account Lab.|

### <a name="lab-settings"></a>Impostazioni Lab

Usare le impostazioni nella tabella seguente quando si configura un Lab della classe.  Per altre informazioni su come creare un Lab per le aule, vedere [l'esercitazione sulla configurazione di una classe Lab](tutorial-setup-classroom-lab.md).

| Impostazioni Lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| Media (virtualizzazione annidata). Queste dimensioni della macchina virtuale sono ideali per database relazionali, Caching in memoria e analisi.  Questa dimensione supporta anche la virtualizzazione nidificata.|  
|Immagine di macchina virtuale| Windows 10 Pro|

> [!NOTE] 
> È necessario usare media (virtualizzazione annidata) perché la distribuzione di HDP sandbox con Docker richiede:
>   - Windows Hyper-V con virtualizzazione annidata
>   - Almeno 10 GB di RAM

## <a name="template-machine-configuration"></a>Configurazione computer modello

Per configurare il computer modello, è possibile:
- Installare Docker
- Distribuire sandbox HDP
- Usare PowerShell e Windows Utilità di pianificazione per avviare automaticamente i contenitori Docker

### <a name="install-docker"></a>Installare Docker

I passaggi descritti in questa sezione sono basati sulle [istruzioni di Cloudera per la distribuzione con i contenitori Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Per usare i contenitori Docker, è necessario prima installare Docker desktop nella macchina virtuale del modello:

1. Per installare [Docker per Windows](https://docs.docker.com/docker-for-windows/install/), attenersi alla procedura descritta nella [sezione Prerequisiti](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) . 

    > [!IMPORTANT] 
    > Assicurarsi che l'opzione **di configurazione utilizza contenitori Windows anziché Linux** sia deselezionata.

1. Assicurarsi che i **contenitori di Windows e le funzionalità Hyper-V** siano attivati.
   ![Attiva o disattiva funzionalità di Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Seguire i passaggi nella sezione [Memory per Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) per configurare la configurazione della memoria di Docker.

    > [!WARNING]
    > Se si seleziona inavvertitamente l'opzione **Usa contenitori Windows anziché i contenitori Linux** quando si installa Docker, non verranno visualizzate le impostazioni di configurazione della memoria.  Per risolvere questo problema, è possibile passare all'uso di contenitori Linux facendo [clic sull'icona Docker nella barra delle](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)applicazioni di Windows. Quando si apre il menu desktop Docker, selezionare **passa a contenitori Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Distribuire sandbox HDP

In questa sezione si distribuirà HDP sandbox e si accederà anche a HDP sandbox tramite il browser.

1. Assicurarsi di aver installato [git bash](https://gitforwindows.org/) come elencato nella [sezione Prerequisiti](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) della guida, perché questa operazione è consigliata per completare i passaggi successivi.

1. Utilizzando la [Guida alla distribuzione e all'installazione di Cloudera per Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html), completare la procedura descritta nelle sezioni seguenti:
   
   -    Distribuire sandbox HDP
   -    Verifica sandbox HDP

    > [!WARNING] 
    > Quando si Scarica il file zip più recente per HDP, assicurarsi di *non* salvare il file zip in un percorso di directory che includa spazi vuoti.

    > [!NOTE] 
    > Se si riceve un'eccezione durante la distribuzione, l'unità di attestazione **non è stata condivisa**, è necessario condividere l'unità C con Docker in modo che i contenitori Linux di HDP possano accedere ai file di Windows locali.  Per risolvere il problema, [fare clic sull'icona Docker nella barra](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) delle applicazioni di Windows per aprire il menu desktop Docker e selezionare **Impostazioni**.  Quando viene visualizzata la finestra **di dialogo Impostazioni di Docker** , selezionare **risorse > condivisione file** e controllare l'unità **C** .  È quindi possibile ripetere i passaggi per distribuire HDP sandbox.

1. Dopo la distribuzione e l'esecuzione dei contenitori Docker per HDP sandbox, è possibile accedere all'ambiente avviando il browser e seguendo le istruzioni di Cloudera per l'apertura della [pagina iniziale di sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e l'avvio del dashboard HDP.

    > [!NOTE] 
    > Queste istruzioni presuppongono che sia stato innanzitutto eseguito il mapping dell'indirizzo IP locale dell'ambiente sandbox a sandbox-hdp.hortonworks.com nel file host nella macchina virtuale del modello.  Se **non si esegue questo** mapping, è possibile accedere alla pagina iniziale di sandbox passando a. [http://localhost:8080](http://localhost:8080)

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Avvia automaticamente i contenitori Docker al momento dell'accesso degli studenti

Per offrire un'esperienza di facile utilizzo per gli studenti, verrà usato uno script di PowerShell che consente di eseguire automaticamente le operazioni seguenti:
  - Avvia i contenitori Docker sandbox HDP quando uno studente viene avviato e si connette alla VM Lab.
  - Avvia il browser e passa alla pagina iniziale di sandbox.
Si userà anche Windows Utilità di pianificazione per eseguire automaticamente questo script quando uno studente accede alla propria macchina virtuale.
Per eseguire questa operazione, seguire questa procedura: creazione di [script per Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Stima dei costi

Se si desidera stimare il costo di questo Lab, è possibile utilizzare l'esempio seguente.

Per una classe di 25 studenti con 20 ore di tempo di classe pianificata e 10 ore di quota per il lavoro o le assegnazioni, il prezzo del Lab sarà:
  - 25 studenti * (20 + 10) ore * 55 unità Lab * 0,01 USD all'ora = 412,50 USD

Per ulteriori informazioni sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni

Questo articolo illustra i passaggi necessari per creare un Lab per una classe Big Data Analytics che usa la piattaforma dati Hortonworks distribuita con Docker.  L'installazione di questo tipo di classe può essere usata per classi di analisi dei dati simili.  Questa installazione può essere applicata anche ad altri tipi di classi che usano Docker per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
