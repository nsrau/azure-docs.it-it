---
title: Automatizzare l&quot;installazione del servizio Mobility per Azure Site Recovery con gli strumenti di distribuzione software | Microsoft Docs
description: Questo articolo consente di automatizzare l&quot;installazione del servizio Mobility con strumenti di distribuzione software come System Center Configuration Manager
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: da682645640d74485a1caaff82fd0483a0e3f149
ms.openlocfilehash: 6f10ea05edd3cb5b472b1102946b8da2dd1862d2

---
# <a name="automate-mobility-service-installation-using-software-deployment-tools"></a>Automatizzare l'installazione del servizio Mobility tramite strumenti di distribuzione software

In questo articolo viene fornito un esempio di come è possibile usare System Center Configuration Manager (SCCM) per distribuire il servizio Mobility di Azure Site Recovery nel data center. Usare uno strumento di distribuzione software come SCCM offre i seguenti vantaggi
* Pianificazione di installazioni e aggiornamenti in seguito alla distribuzione, durante la finestra di manutenzione pianificata per gli aggiornamenti software.
* Distribuzione a elevata scalabilità a centinaia di server simultaneamente


> [!NOTE]
> In questo articolo si usa System Center Configuration Manager 2012 R2 per illustrare l'attività di distribuzione. È inoltre possibile automatizzare l'installazione del servizio Mobility tramite [Automazione di Azure e la configurazione dello stato desiderato](site-recovery-automate-mobility-service-install.md).

