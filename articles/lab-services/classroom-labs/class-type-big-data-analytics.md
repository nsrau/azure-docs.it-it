---
title: Configurare un lab per insegnare l'analisi dei Big Data usando Azure Lab Services. Documenti Microsoft
description: Scopri come configurare un laboratorio per insegnare l'analisi dei Big Data utilizzando la distribuzione Docker di Hortonworks Data Platform (HDP).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538778"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Configurare un lab per l'analisi dei Big Data utilizzando la distribuzione Docker della piattaforma dati HortonWorksSet up a lab to big data analytics using Docker deployment of HortonWorks Data Platform

Questo articolo illustra come configurare un lab per insegnare un corso di analisi dei Big Data.This article shows you how to set up a lab to teach a big data analytics class.  Con questo tipo di classe, gli studenti imparano a gestire grandi volumi di dati e applicare algoritmi di apprendimento statistico e macchina per ricavare informazioni dettagliate sui dati.  Un obiettivo chiave per gli studenti è quello di imparare a utilizzare strumenti di analisi dei dati, come il pacchetto software open source di [Apache Hadoop](https://hadoop.apache.org/) che fornisce strumenti per l'archiviazione, la gestione e l'elaborazione dei Big Data.

In questo laboratorio, gli studenti utilizzeranno una popolare versione commerciale di Hadoop fornita da [Cloudera](https://www.cloudera.com/), chiamata [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  In particolare, gli studenti utilizzeranno [HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) una versione semplificata e facile da usare della piattaforma, priva di costi e destinata all'apprendimento e alla sperimentazione.  Anche se questa classe può utilizzare macchine virtuali Windows o Linux (VM) con la sandbox HDP distribuita, in questo articolo verrà illustrato come utilizzare Windows.

Un altro aspetto interessante di questo lab è che distribuiremo la sandbox HDP nelle macchine virtuali lab usando i contenitori [Docker.Another](https://www.docker.com/) interesting aspect of this lab, is that we will deploy HDP Sandbox on the lab VMs using Docker containers.  Ogni contenitore Docker fornisce il proprio ambiente isolato per l'esecuzione di applicazioni software all'interno.  Concettualmente, i contenitori Docker sono come macchine virtuali annidate e possono essere utilizzati per distribuire ed eseguire facilmente un'ampia gamma di applicazioni software basate su immagini contenitore fornite in [Docker Hub](https://www.docker.com/products/docker-hub).  Lo script di distribuzione di Cloudera per HDP Sandbox estrae automaticamente [l'immagine Docker HDP Sandbox 3.0.1](https://hub.docker.com/r/hortonworks/sandbox-hdp) da Docker Hub ed esegue due contenitori Docker:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Configurazione del lab

Per configurare questo lab, sono necessari una sottoscrizione di Azure e un account lab per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account lab in Azure Lab Services.Once you get an Azure subscription, you can create a new lab account in Azure Lab Services. Per ulteriori informazioni sulla creazione di un nuovo account lab, vedere [Esercitazione sull'installazione](tutorial-setup-lab-account.md)di un account lab .  È inoltre possibile utilizzare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account lab

Abilitare le impostazioni descritte nella tabella seguente per l'account lab. Per ulteriori informazioni su come abilitare le immagini del marketplace, consultate Specificare le immagini del [Marketplace disponibili per i creatori di lab.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine di Windows 10 Pro per l'uso all'interno dell'account lab.|

### <a name="lab-settings"></a>Impostazioni lab

Utilizza le impostazioni nella tabella seguente quando configuri un laboratorio di classe.  Per ulteriori informazioni su come creare un lab della classe, vedere [Configurare un'esercitazione](tutorial-setup-classroom-lab.md)del laboratorio in classe .

| Impostazioni lab | Valore/istruzioni |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| Medio (virtualizzazione annidata). Questa dimensione della macchina virtuale è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi.  Questa dimensione supporta anche la virtualizzazione annidata.|  
|Immagine di macchina virtuale| Windows 10 Pro|

> [!NOTE] 
> Abbiamo bisogno di usare Medium (Nested Virtualization) poiché la distribuzione HDP Sandbox utilizzando Docker richiede:
>   - Windows Hyper-V con virtualizzazione annidata
>   - Almeno 10 GB di RAM

## <a name="template-machine-configuration"></a>Configurazione macchina modello

Per impostare la macchina modello, ci sarà:
- Installare Docker
- Distribuire la sandbox HDP
- Usare PowerShell e l'Utilità di pianificazione di Windows per avviare automaticamente i contenitori DockerUse PowerShell and Windows Task Scheduler to automatically start the Docker containers

### <a name="install-docker"></a>Installare Docker

I passaggi descritti in questa sezione sono basati sulle istruzioni di Cloudera per la [distribuzione con i contenitori Docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Per usare i contenitori Docker, è innanzitutto necessario installare Docker Desktop nella macchina virtuale modello:To use Docker containers, you must first install Docker Desktop on the template VM:

1. Seguire i passaggi nella [sezione Prerequisiti](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) per installare [Docker per Windows](https://docs.docker.com/docker-for-windows/install/). 

    > [!IMPORTANT] 
    > Verificare che l'opzione di configurazione **Usa contenitori Windows anziché contenitori Linux** sia deselezionata.

1. Verificare che **i contenitori di Windows e le funzionalità Hyper-V** siano attivati.
   ![Attiva o disattiva funzionalità di Windows](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Seguire i passaggi nella sezione [Memoria per Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) per configurare la configurazione della memoria di Docker.

    > [!WARNING]
    > Se si seleziona inavvertitamente l'opzione **Usa contenitori Windows anziché contenitori Linux durante** l'installazione di Docker, le impostazioni di configurazione della memoria non verranno visualizzate.  Per risolvere questo problema, è possibile passare all'utilizzo di contenitori Linux [facendo clic sull'icona Docker nella barra delle applicazioni](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)di Windows ; quando si apre il menu Docker Desktop, selezionare **Passa a contenitori Linux**.
 
### <a name="deploy-hdp-sandbox"></a>Distribuire la sandbox HDP

In questa sezione, si distribuirà HDP Sandbox e quindi accedere anche HDP Sandbox utilizzando il browser.

1. Assicurarsi di aver installato [Git Bash](https://gitforwindows.org/) come elencato nella [sezione Prerequisiti](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) della guida poiché questa operazione è consigliata per completare i passaggi successivi.

1. Utilizzando la Guida alla [distribuzione e all'installazione di Cloudera per Docker,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)completare i passaggi descritti nelle sezioni seguenti:
   
   -    Distribuire la sandbox HDP
   -    Verificare la sandbox HDP

    > [!WARNING] 
    > Quando si scarica il file .zip più recente per HDP, assicurarsi di *non* salvare il file .zip in un percorso di directory che include spazi vuoti.

    > [!NOTE] 
    > Se si riceve un'eccezione durante la distribuzione che indica **che Drive non è stato condiviso,** è necessario condividere l'unità C con Docker in modo che i contenitori Linux di HDP possano accedere ai file Windows locali.  Per risolvere il problema, [fare clic sull'icona Docker nella barra delle applicazioni](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) di Windows per aprire il menu Docker Desktop e selezionare **Impostazioni**.  Quando si apre la finestra di dialogo **Impostazioni di Docker,** selezionare **Risorse > Condivisione file** e controllare l'unità **C.**  È quindi possibile ripetere i passaggi per distribuire HDP Sandbox.

1. Una volta distribuiti e in esecuzione i contenitori Docker per HDP Sandbox, è possibile accedere all'ambiente avviando il browser e seguendo le istruzioni di Cloudera per aprire la pagina di benvenuto della [sandbox](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) e avviare il dashboard HDP.

    > [!NOTE] 
    > Queste istruzioni presuppongono che sia stato prima eseguito il mapping dell'indirizzo IP locale dell'ambiente sandbox all'sandbox-hdp.hortonworks.com nel file host nella macchina virtuale modello.  Se **non** si esegue questo mapping, è possibile accedere [http://localhost:8080](http://localhost:8080)alla pagina di benvenuto della Sandbox passando a .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Avvia automaticamente i contenitori Docker quando gli studenti accedono

Per offrire un'esperienza facile da usare per gli studenti, useremo uno script di PowerShell che automaticamente:To provide an easy to use experience for students, we'll use a PowerShell script that automatically:
  - Avvia i contenitori Docker sandbox HDP all'avvio di uno studente e si connette alla macchina virtuale lab.
  - Avvia il browser e passa alla pagina di benvenuto del Sandbox.
Useremo anche l'Utilità di pianificazione di Windows per eseguire automaticamente questo script quando uno studente accede alla propria macchina virtuale.
Per configurare questa impostazione, attenersi alla seguente procedura: [Scripting di Big Data Analytics](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Preventivo

Se si desidera stimare il costo di questo lab, è possibile utilizzare l'esempio seguente.

Per una classe di 25 studenti con 20 ore di tempo programmato e 10 ore di quota per compiti a casa o compiti, il prezzo per il laboratorio sarebbe:
  - 25 studenti : 20 ore 10 : 55 unità di laboratorio , 0,01 USD all'ora, 412,50 USD

Ulteriori ulteriori dettagli sui prezzi, vedere Prezzi di [Servizi di laboratorio di Azure](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusioni

Questo articolo illustra i passaggi necessari per creare un lab per una classe di analisi dei Big Data che usa Hortonworks Data Platform distribuita con Docker.  La configurazione per questo tipo di classe può essere utilizzata per classi di analisi dei dati simili.  Questa configurazione può essere applicabile anche ad altri tipi di classi che utilizzano Docker per la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni alla configurazione di qualsiasi lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungi utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Imposta quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Link di registrazione via e-mail agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)