## <a name="prerequisites"></a>Prerequisiti
1. Uno strumento di distribuzione software come System Center Configuration Manager (SCCM) già stato distribuito nell'ambiente.
  * Creare due [Raccolte dispositivi](https://technet.microsoft.com/library/gg682169.aspx), una per tutti i **server Windows** e una per tutti i **server Linux** da proteggere con Azure Site Recovery.
3. Un server di configurazione già registrato con Azure Site Recovery.
4. Una condivisione file di rete sicura (condivisione SMB) accessibile dal server di SCCM.

## <a name="deploy-mobility-service-on-computers-running-microsoft-windows-operating-systems"></a>Distribuire il servizio Mobility nei computer che eseguono sistemi operativi Microsoft Windows
> [!NOTE]
> In questo articolo si presuppone quanto segue
> 1. L'indirizzo IP del server di configurazione è 192.168.3.121
> 2. La condivisione file di rete sicura è \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Passaggio 1: preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcWindows**
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo
3. Eseguire i comandi seguenti per generare un file passphrase.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copiare il file MobSvc.passphrase nella cartella MobSvcWindows nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copiare **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe** nella cartella **MobSvcWindows** nella condivisione di rete.
7. Copiare il codice riportato di seguito e salvarlo come **install.bat** nella cartella **MobSvcWindows**
> [!NOTE]
> Assicurarsi di sostituire i segnaposto [CSIP] nello script di seguito con i valori effettivi dell'indirizzo IP del server di configurazione.

  [!INCLUDE [site-recovery-sccm-windows-script](../../includes/site-recovery-sccm-windows-script.md)]

### <a name="step-2-create-a-package"></a>Passaggio 2: creare un pacchetto

1. Accedere alla console di System Center Configuration Manager
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**
3. Fare clic con il tasto destro del mouse sui **Pacchetti** e selezionare **Crea pacchetto**
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic sul pulsante **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. Nella pagina **Specificare le informazioni sul programma standard	** indicare gli input seguenti e fare clic su **Avanti**. (gli altri input possono mantenere i valori predefiniti).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties.png)   
| **Nome parametro** | **Valore** |
|--|--|
| Nome | Installare il servizio Mobility di Microsoft Azure (Windows) |
| Riga di comando | install.bat |
| Il programma può essere eseguito | anche se non ci sono utenti connessi |
9. Nella pagina successiva, selezionare i sistemi operativi di destinazione. Il servizio Mobility può essere installato solo su Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2.png)   
10. Fare clic su Avanti due volte per completare la procedura guidata.
> [!NOTE]
> Lo script supporta sia le nuove installazione degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="step-3-deploy-the-package"></a>Passaggio 3: distribuire il pacchetto
1. Nella console di SCCM, fare clic con il tasto destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selezionare i **[Punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Dopo aver completato la procedura guidata, il pacchetto inizia la replica nei punti di distribuzione specificati
4. Una volta completata la distribuzione del pacchetto, fare clic su quest'ultimo con il tasto destro del mouse e selezionare **Distribuisci**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi di Windows Server creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection.png)
6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**
7. Nella pagina **Specify setting to control how this software is deployed** (Specificare l'impostazione per controllare la distribuzione del software) assicurarsi che sia selezionato lo scopo richiesto.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Ulteriori informazioni sulla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx)
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center e completare la procedura guidata.

> [!TIP]
> Per evitare riavvii non necessair, pianificare l'installazione del pacchetto durante la finestra di manutenzione mensile o degli aggiornamenti software.

È possibile monitorare l'avanzamento della distribuzione utilizzando la console di SCCM e aprendo **Monitoraggio** > **Distribuzioni** > *[nome pacchetto]*
  ![monitor-sccm](./media/site-recovery-install-mobility-service-using-sccm/report.PNG)

## <a name="deploy-mobility-service-on-computers-running-linux-operating-systems"></a>Distribuire il servizio Mobility nei computer che eseguono sistemi operativi Linux
> [!NOTE]
> In questo articolo si presuppone quanto segue
> 1. L'indirizzo IP del server di configurazione è 192.168.3.121
> 2. La condivisione file di rete sicura è \\\ContosoSecureFS\MobilityServiceInstallers

### <a name="step-1-prepare-for-deployment"></a>Passaggio 1: preparare la distribuzione
1. Creare una cartella nella condivisione di rete chiamandola **MobSvcLinux**
2. Accedere al server di configurazione e aprire un prompt dei comandi amministrativo
3. Eseguire i comandi seguenti per generare un file passphrase.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
6. Copiare il file MobSvc.passphrase nella cartella MobSvcWindows nella condivisione di rete.
5. Accedere al repository del programma di installazione nel server di configurazione eseguendo il comando.

  `cd %ProgramData%\ASR\home\svsystems\puhsinstallsvc\repository`
6. Copiare i seguenti file nella cartella **MobSvcLinux** nella condivisione di rete
  * Microsoft-ASR\_UA\_*version*\_OEL-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL6-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_RHEL7-64\_GA\_*date*\_Release.tar.gz
  * Microsoft-ASR\_UA\_*version*\_SLES11-SP3-64\_GA\_*date*\_Release.tar.gz

7. Copiare il codice riportato di seguito e salvarlo come **install_linux.sh** nella cartella **MobSvcLinux**
> [!NOTE]
> Assicurarsi di sostituire i segnaposto [CSIP] nello script di seguito con i valori effettivi dell'indirizzo IP del server di configurazione.

  [!INCLUDE [site-recovery-sccm-linux-script](../../includes/site-recovery-sccm-linux-script.md)]

### <a name="step-2-create-a-package"></a>Passaggio 2: creare un pacchetto

1. Accedere alla console di System Center Configuration Manager
2. Passare a **Raccolta software** > **Gestione applicazioni** > **Pacchetti**
3. Fare clic con il tasto destro del mouse sui **Pacchetti** e selezionare **Crea pacchetto**
4. Indicare i valori per nome, descrizione, produttore, lingua e versione.
5. Selezionare la casella di controllo **Questo pacchetto contiene file di origine**.
6. Fare clic sul pulsante **Sfoglia** e selezionare la condivisione di rete in cui è archiviato il programma di installazione (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux)

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/create_sccm_package-linux.png)

7. Nella pagina **Scegliere il tipo di programma da creare** selezionare **Programma standard** e fare clic su **Avanti**

  ![create-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm-standard-program.png)
8. Nella pagina **Specificare le informazioni sul programma standard	** indicare gli input seguenti e fare clic su **Avanti**. (gli altri input possono mantenere i valori predefiniti).

  ![sccm-package-properties](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-linux.png)   
| **Nome parametro** | **Valore** |
|--|--|
| Nome | Installare il servizio Mobility di Microsoft Azure (Linux) |
| Riga di comando | ./install_linux.sh |
| Il programma può essere eseguito | anche se non ci sono utenti connessi |

9. Nella pagina successiva, selezionare **Questo programma può essere eseguito in qualsiasi piattaforma**
  ![sccm-package-properties-page2](./media/site-recovery-install-mobility-service-using-sccm/sccm-program-properties-page2-linux.png)   

10. Fare clic su **Avanti** due volte per completare la procedura guidata.
> [!NOTE]
> Lo script supporta sia le nuove installazione degli agenti del servizio Mobility che l'aggiornamento degli agenti già installati.

### <a name="step-3-deploy-the-package"></a>Passaggio 3: distribuire il pacchetto
1. Nella console di SCCM, fare clic con il tasto destro del mouse sul pacchetto e selezionare **Distribuisci contenuto**
  ![distribute-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_distribute.png)
2. Selezionare i **[Punti di distribuzione](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** in cui copiare i pacchetti.
3. Dopo aver completato la procedura guidata, il pacchetto inizia la replica nei punti di distribuzione specificati.
4. Una volta completata la distribuzione del pacchetto, fare clic su quest'ultimo con il tasto destro del mouse e selezionare **Distribuisci**
  ![deploy-sccm-package](./media/site-recovery-install-mobility-service-using-sccm/sccm_deploy.png)
5. Selezionare la raccolta di dispositivi server Linux creata nella sezione dei prerequisiti come raccolta di destinazione per la distribuzione.

  ![sccm-select-target-collection](./media/site-recovery-install-mobility-service-using-sccm/sccm-select-target-collection-linux.png)
6. Nella pagina **Specificare la destinazione del contenuto** selezionare i **Punti di distribuzione**
7. Nella pagina **Specify setting to control how this software is deployed** (Specificare l'impostazione per controllare la distribuzione del software) assicurarsi che sia selezionato lo scopo richiesto.

  ![sccm-deploy-select-purpose](./media/site-recovery-install-mobility-service-using-sccm/sccm-deploy-select-purpose.png)
8. Specificare una pianificazione nella pagina **Specificare la pianificazione per questa distribuzione**. Ulteriori informazioni sulla [pianificazione dei pacchetti](https://technet.microsoft.com/library/gg682178.aspx)
9. Configurare le proprietà nella pagina **Punti di distribuzione** in base alle necessità del data center e completare la procedura guidata.

Il servizio Mobility viene installato nella raccolta di dispositivi server Linux in base alla pianificazione configurata.

## <a name="other-methods-to-install-mobility-services"></a>Altri metodi per installare i servizi Mobility
Ulteriori informazioni sugli altri modi per installare i servizi Mobility.
* [Installazione manuale tramite interfaccia utente grafica](http://aka.ms/mobsvcmanualinstall)
* [Installazione manuale tramite la riga di comando](http://aka.ms/mobsvcmanualinstallcli)
* [Installazione push tramite il server di configurazione](http://aka.ms/pushinstall)
* [Installazione automatica tramite Automazione di Azure e la configurazione dello stato desiderato](http://aka.ms/mobsvcdscinstall)

## <a name="next-steps"></a>Passaggi successivi
È ora possibile [Abilitare la protezione](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-vmware-to-azure#step-6-replicate-applications) per le macchine virtuali.



<!--HONumber=Jan17_HO2-->


