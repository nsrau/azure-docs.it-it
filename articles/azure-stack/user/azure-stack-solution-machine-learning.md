---
title: Creare una bordo soluzione di machine learning con Azure e Azure Stack | Microsoft Docs
description: Informazioni su come creare una soluzione di apprendimento macchina edge Usa Python con Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: c195f2ee24b61a57c098d5214a37f65e80845074
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410205"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Esercitazione: Creare una bordo soluzione di machine learning con Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come creare una bordo soluzione di machine learning con Azure e Azure Stack.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> - Creare un account di archiviazione e un contenitore per la pulizia dati risiedano.
> - Creare un Ubuntu Data Science Virtual Machine (DSVM) nel portale di Azure.
> - Distribuire servizi di Azure Machine Learning in Azure per compilare ed eseguire il training di modelli.
> - Creare account di servizi di Azure Machine Learning.
> - Distribuire e usare registro contenitori di Azure.
> - Distribuire un cluster Kubernetes in Azure Stack.
> - Creare un account di archiviazione di Azure Stack e una coda di archiviazione per i dati.
> - Creare una funzione di nuovo Stack di Azure per spostare i dati puliti dallo Stack di Azure in Azure.

**Quando usare questa soluzione**

 -  L'organizzazione Usa un approccio di DevOps, o uno prevista per il prossimo futuro.
 -  Si desidera implementare procedure di integrazione continua/distribuzione continua dell'implementazione di Azure Stack e nel cloud pubblico.
 -  Si vuole consolidare la pipeline CI/CD per gli ambienti cloud e locali.
 -  Si desidera che la possibilità di sviluppare applicazioni usando servizi cloud o in locale.
 -  Si desidera sfruttare le competenze di sviluppatore coerente tra le applicazioni cloud e locali.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione applicazioni ibride. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

Alcuni dei componenti necessari per compilare questo caso d'uso e potrebbe richiedere alcuni minuti per preparare:

 -  Un Partner OEM/Hardware di Azure possono distribuire un ambiente di produzione Azure Stack e tutti gli utenti possono distribuire un ASDK

 -  Un operatore di Azure Stack deve anche distribuire il servizio App, creare piani e offerte, creare una sottoscrizione tenant e aggiungere l'immagine di Windows Server 2016

 -  Una rete ibrida e il servizio App è necessaria la configurazione (ulteriori informazioni sulle [integrazione di App con reti virtuali.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Privato [compilazione e versione agente](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) per integrazione di Visual Studio Team Services deve essere impostato prima della distribuzione (assicurarsi che tutti i componenti esistenti usati soddisfa i requisiti prima di iniziare).

È necessario conoscere prima di Azure e Azure Stack. Per altre informazioni prima di procedere, iniziare con questi argomenti:

 -  [Introduzione ad Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Concetti chiave di Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Guida alla soluzione di integrazione continua/distribuzione ibrida di Azure Stack](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Una sottoscrizione di Azure (Crea un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  Creando un nuovo URL dell'App Web [App Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) in Azure

 -  Distribuzione di [su Kubernetes in Azure (ACS) di servizi contenitore di Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Distribuzione del servizio di Azure Machine Learning (anteprima) [esercitazione in 4 parti](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Sperimentazione di Azure Machine Learning [account](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Un sistema integrato di Azure Stack o la distribuzione di Azure Stack Development Kit.

    - Trovare le istruzioni per l'installazione di Azure Stack in [installazione di Azure Stack Development Kit](/articles/azure-stack/asdk/asdk-install).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Questa installazione può richiedere alcune ore per il completamento.

 -  Distribuzione di [servizio App](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) servizi PaaS per Azure Stack

 -  Oggetto [piano/offerte](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) all'interno dell'ambiente Azure Stack

 -  Oggetto [sottoscrizione Tenant](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) all'interno dell'ambiente Azure Stack

 -  Un'immagine di macchina virtuale Ubuntu Server (disponibile nel [Marketplace Azure Stack](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) verrà compilata questa macchina virtuale nella sottoscrizione del tenant in Azure Stack come agente di compilazione privata, nonché le macchine virtuali di Kubernetes. Questa immagine non è disponibile, l'operatore di Azure Stack utili nel caso, per garantire che questo viene aggiunto all'ambiente. (Non usare la build 18.04 di ubuntu, perché è attualmente non supportato.)

 -  Un'App Web all'interno della sottoscrizione di tenant (si noti il nuovo URL dell'App Web per un uso successivo).

 -  Distribuzione di basato su Linux di Visual Studio Team Services Private Build agente macchina virtuale, all'interno della sottoscrizione tenant

 -  Distribuzione di un [dei servizi contenitore di Azure (ACS) su Kubernetes in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Strumenti di sviluppo**

 -  [Area di lavoro di Visual Studio Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (il processo di iscrizione viene creato un progetto denominato "MyFirstProject")

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) installazione e [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) sign-on

 -  [Clone locale](https://www.visualstudio.com/docs/git/gitquickstart) del progetto

 -  [Il sottosistema di Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) installazione (per BASH e SSH)

 -  [Docker per Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) installazione

 -  [Azure Machine Learning Workbench (anteprima)](https://aka.ms/azureml-wb-msi) installazione

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) installazione ambiente

**VISUAL STUDIO TEAM SERVICES**

 -  **Account di Visual Studio Team Services.** Configurare rapidamente l'integrazione continua per la compilazione, test e distribuzione. Per altre informazioni sugli account di Visual Studio Team Services, vedere [creare l'Account di Visual Studio Team Services](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **Repository di codice.** Usa i repository di codice esistenti in GitHub, BitBucket, DropBox, Onedrive e TFS, la piattaforma di Visual Studio Team Services può sfruttare più repository di codice per semplificare la pipeline di sviluppo. Per altre informazioni sul codice vedere repository [Introduzione a Git e Visual Studio Team Services](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) esercitazione.

 -  **Connessione del servizio.** Connettersi a servizi esterni e remoti di eseguire attività per il test, compilazione o la distribuzione. Per altre informazioni su Visual Studio Team Services vedere le connessioni del servizio [gli endpoint servizio di compilazione e versione](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Le definizioni di compilazione.** Definire i processi di compilazione automatizzata e comporre un set di attività tramite il catalogo delle attività. Per altre informazioni sulla compilazione Vedere definizioni [creare una definizione di compilazione](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) documentazione.

 -  **Definizioni di versione.** Definire il processo di distribuzione per una raccolta di ambienti in cui vengono distribuiti gli elementi dell'applicazione. Per altre informazioni sulla versione di vedere le definizioni [creare una definizione di versione](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) documentazione.

 -  **Pool di agenti di compilazione Linux ospitato Visual Studio Team Services.** Compilare, testare e distribuire rapidamente applicazioni agente mediante un linguaggio gestito e mantenuto ospitato. Per altre informazioni sulla compilazione di Visual Studio Team Services ospitato vedere agents [agenti ospitati](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) documentazione.

## <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione

Creare un account di archiviazione e un contenitore per la pulizia dati risiedano.

1.  Accedi per il [ *portale di Azure*](https://portal.azure.com/).

2.  Nel portale di Azure, espandere il menu a sinistra per aprire il menu dei servizi e scegliere **tutti i servizi**. Scorrere verso il basso **memorizzazione** e scegliere **account di archiviazione**. Nel * * gli account di archiviazione * * finestra scegliere **Add**.

3.  Immettere un nome per l'account di archiviazione.

    > [!Note]  
    > I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. Il nome di account di archiviazione deve essere univoco all'interno di Azure. Il portale di Azure indica se il nome di account di archiviazione selezionato è già in uso.

4.  Specificare il modello di distribuzione da usare: **Resource Manager**.

5.  Selezionare il tipo di account di archiviazione: **utilizzo generico V1**, quindi specificare il livello di prestazioni: **Standard**.

6.  Selezionare l'opzione di replica dell'account di archiviazione: **archiviazione con ridondanza geografica**.

7.  Selezionare nuovo abbonamento di account di archiviazione.

8.  Specificare un nuovo gruppo di risorse o selezionarne uno esistente.

9.  Selezionare la posizione geografica dell'account di archiviazione.

10. Selezionare **Crea** per creare l'account di archiviazione.

    ![Alt text](\media\azure-stack-solution-machine-learning\image1.png)

11.  Scegliere l'account di archiviazione creato di recente.

12.  Select on **BLOB**.

    ![Alt text](media\azure-stack-solution-machine-learning\image2.png)

13.  Selezionare sul **+ contenitore** e selezionare **contenitore**.

    ![Alt text](media\azure-stack-solution-machine-learning\image3.png)

14.  Assegnare il nome al contenitore **uploadeddata** e scegliere il tipo di accesso **contenitore**.

15.  Select on **creare**.

    ![Alt text](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Passaggio 2: Creare una macchina virtuale di Data Science

Creare un Ubuntu Data Science Virtual Machine (DSVM) nel portale di Azure.

1.  Accedere al portale di Azure da [https://portal.azure.com](https://portal.azure.com/)

2.  Selezionare il **+ nuovo** collegamento e cercare "Data Science Virtual Machine per Linux Ubuntu CSP

    ![Alt text](media\azure-stack-solution-machine-learning\image5.png)

1.  Scegli **macchina virtuale Data Science per Linux (Ubuntu)** nell'elenco e seguire sullo schermo le istruzioni per creare la macchina virtuale data SCIENCE.

    ![Alt text](media\azure-stack-solution-machine-learning\image6.png)

> ! [Importante]  
> **Scegli** Password * * come i*tipo di autenticazione*.

Posizionare la nuova macchina virtuale data SCIENCE nello stesso gruppo di risorse dell'account di archiviazione appena creato. Tutti gli oggetti di Machine Learning Edge vengono distribuiti in Azure all'interno di questo gruppo di risorse.

1.  Impostazioni consentono di configurare le funzionalità facoltative

    a.  Selezionare il **Account di archiviazione** creato in precedenza.

    b.  Creare una nuova **rete virtuale**, **Subnet**, e **indirizzo IP pubblico** deve per impostazione predefinita creare un nome basato sul nome del gruppo di risorse.

    c.  Creare una nuova **NSG** deve creare automaticamente questo con le regole corrette già applicate.

    d.  Per il **Account di archiviazione di diagnostica**, selezionare l'account di archiviazione creato in precedenza.

    e.  Nota: Con AAD abilitate e configurate per la sottoscrizione di Azure, Managed Service Identity può essere abilitata anche.

2.  Selezionare **OK**.

### <a name="connect-to-the-dsvm"></a>Connettere la macchina virtuale data SCIENCE

Dopo aver creato la macchina virtuale data SCIENCE, connettersi alla VM dal sottosistema Windows per Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Tipo Sì quando viene richiesto di confermare la connessione della macchina virtuale.

2.  Immettere la password creata per la macchina virtuale data SCIENCE.

### <a name="update-the-dsvm"></a>Aggiornare la macchina virtuale data SCIENCE 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Passaggio 3: Distribuire Azure Machine Learning Services

Distribuire Azure Machine Learning Services in Azure.

I servizi di Azure Machine Learning (anteprima) sono una soluzione integrata di data science e analisi avanzata end-to-end. Consentono a data scientist professionisti di preparare i dati, sviluppare esperimenti e distribuire modelli su scala cloud.

Questa sezione viene illustrato:

> [!div class="checklist"]
> - Creare account del servizio per servizi Azure Machine Learning
> - Installare Azure Machine Learning Workbench ed eseguire l'accesso
> - Creare un progetto in Workbench
> - Eseguire uno script in tale progetto
> - Accedere all'interfaccia della riga di comando

Poiché sono parte del portfolio di Microsoft Azure, i servizi di Azure Machine Learning richiedono una sottoscrizione di Azure. Per ottenere una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Sono necessarie adeguate autorizzazioni per creare risorse quali gruppi di risorse, macchine virtuali e altri asset.

L'applicazione di Azure Machine Learning Workbench può essere installato nei sistemi operativi seguenti:

 -  Windows 10 o Windows Server 2016
 -  macOS Sierra o High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Passaggio 4: Creare servizi di Azure Machine Learning

Creare account di servizi di Azure Machine Learning.

Usare il portale di Azure per effettuare il provisioning di account di Azure Machine Learning:

1.  Accedi per il [portale di Azure](https://portal.azure.com/) utilizzando le credenziali della sottoscrizione di Azure da usare. Per ottenere una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Alt text](media\azure-stack-solution-machine-learning\image7.png)

1.  Selezionare il pulsante **Crea una risorsa** (+) nell'angolo superiore sinistro del portale.

    ![Creare una risorsa nel portale di Azure](media\azure-stack-solution-machine-learning\image8.png)

1.  Immettere **Machine Learning** nella barra di ricerca. Selezionare il risultato di ricerca denominato **Machine Learning Experimentation (anteprima)**.

    ![Ricerca di Azure Machine Learning](media\azure-stack-solution-machine-learning\image9.png)

1.  Nel **sperimentazione di Machine Learning** riquadro, scorrere verso il basso e selezionare **crea** per iniziare a definire l'account di sperimentazione.

    ![Azure Machine Learning: creazione dell'account di Sperimentazione](media\azure-stack-solution-machine-learning\image10.png)

1.  Nel **sperimentazione di Machine Learning** riquadro Configura l'account di sperimentazione di Machine Learning.

    | Impostazione | Valore consigliato per l'esercitazione | DESCRIZIONE |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome account di Experimentation | Nome univoco | Immettere un nome univoco che identifica l'account. Usare un nome di reparto o progetto che identifica meglio l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). |
    | Sottoscrizione | Sottoscrizione | Scegliere la sottoscrizione di Azure da usare per l'esperimento. In presenza di più sottoscrizioni, scegliere la sottoscrizione appropriata in cui viene fatturata la risorsa. |
    | Gruppo di risorse | Gruppo di risorse | Usare un gruppo di risorse nella sottoscrizione oppure immettere un nome per creare un nuovo gruppo di risorse per questo account di sperimentazione. |
    | Località | L'area più vicina agli utenti | Scegliere la località più vicina agli utenti e le risorse di dati. |
    | Number of seats (Numero di postazioni) | 2 | Immettere il numero di postazioni. Informazioni sull'[impatto delle postazioni sui prezzi](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Per questa Guida introduttiva, sono necessari solo due postazioni. È possibile aggiungere o rimuovere le postazioni secondo necessità nel portale di Azure. |
    | Account di archiviazione | Nome univoco | Selezionare **Crea nuovo** e specificare un nome per creare un [account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). Il nome deve contenere da 3 a 24 caratteri esclusivamente alfanumerici. In alternativa, selezionare **Usa esistente** e selezionare l'account di archiviazione esistente dall'elenco. L'account di archiviazione è obbligatorio e viene usato per contenere gli elementi del progetto e i dati della cronologia di esecuzione. |
    | Workspace for Experimentation account (Area di lavoro per account di Sperimentazione) | IrisGarden<br>(nome usato nelle esercitazioni) | Specificare un nome per un'area di lavoro per questo account. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). Questa area di lavoro contiene gli strumenti necessari per creare, gestire e pubblicare esperimenti. |
    | Assign owner for the workspace (Proprietario area di lavoro) | L'account | Selezionare il proprio account come proprietario dell'area di lavoro. |
    | Creare un account di Gestione modelli | **check** | Creare un account di Gestione modelli. Questo verrà considerato per distribuire e gestire i modelli come servizi web in tempo reale. <br><br>Anche se facoltativo, è consigliabile creare l'account Gestione modelli nello stesso momento account di sperimentazione. |
    | Nome account | Nome univoco | Scegliere un nome univoco che identifica l'account Gestione modelli. Usare il reparto o progetto nome che identifica meglio l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). |
    | Piano tariffario di Gestione modelli | **DEVTEST** | Selezionare **nessun piano tariffario selezionato** per specificare il piano tariffario per il nuovo account di gestione modelli. Per risparmiare sui costi, selezionare il piano tariffario sviluppo/test, se è disponibile per la sottoscrizione (disponibilità limitata). In caso contrario selezionare il piano tariffario S1. Scegliere di selezionare questa opzione per salvare la selezione del piano tariffario. |
    | Aggiungi al dashboard | verifica | Selezionare il **Aggiungi al dashboard** possibilità di tenere facilmente traccia dell'account sperimentazione di Machine Learning nella pagina iniziale del dashboard del portale di Azure. |

    ![Configurazione dell'account di Sperimentazione di Machine Learning](media\azure-stack-solution-machine-learning\image11.png)

1.  Selezionare **Crea** per avviare il processo di creazione dell'account di Sperimentazione unitamente a quello di Gestione modelli.

    ![Configurazione dell'account di Sperimentazione di Machine Learning](media\azure-stack-solution-machine-learning\image12.png)

    Potrebbero occorrere alcuni minuti per creare un account. Controllare lo stato del processo di distribuzione selezionando l'icona delle notifiche (campanello) sulla barra degli strumenti del portale Azure.

    ![Notifiche del portale di Azure](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Installare e accedere a workbench 

Azure Machine Learning Workbench è disponibile per Windows o macOS. Visualizzare l'elenco delle [piattaforme supportate](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

**Avviso:** l'installazione potrebbe richiedere circa un'ora per il completamento.

1.  Scaricare e avviare il programma di installazione della versione più recente di Workbench.

    > [!Important]  
    > Scaricare l'intero programma di installazione sul disco e avviarlo. Non eseguirlo direttamente dal widget di download del browser.<br>**In Windows:<br>**  una. Scaricare [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. In Esplora file fare doppio clic sul programma di installazione scaricato.

1.  Seguire sullo schermo le istruzioni nel programma di installazione fino al completamento.

    * * L'installazione potrebbe richiedere più di 30 minuti per completare. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    Il programma di installazione scaricherà e configurare tutte le dipendenze necessarie, ad esempio Python, Miniconda e altre librerie correlate. Questa installazione include anche la strumento da riga di comando di Azure multi-piattaforma o della riga di comando di Azure.

1.  Per avviare Workbench, selezionare il pulsante **Launch Workbench** (Avvia Workbench) nell'ultima schermata del programma di installazione.

    Se l'installazione guidata viene chiusa, avviare usando il **Machine Learning Workbench** collegamento sul desktop.

1.  Selezionare **Accedi con Microsoft** per l'autenticazione con Azure Machine Learning Workbench. Usare le stesse credenziali usate nel portale di Azure per creare gli account di sperimentazione e Gestione modelli.

    Una volta effettuato l'accesso, Workbench Usa il primo account di sperimentazione trovato nelle sottoscrizioni di Azure e Visualizza tutte le aree di lavoro e i progetti associati a quell'account.

    > [!Tip]  
    > Passare a un account di sperimentazione diverso usando l'icona nell'angolo inferiore sinistro della finestra dell'applicazione Workbench.

### <a name="create-a-new-project-in-workbench"></a>Creare un nuovo progetto in workbench

1.  Aprire l'app di Azure Machine Learning Workbench e l'accesso se necessario.

    - In Windows, avviare usando il **Machine Learning Workbench** collegamento sul desktop.

    - In macOS selezionare **Azure ML Workbench** in Launchpad.

1.  Selezionare il segno più (+) nel riquadro **PROJECTS** (PROGETTI) e scegliere **New Project** (Nuovo progetto).

    ![Nuova area di lavoro](media\azure-stack-solution-machine-learning\image14.png)

1.  Compilare i campi del modulo e selezionare il pulsante **Create** (Crea) per creare un nuovo progetto in Workbench.

    | Campo | Valore consigliato per l'esercitazione | DESCRIZIONE |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Project name (Nome progetto) | myIris | Immettere un nome univoco che identifica l'account. Usare il reparto o progetto nome che identifica meglio l'esperimento. Il nome deve avere una lunghezza compresa tra 2 e 32 caratteri. Può contenere solo caratteri alfanumerici e il trattino (-). |
    | Directory del progetto | c:\Temp\ | Specificare la directory in cui viene creato il progetto. |
    | Descrizione del progetto | Lasciare vuoto | Campo facoltativo utile per descrivere i progetti. |
    | URL repository GIT Visualstudio.com | Lasciare vuoto | Campo facoltativo. Associare un progetto a un repository Git in Visual Studio Team Services per la collaborazione e controllo del codice sorgente. [Informazioni su come configurare un repository](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Area di lavoro selezionata | IrisGarden (se esistente) | Scegliere un'area di lavoro creato per l'account di sperimentazione nel portale di Azure. <br>Utilizzo dell'avvio rapido, viene elencato l'area di lavoro dal nome del IrisGarden. In caso contrario, usare l'area di lavoro con il nome dell'account di sperimentazione, o un nome di account preferito. |
    | Modello di progetto | Classificazione del set di dati Iris | I modelli contengono script e i dati usati per esplorare il prodotto. Questo modello contiene gli script e i dati necessari per questa Guida introduttiva e le altre esercitazioni in questo sito della documentazione. |

    ![Nuovo progetto](media\azure-stack-solution-machine-learning\image15.png)

1.  Viene creato un nuovo progetto e il dashboard viene aperto con tale progetto visualizzato. Esplorare la home page del progetto, origini dati, blocchi appunti e file del codice sorgente.

    ![Aprire il progetto](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Collegare una destinazione di calcolo DSVM

Dopo aver creato la macchina virtuale data SCIENCE, collegarlo al progetto Azure Machine Learning.

1.  All'interno dell'app di Azure Machine Learning Workbench, avviare l'interfaccia CLI di Azure Machine Learning Workbench selezionando **File**->**aprire PowerShell**

    ![Alt text](media\azure-stack-solution-machine-learning\image17.png)

1.  Una volta il comando di PowerShell Prompt dei comandi aperto utilizzare il comando seguente:

    ```PowerShell  
        az login
    ```

1.  Riceve la richiesta seguente:

     ![Alt text](media\azure-stack-solution-machine-learning\image18.png)

1.  Passare al sito come descritto in dettaglio nel prompt dei comandi e immettere il codice fornito.

    ![Alt text](media\azure-stack-solution-machine-learning\image19.png)

1.  Selezionare Continua quando richiesto, quindi selezionare l'account di Azure che è associato l'Account di sperimentazione Machine Learning di Azure.

    ![Alt text](media\azure-stack-solution-machine-learning\image20.png)

1.  L'interfaccia CLI di Azure Machine Learning Workbench ti invierà il messaggio seguente:

    ![Alt text](media\azure-stack-solution-machine-learning\image21.png)

1.  Account di accesso di Account di Machine Learning e dell'area di lavoro viene illustrato come esito positivo, collegare la macchina virtuale data SCIENCE.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    Verrà visualizzata la notifica seguente:

    ![Alt text](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Questo processo richiederà del tempo e genererà una notevole quantità di testo come estrae diverse immagini docker, di assemblybiztalk li registra e quindi applica il codice necessario e le applicazioni a loro.

Ora eseguire esperimenti in questa DSVM.

### <a name="create-a-data-preparation-package"></a>Creare un pacchetto di preparazione dei dati

Successivamente, iniziare a preparare i dati in Azure Machine Learning Workbench. Ogni trasformazione eseguita in Workbench viene archiviato in un formato JSON in un pacchetto di preparazione dei dati locali (\*file. dprep). Questo pacchetto di preparazione dei dati è il contenitore principale per le operazioni di preparazione dei dati in Workbench.

Questo pacchetto di preparazione dei dati può essere passato in seguito a un runtime, ad esempio local-C\#/CoreCLR, Scala/Spark o Scala/HDI.

1.  Selezionare l'icona della cartella per aprire la visualizzazione dei file, quindi selezionare **iris.csv** per aprire il file.

    Il file contiene una tabella con 5 colonne e 50 righe. Verranno visualizzate quattro colonne sono colonne numeriche. La quinta colonna è una colonna di destinazione di stringa. Nessuna delle colonne include nomi di intestazione.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  Nella **visualizzazione dati** selezionare il segno più (**+**) per aggiungere una nuova origine dati. Verrà visualizzata la pagina **Aggiungi origine dati**.

    ![Visualizzazione dati in Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image24.png)

1.  Selezionare **file di testo (\*, con estensione csv \*con estensione JSON, \*. txt.,...)** .

    ![Origine dati in Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image25.png)

1.  Selezionare **Avanti**.

2.  Individuare il file **iris**e selezionare **fine**. Verranno usati valori predefiniti per parametri come separatori e tipi di dati.

    > [!Important]  
    > Selezionare il **iris** file dalla directory del progetto corrente per questo esercizio. In caso contrario, i passaggi successivi potrebbero non riuscire.

    ![Selezionare iris](media\azure-stack-solution-machine-learning\image26.png)

1.  Un nuovo file denominato `*iris-1.dsource` viene creato. Il file è denominato in modo univoco con `-1` perché il progetto di esempio include già un **dsource** file.

    Il file verrà aperto e verranno visualizzati i dati. Una serie di intestazioni di colonna, da **Column1** al **Column5**, viene aggiunto automaticamente a questo set di dati. Scorrere verso il basso e notare che l'ultima riga del set di dati è vuota. La riga è vuota a causa dell'interruzione di riga aggiuntiva nel file CSV.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image27.png)

1.  Selezionare il pulsante **Metriche**. Gli istogrammi verranno generati e visualizzati.

    Passare nuovamente alla vista dati selezionando il **dati** pulsante.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image28.png)

1.  Osservare gli istogrammi. Per ogni colonna è stato calcolato un set completo di statistiche.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image29.png)

1.  Iniziare la creazione di un pacchetto di preparazione dei dati selezionando il pulsante **Prepara**. Verrà visualizzata la finestra di dialogo **Prepara**.

    Il progetto di esempio contiene un' **iris. dprep** file di preparazione dei dati selezionata per impostazione predefinita.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image30.png)

1.  Creare un nuovo pacchetto di preparazione dei dati selezionando **+ nuovo pacchetto di preparazione dei dati** dal menu di scelta.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image31.png)

1.  Immettere un nuovo valore per il nome del pacchetto (usare **iris-1**) e quindi selezionare **OK**.

    Un nuovo pacchetto di preparazione dei dati denominato **iris-1.dprep** viene creato e aperto nell'editor di preparazione dei dati.

    ![Visualizzazione dati Iris](media\azure-stack-solution-machine-learning\image32.png)

    Successivamente, è necessaria la preparazione dei dati.

1.  Selezionare ogni intestazione di colonna per rendere modificabile il testo dell'intestazione. Rinominare quindi ogni colonna, come indicato di seguito:

    In ordine, immettere **lunghezza del Sepalo**, **Sepal Width**, **Petal Length**, **Petal Width**, e **Species** per le cinque colonne rispettivamente.

    ![Rinominare le colonne](media\azure-stack-solution-machine-learning\image33.png)

1.  Contare i valori distinti:

    1.  Selezionare la colonna **Species**.

    2.  Fare clic con il pulsante destro del mouse per selezionare un'opzione.

    3.  Selezionare **dei conteggi di valore** dal menu di scelta.

        Il riquadro **Inspectors** (Controlli) viene visualizzato sotto i dati. Viene visualizzato un istogramma con quattro barre. La colonna di destinazione dispone di quattro valori distinti: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**e un **(null)** valore.

    ![Selezionare Value Counts (Conteggi dei valori)](media\azure-stack-solution-machine-learning\image34.png)

    ![Istogramma dei conteggi dei valori](media\azure-stack-solution-machine-learning\image35.png)

1.  Per escludere tramite filtro i valori Null, selezionare la barra "(null)" e quindi selezionare il segno meno (**-**).

    La riga (null) viene quindi visualizzata in grigio per indicare che è stata esclusa tramite filtro.

    ![Escludere tramite filtro i valori null](media\azure-stack-solution-machine-learning\image36.png)

1.  Notare i singoli passaggi per la preparazione dei dati indicati in modo dettagliato nel riquadro **STEPS** (PASSAGGI). Come sono state rinominate le colonne e righe con valore null vengono filtrate, ogni azione viene registrata come passaggio di preparazione dei dati. Modificare singoli passaggi per cambiarne le impostazioni, riordinare i passaggi e rimuoverli.

    ![Alt text](media\azure-stack-solution-machine-learning\image37.png)

1.  Chiudere l'editor di preparazione dei dati. Selezionare l'icona **x** nella scheda **iris-1** con l'icona del grafico per chiudere la scheda. Il lavoro viene salvato automaticamente nel **iris-1.dprep** file visualizzato sotto il **preparazioni dati** intestazione.

    ![Chiudi](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Generare codice Python per richiamare un pacchetto di preparazione dei dati

L'output di un pacchetto di preparazione dei dati può essere esaminato direttamente in Python o in un notebook di Jupyter. I pacchetti possono essere eseguiti in più runtime tra cui Python, Spark che include Docker, e HDInsight locali.

1.  Individuare il file **iris-1.dprep** nella scheda Data Preparations (Preparazioni dati).

2.  Fare clic con il pulsante destro del mouse sul file **iris-1.dprep**, quindi scegliere **Generate Data Access Code File** (Genera file di codice per l'accesso ai dati) dal menu contestuale.

    ![Generare il codice](media\azure-stack-solution-machine-learning\image39.png)

    Un nuovo file denominato **iris-1.py** viene aperto con le righe di codice per richiamare la logica creata come pacchetto di preparazione dei dati seguenti:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    A seconda del contesto in cui viene eseguito questo codice, drep rappresenta un tipo diverso di frame di dati:

    -  In caso di esecuzione in un runtime di Python, viene usato un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -  In caso di esecuzione in un contesto Spark, viene usato un [frame di dati Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html).

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Esaminare iris_sklearn.py e i file di configurazione

1.  Nel progetto aperto, selezionare la **file** pulsante (icona della cartella) nel riquadro a sinistra per aprire l'elenco file nella cartella del progetto.

    ![Aprire il progetto di Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image40.png)

1.  Selezionare il file di script **iris_sklearn.py** di Python.

    ![Scegliere uno script](media\azure-stack-solution-machine-learning\image41.png)

    Il codice apre una nuova scheda dell'editor di testo in Workbench.

    > [!Note]  
    > Il codice visualizzato potrebbe non essere esattamente uguale al codice precedente poiché questo progetto di esempio viene aggiornato di frequente.

    ![Aprire un file](media\azure-stack-solution-machine-learning\image42.png)

1.  Esaminare il codice dello script Python per acquisire familiarità con lo stile di codifica.

    Lo script **iris_sklearn.py** esegue le attività seguenti:  

    -  Carica il pacchetto di preparazione dati predefinito denominato **iris.dprep** per creare un [frame di dati pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Aggiunge funzionalità casuali per rendere il problema più difficile da risolvere. La casualità è necessaria perché Iris è un set di dati di piccole dimensioni che viene classificato con un'accuratezza quasi del 100%.

    -  Usa libreria di apprendimento automatico thescikit learnmachine per compilare un modello di regressione logistica. Questa libreria è inclusa con Azure Machine Learning Workbench per impostazione predefinita.

    -  Serializza il modello usando la libreria [pickle](https://docs.python.org/3/library/pickle.html) in un file della cartella `outputs`.

    -  Carica il modello serializzato e quindi lo deserializza in memoria.

    -  Usa il modello deserializzato per eseguire una stima su un nuovo record.

    -  Traccia due grafici, una matrice di confusione e un ricevitore multiclasse operativo curva di caratteristica (ROC), utilizzando il [matplotlib](https://matplotlib.org/) libreria e quindi li salva in theoutputsfolder. Se non evidente, installare questa libreria nell'ambiente.

    -  Vengono tracciati automaticamente l'accuratezza di modello e frequenza regolarizzazione nella cronologia di esecuzione. L'oggetto `run_logger` viene usato per registrare il tasso di regolarizzazione e l'accuratezza del modello nei log.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Eseguire iris_sklearn. py nell'ambiente locale

1.  Avviare l'interfaccia della riga di comando di Azure Machine Learning:

    1.  Avviare Azure Machine Learning Workbench.

    2.  Dal menu di Workbench selezionare **File**> **Open Command Prompt** (Apri prompt dei comandi).

    La finestra di interfaccia della riga di comando (CLI) di Azure Machine Learning inizia nella cartella del progetto `C:\Temp\\myIris\` su Windows. Questo progetto è lo stesso di quello creato nella parte 1 dell'esercitazione.

    > [!Important]  
    > Usare questa finestra di comando per completare i passaggi successivi.

1.  Nella finestra della riga di comando, installare la libreria di tracciato Python **matplotlib**, se non è già installato.

    Lo script **iris_sklearn.py** include dipendenze da due pacchetti Python: **scikit-learn** e **matplotlib**. Il **scikit-informazioni su** pacchetto viene installato da Azure Machine Learning Workbench per semplificare il lavoro. Tuttavia, l'installazione di **matplotlib** potrebbe essere necessaria.

    Se procede senza installare **matplotlib**, il codice in questa esercitazione possa essere eseguita correttamente. Il codice non sarà tuttavia in grado di produrre l'output della matrice di confusione e i tracciati della curva ROC multiclasse mostrati nelle visualizzazioni della cronologia.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    L'installazione richiede circa un minuto.

1.  Tornare all'applicazione Workbench.

2.  Trovare la scheda denominata **iris_sklearn.py**.

    ![Trovare la scheda con lo script](media\azure-stack-solution-machine-learning\image43.png)

1.  Sulla barra degli strumenti della scheda, selezionare **locale** come ambiente di esecuzione, andiris_sklearn.pyas lo script da eseguire. È possibile che queste opzioni siano già selezionate.

    ![Alt text](media\azure-stack-solution-machine-learning\image44.png)

1.  Spostare a destra della barra degli strumenti ed enter0.01in il **argomenti** campo.

    Questo valore corrisponde al tasso di regolarizzazione del modello di regressione logistica.

    ![Selezione di ambiente locale e script](media\azure-stack-solution-machine-learning\image45.png)

1.  Selezionare il pulsante **Run** (Esegui). Viene immediatamente pianificato un processo. Il processo viene elencato nel riquadro **Jobs** (Processi) sul lato destro della finestra di Workbench.

    ![Selezione di ambiente locale e script](media\azure-stack-solution-machine-learning\image46.png)

    Dopo alcuni istanti, lo stato del processo passa da **Submitting**, alla **che esegue**e infine a **Completed**.

1.  Selezionare **Completed** (Completato) nel testo relativo allo stato del processo nel riquadro **Jobs** (Processi).

    ![Eseguire sklearn](media\azure-stack-solution-machine-learning\image47.png)

    Una finestra popup apre e visualizza il testo di output standard (stdout) per l'esecuzione. Per chiudere questo testo, selezionare la **chiudere** (**x**) pulsante nell'angolo in alto a destra della finestra popup.

    ![Output standard](media\azure-stack-solution-machine-learning\image48.png)

1.  Nello stato dello stesso processo nel **processi** riquadro, selezionare il testo in blu **iris_sklearn.py \[n\] **(* n * è il numero di esecuzione) subito sopra il  **Completato** lo stato e l'ora di inizio. Si apre la finestra **Run Properties** (Proprietà esecuzione) contenente le informazioni seguenti su questa specifica esecuzione:

    -  Informazioni sulle **proprietà di esecuzione**

    -  **Outputs**

    -  **Metriche**

    -  Eventuali **visualizzazioni**

    -  **Log**

    Al termine dell'esecuzione, la finestra popup mostra i risultati seguenti:

    > [!Note]  
    > Poiché l'esercitazione ha introdotto alcune della sequenza casuale in set di training, i risultati esatti potrebbero variare rispetto di illustrato di seguito.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Chiudere la scheda **Run Properties** (Proprietà esecuzione) e quindi tornare alla scheda **iris_sklearn.py**.

1.  Ripetere per le altre esecuzioni.

Nel campo **Arguments** (Argomenti) immettere una serie di valori compresi tra `0.001` e `10`. Selezionare **Run** (Esegui) per eseguire il codice alcune altre volte. Il valore dell'argomento modificato ogni volta viene immesso il modello di regressione logistica nel codice, generando risultati diversi ogni volta.

### <a name="review-the-run-history-in-detail"></a>Esaminare la cronologia di esecuzione in dettaglio

In Azure Machine Learning Workbench ogni esecuzione dello script viene acquisita come record della cronologia di esecuzione. Visualizzare la cronologia di esecuzione di uno script specifico aprendo la **esecuzioni** visualizzazione.

1.  Per aprire l'elenco di **esecuzioni**, selezionare il pulsante **Runs** (Esecuzioni) (icona a forma di orologio) sulla barra degli strumenti a sinistra. Quindi selezionare **iris_sklearn. py** per visualizzare i **Dashboard esecuzione** ofiris_sklearn.py.

    ![Visualizzazione delle esecuzioni](media\azure-stack-solution-machine-learning\image49.png)

1.  La scheda **Run Dashboard** (Dashboard esecuzioni) verrà aperta.

    Esaminare le statistiche acquisite in più esecuzioni. Grafici di eseguire il rendering nella parte superiore della scheda. Ogni esecuzione ha un numero consecutivo e i dettagli delle esecuzioni vengono elencati nella tabella nella parte inferiore della schermata.

    ![Dashboard delle esecuzioni](media\azure-stack-solution-machine-learning\image50.png)

1.  Filtrare la tabella e quindi selezionare uno dei grafici per visualizzare lo stato, la durata, l'accuratezza e il tasso di regolarizzazione di ogni esecuzione.

2.  Selezionare le caselle di controllo accanto a due o più esecuzioni nella tabella **Runs** (Esecuzioni). Selezionare il pulsante **Compare** (Confronta) per aprire un riquadro dettagliato di confronto. Esaminare il confronto con le informazioni affiancate.

3.  Per tornare a **Run Dashboard** (Dashboard esecuzioni) selezionare il pulsante **Run List** (Elenco di esecuzione) nella parte superiore sinistra del riquadro **Comparison** (Confronto).

    ![Tornare all'elenco di esecuzione](media\azure-stack-solution-machine-learning\image51.png)

1.  Selezionare una singola esecuzione per visualizzarne i dettagli. Si noti che le statistiche per l'esecuzione selezionata sono elencate nella sezione **Run Properties** (Proprietà esecuzione). I file scritti nella cartella di output sono elencati nel **output** sezione e scaricare i file da tale posizione.

    ![Dettagli dell'esecuzione](media\azure-stack-solution-machine-learning\image52.png)

Nella sezione **Visualizations** (Visualizzazioni) viene eseguito il rendering dei due tracciati, ovvero la matrice di confusione e la curva ROC multiclasse. Tutti i file di log sono disponibili nella sezione **Logs** (Log).

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Eseguire gli script nella finestra della riga di comando di Azure Machine Learning (ML) Workbench

1.  Avviare l'interfaccia della riga di comando di Azure Machine Learning:

    1.  Avviare Azure Machine Learning Workbench.

    2.  Dal menu di Workbench selezionare **File** > **Open Command Prompt** (Apri prompt dei comandi).

    L'avvio di prompt della riga di comando nella cartella del progetto `C:\\Temp\\myIris` su Windows. Si tratta del progetto creato nella parte 1 dell'esercitazione.

    > [!Important]  
    > Usare questa finestra di comando per completare i passaggi successivi.

1.  Accedere ad Azure nella finestra dell'interfaccia della riga di comando. [Altre informazioni su az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Ignorare questo passaggio se è già connessi.

1.  Al prompt dei comandi immettere:

    ```CLI
        az login
    ```

    Questo comando restituisce un codice da usare nel browser all'indirizzo [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Passare a [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) nel browser. Immettere il codice ricevuto dai dati ricevuti della riga di comando.

    L'app Workbench e della riga di comando usano cache delle credenziali indipendenti per l'autenticazione nelle risorse di Azure. Autenticazione non è necessaria anche in questo caso fino alla scadenza del token memorizzato nella cache.

1.  Se l'organizzazione ha più sottoscrizioni di Azure (ambiente aziendale), impostare la sottoscrizione da usare. Trovare la sottoscrizione, impostarla usando l'ID sottoscrizione e quindi testarla.

1.  Elencare ogni sottoscrizione di Azure a cui si accede tramite questo comando:

    ```CLI
        az account list -o table 
    ```

    Il **elenco di account di az** comando restituisce l'elenco delle sottoscrizioni disponibili per l'account di accesso. Se è presente più di uno, identificare il valore ID della sottoscrizione per la sottoscrizione usata.

1.  Impostare la sottoscrizione di Azure usata come account predefinito:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Dove < the-subscription-id > è il valore di ID per la sottoscrizione usata. Non includere le parentesi acute.

1.  Confermare l'impostazione della nuova sottoscrizione richiedendo i dettagli per la sottoscrizione corrente.

    ```CLI
        az account show
    ```

1.  Nella finestra dell'interfaccia della riga di comando inviare lo script **iris_sklearn.py** come esperimento.

    L'esecuzione di iris_sklearn. py viene eseguito un contesto di calcolo locale.

1.  In Windows:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  In MacOS:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    L'output dovrebbe essere simile a: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Esaminare l'output. Tutto deve essere lo stesso output e i risultati come quando lo script di esecuzione di Workbench.

7.  Tornare a Workbench ed eseguire queste operazioni:

    Selezionare l'icona della cartella nel riquadro a sinistra per elencare i file del progetto.  Aprire lo script di Python denominato **run.py**. Questo script è utile a ciclo su vari tassi di regolarizzazione. 

    ![Tornare all'elenco di esecuzione](media\azure-stack-solution-machine-learning\image53.png)

1.  Eseguire l'esperimento più volte con tali tassi.

    Questo script avvia` aniris_sklearn.pyjob` con una o di frequenza di regolarizzazione `10.0` (un numero estremamente grande). Lo script quindi dimezza il tasso a metà nella seguente di esecuzione e così via, fino a ottenere la frequenza non inferiore a `0.005`. Lo script contiene il codice seguente:

    ![Tornare all'elenco di esecuzione](media\azure-stack-solution-machine-learning\image54.png)

1.  Eseguire lo script **run.py** dalla riga di comando, come illustrato di seguito:

    ```CLI
        python run.py
    ```
Questo comando Invia `iris_sklearn.py` più volte con diverse frequenze di regolarizzazione

Quando `run.py` al termine, grafici delle metriche diversi vengono visualizzato nella visualizzazione elenco della cronologia di esecuzione in workbench.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Eseguire gli script in una basata su Ubuntu Data Science Virtual Machine (DSVM) in Azure

Per eseguire lo script in un contenitore Docker in un computer Linux remoto, è necessario l'accesso SSH (nome utente e password) per l'esecuzione di tale computer remoto. Nel computer deve anche essere installato e in esecuzione un motore Docker.

1.  Modifica generato<your DSVM Name>.runconfigfile underaml_configand sostituire il framework valuePySparktoPython l'impostazione predefinita:

    ```yaml  
    Framework: Python
    ```
1.  Eseguire lo stesso comando come indicato in precedenza nella finestra della riga di comando, utilizza come destinazione*<DSVM>* il tempo richiesto per eseguire iris_sklearn. py in un contenitore Docker remoto: (sostituire il <DSVM> con il nome della VM di analisi scientifica dei dati, senza parentesi).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

Il comando esegue come se in adocker-pythonenvironment, ad eccezione del fatto si verifica l'esecuzione nella VM di Linux remoto. La finestra di interfaccia della riga di comando visualizza le stesse informazioni di output.

### <a name="download-the-model-pickle-file"></a>Scaricare il file pickle del modello

Nella parte precedente dell'esercitazione, lo script **iris_sklearn.py** è stato eseguito in locale in Machine Learning Workbench. Tale azione ha serializzato il modello di regressione logistica usando il diffuso pacchetto di serializzazione degli oggetti Python [pickle](https://docs.python.org/3/library/pickle.html).

1.  Aprire l'applicazione Machine Learning Workbench. Quindi aprire il **myIris** progetto creato nelle parti precedenti della serie di esercitazioni.

2.  Dopo aver aperto il progetto, selezionare la **file** pulsante (icona della cartella) nel riquadro sinistro per aprire l'elenco dei file nella cartella del progetto.

3.  Selezionare il file **iris_sklearn.py**. Il codice Python verrà aperto in una nuova scheda dell'editor di testo nel workbench.

4.  Esaminare il file **iris_sklearn.py** per individuare dove è stato generato il file pickle. Usare CTRL+F per aprire la finestra di dialogo di **ricerca** e quindi trovare la parola **pickle** nel codice Python.

Questo frammento di codice illustra come è stato generato il file di output ottenuto con pickle. Il file pickle di output è denominato **model.pkl** nel disco.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Individuare il file pickle del modello nei file di output di un'esecuzione precedente.

    Quando la **iris_sklearn. py** lo script viene eseguito, viene scritto il file di modello per il **genera** cartella con il nome **Model. pkl**. Questa cartella risiede nell'ambiente di esecuzione scelto per eseguire lo script, non nella cartella del progetto locale. 

    1. Per individuare il file, selezionare la **esecuzioni** pulsante (icona di orologio) nel riquadro sinistro per aprire l'elenco delle **tutte le esecuzioni**.  

    2. Verrà aperta la scheda **All Runs** (Tutte le esecuzioni). Nella tabella delle esecuzioni, selezionare una delle esecuzioni recenti in cui la destinazione è stata **locale** e il nome dello script era **iris_sklearn. py**.  

    3. Verrà visualizzato il riquadro **Run Properties** (Proprietà esecuzione). Nella sezione superiore destra del riquadro, si noti che il **output** sezione. d\. Per scaricare il file pickle, selezionare la casella di controllo accanto al **Model. pkl** e quindi selezionare **scaricare**. Salvare il file nella radice della cartella del progetto. Il file è necessario nei prossimi passaggi.  

    ![Scaricare il file pickle](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Ottenere i file di script e lo schema di assegnazione dei punteggi

Per distribuire il servizio web insieme al file di modello, è necessario uno script di assegnazione dei punteggi. Facoltativamente, è necessario uno schema per i dati di input del servizio web. Lo script di assegnazione dei punteggi carica il file **model.pkl** dalla cartella corrente e lo usa per produrre nuove stime.

1.  Aprire l'applicazione Machine Learning Workbench. Quindi aprire il **myIris** progetto creato nella parte precedente della serie di esercitazioni.

2.  Dopo aver aperto il progetto, selezionare la **file** pulsante (icona della cartella) nel riquadro sinistro per aprire l'elenco dei file nella cartella del progetto.

3.  Selezionare il file **score_iris.py**. Verrà aperto lo script Python. Questo file viene usato come file di assegnazione dei punteggi.

    ![File di assegnazione dei punteggi](media\azure-stack-solution-machine-learning\image56.png)

1.  Per ottenere il file di schema, eseguire lo script. Selezionare l'ambiente **local** (locale) e lo script **score_iris.py** nella barra dei comandi e quindi fare clic su **Run** (Esegui).

    Questo script crea un file JSON nel **output** sezione, che consente di acquisire lo schema di dati di input richiesto dal modello.

1.  Si noti il riquadro **Jobs** (Processi) sul lato destro del riquadro **Project Dashboard** (Dashboard progetto). Attendere il processo di score_iris.py** versione più recente * * visualizzare il verde **Completed** dello stato. Fare quindi clic sul collegamento ipertestuale **score_iris.py** per l'esecuzione più recente del processo per visualizzare i dettagli dell'esecuzione.

2.  Nella sezione **Outputs** (Output) del riquadro **Run Properties** (Proprietà esecuzione) selezionare il file **service_schema.json** appena creato. Selezionare la casella di controllo accanto al nome del file e quindi fare clic su **Download** (Scarica). Salvare il file nella cartella radice del progetto.

3.  Tornare alla scheda precedente e il **score_iris. py** script. Usando la raccolta dei dati, è possono acquisire gli input del modello e le stime dal servizio web. I passaggi seguenti sono di particolare interesse per la raccolta dei dati.

4.  Esaminare il codice nella parte superiore del file, che importa la classe **ModelDataCollector**. Contiene la funzionalità di raccolta dati di modello:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Esaminare le righe di codice seguenti nella funzione **init()** che crea un'istanza di **ModelDataCollector**:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Esaminare le righe di codice seguenti nella funzione **run(input_df)** che raccoglie i dati di input e di stima:

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

A questo punto, preparare l'ambiente per rendere operativo il modello.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Passaggio 5: Distribuire e usare registro contenitori di Azure

Distribuire e usare registro contenitori di Azure.

Creare un Registro contenitori di Azure con il comando **az acr create**. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Il gruppo di risorse è lo stesso.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Accesso al registro contenitori

Usare il comando **az acr login** per accedere all'istanza del Registro contenitori di Azure. Specificare il nome univoco assegnato al registro contenitori al momento della creazione.

    ```CLI
        az acr login --name <acrName>
    ```

Il comando restituisce un ' messaggio ha avuto esito positivo di account di accesso al termine dell'esecuzione.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Preparazione per l'operazionalizzazione in locale per sviluppo e test del servizio

Uso *modalità locale* distribuzione per l'esecuzione nei contenitori Docker nel computer locale e per sviluppo e test.

Per completare la procedura seguente per rendere operativo il modello, il motore Docker deve essere eseguito in locale. Usare il `-h` flag alla fine di ogni comando per visualizzare il messaggio della Guida corrispondente.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Aprire l'interfaccia della riga di comando. In Machine Learning Workbench scegliere **Open Command Prompt** (Apri prompt dei comandi) dal menu **File**.

    Il prompt della riga di comando verrà aperto nel percorso della cartella di progetto corrente **c:\\temp\\myIris**.

1.  Verificare che il provider di risorse di Azure **Microsoft. containerregistry** viene registrato nella sottoscrizione. Registrare questo provider di risorse prima di creare un ambiente nel passaggio 3. Verificare se è già registrato con il comando seguente:

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Visualizzare il seguente output:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Se **Microsoft. containerregistry** è uso non è registrato il seguente comando:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    La registrazione può richiedere alcuni minuti Controllare lo stato della registrazione tramite il precedente **elenco di provider di az** comando o il comando seguente:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    Nella terza riga dell'output viene visualizzato **"registrationState": "Registering"**. Attendere alcuni istanti e ripetere il **mostrare** comandi fino a quando non viene visualizzato l'output **"registrationState": "Registered.**

1.  Creare l'ambiente. Eseguire questo passaggio una volta per ogni ambiente.

    Il seguente comando di installazione richiede l'accesso collaboratore alla sottoscrizione. Accesso come collaboratore alla distribuzione per il gruppo di risorse è obbligatorio. Specificare il nome del gruppo di risorse come parte del comando di installazione utilizzando il gflag.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Seguire le istruzioni visualizzate per effettuare il provisioning di un account di archiviazione per archiviare le immagini Docker, un registro contenitori di Azure per elencare le immagini Docker e un account Azure Application Insights per raccogliere dati di telemetria. **Se si usa il cswitch, il comando creerà anche un cluster del servizio contenitore**.

    Il nome del cluster è un modo per identificare l'ambiente. Il percorso deve essere uguale a quella dell'account Gestione modelli creato dal portale di Azure.

    Per assicurarsi che l'ambiente sia impostato correttamente, usare il comando seguente per controllare lo stato:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Assicurarsi che "Provisioning State" sia il valore "Succeeded", come illustrato, prima di configurare l'ambiente nel passaggio 5:

    ![Stato del provisioning](media\azure-stack-solution-machine-learning\image57.png)

1.  Impostare l'ambiente.

    Al termine della configurazione, usare il comando seguente per impostare le variabili di ambiente necessarie per rendere operativo l'ambiente. Usare lo stesso nome di ambiente usato in precedenza nel passaggio 3. Usare lo stesso nome del gruppo di risorse che è stato restituito come output nella finestra di comando al termine del processo di configurazione.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Per verificare la configurazione appropriata dell'ambiente reso operativo per la distribuzione del servizio web locale, immettere il comando seguente:

    ```CLI
    az ml env show
    ```

    A questo punto, creare il servizio web in tempo reale.

    > [!Note]  
    > Riutilizzare gli account di gestione modelli e l'ambiente per le distribuzioni del servizio web successive. Non è necessario crearli per ogni servizio web. A un account o un ambiente possono essere associati più servizi Web.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Creare un servizio Web in tempo reale con comandi separati

Come alternativa per il **az ml servizio creare in tempo reale** comando riportata in precedenza, questi passaggi possono essere eseguiti anche separatamente.

Registrare prima il modello. Generare quindi il manifesto, compilare l'immagine Docker e creare il servizio Web. Questo approccio graduale offre maggiore flessibilità in ogni passaggio. Inoltre, le entità generate nei passaggi precedenti possono essere riutilizzate.

1. Registrare il modello specificando il nome del file pickle

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Questo comando genera un ID modello.

2.  Creare un manifesto.

    Per creare un manifesto, usare il comando seguente e specificare l'ID modello restituito nel passaggio precedente: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Questo comando genera un ID manifesto.

3.  Creare un'immagine Docker.

    Per creare un'immagine Docker, usare il comando seguente e specificare l'ID valore manifesto restituito nel passaggio precedente. Le dipendenze conda possono essere usate anche tramite il `-c` passare. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Questo comando genera un ID immagine Docker.

2.  Creare il servizio.

    Per creare un servizio, usare il comando seguente e specificare l'ID immagine restituito nel passaggio precedente: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Questo comando genera un ID servizio Web.
    
    Successivamente, eseguire il servizio web.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Passaggio 6: Distribuire un cluster Kubernetes in Azure Stack

Distribuire un cluster Kubernetes in Azure Stack.

Kubernetes può essere installato usando i modelli di Azure Resource Manager generati dal motore di servizi contenitore di Azure (ACS) in Azure Stack. [*Kubernetes* ](https://kubernetes.io/) è un sistema open source per automatizzare la distribuzione, ridimensionamento e la gestione delle applicazioni nei contenitori. Oggetto [ *contenitore* ](https://www.docker.com/what-container) è contenuta in un'immagine, simile a una macchina virtuale. A differenza di una macchina virtuale, l'immagine del contenitore include le risorse che necessarie per eseguire un'applicazione, ad esempio il codice, eseguire il codice, librerie specifiche e le impostazioni di runtime.

Uso di Kubernetes per:

 -  Sviluppare applicazioni con architettura altamente scalabile, aggiornabile, che possono essere distribuite in pochi secondi.

 -  Semplificare la progettazione dell'applicazione e migliorare l'affidabilità da applicazioni diverse di Helm. [*Helm* ](https://github.com/kubernetes/helm) è uno strumento di creazione dei pacchetti open source che consente di installare e gestire il ciclo di vita delle applicazioni Kubernetes.

 -  Facilmente monitorare e diagnosticare l'integrità delle applicazioni con scalabilità e funzionalità di aggiornamento.

> [!Note]  
> Installazione del cluster richiederà circa un'ora, il piano in modo appropriato.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Prerequisiti per la distribuzione del cluster Kubernetes

Per iniziare, verificare le autorizzazioni e conformità di Azure Stack:

1.  Verificare che il **creare un Kubernetes Cluster (anteprima)** elemento è disponibile in Azure Stack Marketplace. Se non è presente, questo elemento di lavoro con un operatore di Azure Stack per aggiungere questo elemento nell'ambiente Azure Stack.

2.  Verificare la possibilità di creare applicazioni nel tenant di Azure Active Directory (Azure AD). Le autorizzazioni sono necessarie per la distribuzione di Kubernetes.

    Per istruzioni su come il controllo delle autorizzazioni, vedere [ *le autorizzazioni di controllo Azure Active Directory*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Generare una pubblica e privata coppia di chiavi SSH per accedere alla VM Linux in Azure Stack. Quando si crea il cluster, è necessaria la chiave pubblica. Per istruzioni, vedere [generare una chiave di autenticazione per SSH](#generate-an-authenticatio-key-for-ssh).

4.  Verificare che la sottoscrizione nel portale tenant di Azure Stack è valida e sono disponibili sufficienti IP pubblico indirizzi disponibili per l'aggiunta di nuove applicazioni.

    Il cluster non può essere distribuito in Azure Stack **amministratore** sottoscrizione. Usare un **utente** sottoscrizione.

###  <a name="generate-an-authentication-key-for-ssh"></a>Generare una chiave di autenticazione per SSH

Da usare i comandi seguenti per generare una chiave di autenticazione all'interno del sottosistema Windows per Linux sessione: 

1. Digitare: 

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Selezionare `id_rsa` quando richiesto. 
3. Creare una passphrase quando viene richiesto. È importante notare questa password per un uso successivo. 
    L'output avrà un aspetto simile al seguente: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alt text](media\azure-stack-solution-machine-learning\image58.png)

4. Dopo aver generato la chiave, incollare le informazioni sulla chiave usando i comandi seguenti: 
    ```Bash
    cat id_rsa.pub
    ```
    L'output avrà un aspetto simile al seguente: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Copiare la chiave generata in SSH pubblica campo chiave.

### <a name="create-a-service-principal-in-azure-ad"></a>Creare un'entità servizio in Azure AD

1.  Accedi a globale [ *portale di Azure*](http://www.poartal.azure.com/).

2.  Accedere con il tenant di Azure AD associato all'istanza di Azure Stack.

3.  Creare un'applicazione Azure AD.

    1. Selezionare **Azure Active Directory** > **+ registrazioni per l'App**> **registrazione nuova applicazione**.
    2. Immettere un **nome** dell'applicazione. 
    3. Selezionare **app Web / API**. 
    4. Immettere `http://localhost` per il **Sign-on di URL**. 
    5. Selezionare **Create**.

1.  Annotare l'**ID applicazione**. Questo ID è necessaria quando si crea il cluster e viene fatto riferimento come **Client ID dell'entità servizio**.

2.  Selezionare **le impostazioni** > **chiavi**.

    1. Immettere il **descrizione**. 
    2. Selezionare **non scade mai** per **Expires**. 
    3. Selezionare **Salva**. Prendere nota di stringa della chiave. La stringa della chiave è necessaria quando si crea il cluster e viene fatto riferimento come le **segreto Client entità servizio**.

### <a name="give-the-service-principal-access"></a>Concedere l'accesso dell'entità servizio

Concedere l'accesso dell'entità servizio alla sottoscrizione in modo che possono creare risorse.

1.  Accedi per il [portale di amministrazione](https://adminportal.local.azurestack.external/).

2.  Selezionare **altri servizi** > * * le sottoscrizioni utente * * > **+ Aggiungi**.

3.  Selezionare la sottoscrizione creata.

4.  Selezionare **controllo di accesso (IAM)** > selezionare **+ Aggiungi**.

5.  Selezionare il **proprietario** ruolo.

6.  Selezionare il nome dell'applicazione creato per il servizio dell'entità. Digitare il nome nella casella di ricerca, se necessario.

7.  Selezionare **Salva**.

8.  Aprire il [portale di Azure Stack](https://portal.local.azurestack.external).

9.  Selezionare **+ nuovo** > **calcolo** > **Cluster Kubernetes**. Selezionare **Create**.

    ![Distribuisci modello di soluzione](media\azure-stack-solution-machine-learning\image59.png)

10\. Selezionare **nozioni di base** in di creare un Cluster Kubernetes.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Immettere il **nome utente dell'amministratore della macchina virtuale Linux**. Nome utente per le macchine virtuali di Linux che fanno parte del cluster Kubernetes e DVM.

12. Immettere il **chiave pubblica SSH** utilizzati per l'autorizzazione a tutti i computer Linux creato come parte del cluster Kubernetes e DVM.

13. Immettere il **Master del prefisso DNS del profilo** che è univoco nell'area. Deve trattarsi di un'area univoco assegnare un nome, ad esempio `ask8s-12345`. Provare a scegliere lo stesso del gruppo di risorse nome ritiene più pratica.

    > [!Note]  
    > Per ogni cluster, usare un prefisso DNS del profilo master nuovi ed esclusivi.

14. Immettere il **conteggio profilo del Pool di agenti**. Il conteggio contiene il numero di agenti nel cluster. Può esistere da 1 a 4.

15. Immettere il **entità servizio ClientId** viene utilizzato dal provider di cloud di Azure in Kubernetes.

16. Immettere il **segreto Client entità servizio** creato durante la creazione dell'applicazione dell'entità servizio.

17. Immettere il **versione del Provider Cloud di Azure in Kubernetes**. Si tratta della versione per il provider di Azure in Kubernetes. Azure Stack rilascia una compilazione Kubernetes personalizzata per ogni versione di Azure Stack.

18. Selezionare il **sottoscrizione** ID.

19. Immettere il nome del nuovo gruppo di risorse o selezionare un gruppo di risorse esistente. Il nome della risorsa deve essere alfanumerico e lettere minuscole.

20. Selezionare il **posizione** del gruppo di risorse. Questa è l'area scelta per l'installazione di Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Specificare le impostazioni di Azure Stack

1.  Selezionare il **delle impostazioni di Azure Stack timbro**.

    ![Distribuisci modello di soluzione](media\azure-stack-solution-machine-learning\image61.png)

2.  Immettere il **Tenant di Azure Resource Manager Endpoint**. Si tratta dell'endpoint di Azure Resource Manager per la connessione per creare il gruppo di risorse per il cluster Kubernetes. L'endpoint dall'operatore Azure Stack è necessaria per un sistema integrato. Per il Azure Stack Development Kit (ASDK), usare `https://management.local.azurestack.external`.

3.  Immettere il **Tenant ID** per il tenant. Visualizzare [ *ottenere l'ID tenant* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) per trovare il valore di ID tenant.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Installare kubectl in ambiente di Windows PowerShell

Da eseguire i comandi seguenti per installare kubectl nell'ambiente di sottosistema Windows per Linux all'interno dell'ambiente di sottosistema Windows per Linux.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Installare kubectl nel sottosistema Windows per l'ambiente di Linux

All'interno dell'ambiente di sottosistema Windows per Linux, eseguire i comandi seguenti per installare kubectl nell'ambiente di sottosistema Windows per Linux.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Configurare kubectl

Affinché kubectl trovare e accedere a un cluster Kubernetes, un*file kubeconfig* è necessaria. Viene creato automaticamente quando viene creato un cluster usando kube-up.sh o distribuisce un cluster di Minikube. Vedere le [ *guide introduttive* ](https://kubernetes.io/docs/setup/) per altre informazioni sulla creazione di cluster. Per accedere a un cluster creato da un altro utente, vedere la [ *documento condivisione accesso al Cluster*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Per impostazione predefinita, si trova in configurazione di kubectl **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Controllare la configurazione di kubectl

Verificare che kubectl è configurato correttamente ottenendo lo stato del cluster:

```Bash  
kubectl cluster-info
```

kubectl è configurato correttamente per accedere al cluster quando viene visualizzata la risposta di url.

kubectl non è configurato correttamente o non in grado di connettersi a un cluster Kubernetes, se viene visualizzato il messaggio seguente:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Ad esempio, quando si esegue un cluster Kubernetes in un portatile locale, uno strumento potrebbe essere necessario (minikube o simile) per eseguire nuovamente i comandi riportati sopra.

Se cluster kubectl-info restituisce la risposta di url, ma il cluster non è comunque accessibile, verificare la corretta configurazione usando:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Abilitare il completamento automatico della shell

kubectl include il supporto di completamento automatico, rendendo l'abilitazione della shell rapido e semplice.

Script di completamento generato da kubectl e sia accessibile dal profilo.

### <a name="connect-to-the-kubernetes-cluster"></a>Connettersi al Cluster Kubernetes

Per connettersi al cluster, il client della riga di comando di Kubernetes (**kubectl**) è necessaria. Le istruzioni per la connessione a e la gestione del cluster sono disponibili [documentazione servizi contenitore di Azure.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Qualsiasi Client SSH può essere usato per la connessione al cluster Kubernetes. È consigliabile il sottosistema Windows per Linux (WSL). La macchina connettendosi al Kubernetes Cluster nel gruppo di risorse creato per il cluster sarà e verrà avviato con il prefisso di vmd-edge: Machine Learning-stack.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Una volta connessi al computer di Kubernetes, eseguire il computer seguente per ottenere i file di configurazione di Kubernetes.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

L'Output sarà simile al seguente:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Copiare queste informazioni di percorso di file e quindi eseguire il comando seguente, incollando il percorso del file kubeconfig copiato in precedenza:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

L'output sarà un grande blocco di testo, ovvero il contenuto JSON non elaborato. Copiare il testo di output e quindi incollare il codice in un file di Visual Studio, il salvataggio come file JSON. Ciò comporta un file kubeconfig.json archiviate localmente. (Salva in/mnt/c/users/ <current user> /documenti/directory Kube come kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Configurare il Cluster Kubernetes

Dopo aver ottenuto il file JSON locale, in una nuova sessione WSL, usare i comandi seguenti per configurare il Cluster.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Verranno definite le impostazioni di configurazione di Kubernetes (vedere l'output riportato di seguito).

![Alt text](media\azure-stack-solution-machine-learning\image62.png)

Avviare il servizio proxy locale:

```Bash  
kubectl proxy
```

Passare al cluster kubernetes dell'interfaccia utente all'indirizzo seguente: `https://localhost:8001`.

![Alt text](media\azure-stack-solution-machine-learning\image63.png)

È ora disponibile un punto di distribuzione di contenitori e un contenitore che si trova nel cloud che è possibile visualizzare in locale.

![Alt text](media\azure-stack-solution-machine-learning\image64.png)

Personalizzare il **iris_deployment.yaml** file (che si trova /*/mnt/c/users/<current user>/documenti o una directory Kube*) in modo **webservicename** e contenitori  **Immagine** e **nome** corrisponde la distribuzione, usando qualsiasi editor di codice preferito.

![Alt text](media\azure-stack-solution-machine-learning\image65.png)

Impostare la porta del contenitore su **5001.**

![Alt text](media\azure-stack-solution-machine-learning\image66.png)

E quindi creare la **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Creare un segreto nel cluster che contiene il token di autorizzazione

Un cluster Kubernetes Usa il segreto delle **docker-registry** tipo per l'autenticazione con un registro contenitori di effettuare il pull di un'immagine privata.

Creare il segreto, denominarlo **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Trovare:

- **< your-Registro di sistema-server >** è il registro contenitori di Azure **Login Server**.
- **< nome->** è il registro contenitori di Azure **Username**.
- **< your pword >** è il registro contenitori di Azure **Password**. Verificare che la password non è racchiuso tra virgolette.
- **< your-messaggio di posta elettronica >** è l'utente che ha accesso in lettura/scrittura al contenitore.
- Trovare le informazioni sul **contenitore di Azure** **Registro di sistema** sotto **chiavi di accesso**.
- Le credenziali di docker a questo punto vengono impostate nel cluster come un segreto denominato **azuremlcr**.

Salvare il **iris_deployment.yaml** file (che si trova /*/mnt/c/users/<current user>directoryKube/documenti/*).

### <a name="create-the-kubernetes-container"></a>Creare il contenitore Kubernetes

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Controllare lo stato della distribuzione:

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

La distribuzione può richiedere tempo.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Configurare Visual Studio Team Services per la distribuzione automatica

#### <a name="create-a-team-project"></a>Creare un progetto team

1.  Assicurarsi che [l'appartenenza al gruppo Project Collection Administrators.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Per creare progetti team, **crea nuovi progetti** autorizzazione deve essere impostata su **Consenti**.

2.  Nella pagina progetti, scegliere **nuovo progetto**.

    ![Alt text](media\azure-stack-solution-machine-learning\image69.png)

1.  Denominare il progetto **HybridMLIris**.

2.  Selezionare il tipo di controllo relativa origine iniziale come **Git**

3.  Selezionare un processo e selezionare **Create**.

    ![Alt text](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importazione di codice crea Repository

È necessario un repository Git per il codice YAML.

#### <a name="add-user-to-the-git-repo"></a>Aggiungere utenti al repository GIT

1.  Dal dashboard del progetto predefinito, selezionare le credenziali Git generare.

    ![Alt text](media\azure-stack-solution-machine-learning\image71.png)

1.  Immettere la password ove necessario e salvare le credenziali Git.

    ![Alt text](media\azure-stack-solution-machine-learning\image72.png)

1.  Inizializzare il repository selezionando il **inizializzare** pulsante e la creazione di un **README** file.

    ![Alt text](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Clonare il repository Git locale e caricare il codice. 

1.  Creare una directory nel computer in `c:\\users\\<User>\\source\\repos\\hybridMLIris`e clonare il repository

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image74.png)

1.  Passare al repository appena clonato:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Copia il **iris_deployment.yaml** file nel repository.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Il commit della modifica in GIT

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Preparare la compilazione privata e l'agente di rilascio per l'integrazione con Visual Studio Team Services

#### <a name="prerequisites"></a>Prerequisiti

Visual Studio Team Services esegue l'autenticazione con Azure Resource Manager usando un'entità servizio. Per Visual Studio Team Services essere in grado di effettuare il provisioning di risorse in una sottoscrizione di Azure Stack richiede lo stato di collaboratore. \ **di seguito sono riportati i passaggi più importanti che devono essere configurate per abilitare questo tipo di autenticazione:**

1.  Entità servizio deve essere creato o può essere usato uno esistente.

2.  Le chiavi di autenticazione devono essere creati per l'entità servizio.

3.  Sottoscrizione di Azure Stack deve essere convalidata tramite il controllo di accesso basato sui ruoli per consentire il nome SPN di far parte del ruolo Contributors.

4.  Una nuova definizione di servizio in Visual Studio Team Services deve essere creata usando l'endpoint di Azure Stack, nonché informazioni di nome SPN.

#### <a name="service-principal-creation"></a>Creazione dell'entità servizio

Vedere le [istruzioni per la creazione dell'entità servizio](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) per creare un'entità servizio e scegliere l'App Web/API per il tipo di applicazione.

**Creazione della chiave di accesso**

Un'entità servizio è necessaria una chiave per l'autenticazione, seguire i passaggi descritti in questa sezione per generare una chiave.

1.  Dal **registrazioni per l'App** in Azure Active Directory, selezionare l'applicazione.

    ![Selezionare l'applicazione](media\azure-stack-solution-machine-learning\image77.png)

1.  Prendere nota del valore di **ID applicazione. Il valore viene utilizzato quando si configura l'endpoint del servizio in Visual Studio Team Services.**

    ![Alt text](media\azure-stack-solution-machine-learning\image78.png)

1.  Per generare una chiave di autenticazione, selezionare **Impostazioni**.

    ![selezionare impostazioni](media\azure-stack-solution-machine-learning\image79.png)

1.  Selezionare **Chiavi**.

    ![Selezionare Chiavi](media\azure-stack-solution-machine-learning\image80.png)

1.  Specificare una descrizione e una durata per la chiave. Al termine scegliere **Salva**.

    ![Salvare la chiave](media\azure-stack-solution-machine-learning\image81.png)

Dopo aver salvato la chiave viene visualizzato il valore della chiave. Copiare questo valore, quando è necessario in un secondo momento. Il **valore della chiave** con l'applicazione è necessario l'ID per l'accesso dell'applicazione. Store il valore della chiave in cui l'applicazione possa recuperarlo.

![Alt text](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Ottenere l'ID Tenant

Come parte della configurazione dell'endpoint servizio, Visual Studio Team Services richiede la **Tenant ID** che corrisponde alla Directory di AAD è stata distribuita l'indicatore di Azure Stack. Seguire i passaggi descritti in questa sezione per raccogliere l'ID Tenant.

1.  Selezionare **Azure Active Directory**.

    ![Selezionare Azure Active Directory](media\azure-stack-solution-machine-learning\image83.png)

1.  Per ottenere l'ID tenant, selezionare **proprietà** per il tenant di Azure AD.

    ![selezionare le proprietà di Azure AD](media\azure-stack-solution-machine-learning\image84.png)

1.  Copiare l'**ID directory**. Questo valore è l'ID tenant.

    ![ID tenant](media\azure-stack-solution-machine-learning\image85.png)

Concedere i diritti di entità servizio per distribuire le risorse della sottoscrizione di Azure Stack

Per accedere alle risorse nella sottoscrizione, assegnare l'applicazione a un ruolo. Decidere quale ruolo rappresenti le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Impostare l'ambito a livello di sottoscrizione, gruppo di risorse o risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Aggiunta di un'applicazione al ruolo lettore per un gruppo di risorse consente ad esempio leggere il gruppo di risorse e le risorse in che esso contenute.

1.  Passare al livello desiderato di ambito per assegnare l'applicazione. Ad esempio, per assegnare un ruolo a un ambito della sottoscrizione, selezionare **Sottoscrizioni**.

    ![Alt text](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Selezionare il **sottoscrizione** (gruppo di risorse o risorsa) per assegnare l'applicazione.

    ![selezionare la sottoscrizione per l'assegnazione](media\azure-stack-solution-machine-learning\image87.png)

1.  Selezionare **Controllo di accesso (IAM)**.

    ![selezionare accesso](media\azure-stack-solution-machine-learning\image88.png)

1.  Selezionare **Aggiungi**.

    ![selezionare aggiungi](media\azure-stack-solution-machine-learning\image89.png)

1.  Selezionare il ruolo per assegnare l'applicazione. La figura seguente mostra le **proprietario** ruolo.

    ![Alt text](media\azure-stack-solution-machine-learning\image90.png)

1.  Per impostazione predefinita, le applicazioni di Azure Active Directory non sono visualizzate nelle opzioni disponibili. Per trovare l'applicazione **consente di specificare il nome** nella ricerca campo e selezionarlo.

    ![Alt text](media\azure-stack-solution-machine-learning\image91.png)

1.  Selezionare **Salva** per completare l'assegnazione del ruolo. L'applicazione viene visualizzata nell'elenco di utenti assegnati a un ruolo per quell'ambito.

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Azure in base al ruolo di controllo di accesso (RBAC) consente la gestione accessi con granularità fine per Azure e Azure Stack. Uso di RBAC, può essere concesso solo il livello di accesso che gli utenti devono svolgere il proprio lavoro. Per altre informazioni sul controllo degli accessi in base al ruolo, vedere [gestire l'accesso alle risorse della sottoscrizione Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**Pool di agenti di Visual Studio Team Services**

Anziché gestire singolarmente ogni agente, gli agenti sono organizzati nelle **pool di agenti**. Un pool di agenti definisce il limite di condivisione per tutti gli agenti nel pool. In Visual Studio Team Services, i pool di agenti sono limitati all'account di Visual Studio Team Services, in modo che possono essere condivise tra progetti team. Per altre informazioni e per un'esercitazione sulla creazione di Visual Studio Team Services i pool di agenti vedere [creare pool di agenti e le code.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Aggiungere il token di accesso aPersonal (PAT) per Azure Stack**

 -  Accedere a Visual Studio Team Services account e selezionare in **profilo di account** nome.

 -  Selezionare **Gestisci sicurezza** alla pagina di creazione del token di accesso.

![Alt text](media\azure-stack-solution-machine-learning\image92.png)

![Alt text](media\azure-stack-solution-machine-learning\image93.jpeg)

![Alt text](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Ottenere le informazioni del token. Non essere visualizzato dopo aver lasciando questa schermata.

1.  Copia il **token**.

    ![Alt text](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installazione che dell'agente di compilazione di Visual Studio Team Services in Azure Stack in un Server di compilazione ospitato

1.  Connettere il **Server di compilazione** distribuite nell'host Azure Stack.

    > [!Note]  
    > Assicurarsi che Azure Stack ospitati Server di compilazione è accessibile dalla rete Internet pubblica. In caso contrario, usare un operatore di Stack di Azure per ottenere l'accesso.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Aggiornare il Server di compilazione Ubuntu alla versione più recente (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Questa operazione richiederà del tempo.

2.  Installare le applicazioni pre-req per il server di compilazione. Da bash shell di Ubuntu basata esecuzione Server di compilazione i comandi seguenti:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Scaricare e distribuire l'agente di compilazione come un servizio usando un **token di accesso personale (PAT)** ed eseguite con l'account amministratore della macchina virtuale.

    ![Alt text](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Passare alla cartella dell'agente di compilazione estratti. Eseguire il codice seguente.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image97.png)

2.  Dopo aver **./config.sh**finiti, eseguire il codice seguente per abilitare il servizio all'avvio del server e avviare il servizio:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

L'agente è ora visibile nella cartella Visual Studio Team Services.

#### <a name="endpoint-creation-permissions"></a>Autorizzazioni di creazione di endpoint

Gli utenti possono creare gli endpoint in modo che le compilazioni VSTO è possono distribuire le app di servizio di Azure allo stack. Visual Studio Team Services si connette all'agente di compilazione, che quindi si connette con Azure Stack.

![Alt text](media\azure-stack-solution-machine-learning\image98.png)

1.  Nel **le impostazioni** dal menu **sicurezza**.

2.  Nel **gruppi VSTS** elenco a sinistra, seleziona **creatori di Endpoint**.

    ![Alt text](media\azure-stack-solution-machine-learning\image99.png)

3.  Nel **scheda membri** selezionare la **+ Aggiungi**.

    ![Alt text](media\azure-stack-solution-machine-learning\image100.png)

1.  Tipo di **username** e selezionare il nome utente dall'elenco.

2.  Selezionare **Save changes** (Salva modifiche).

    ![Alt text](media\azure-stack-solution-machine-learning\image101.png)

3.  Nel **gruppi VSTS** elenco a sinistra, seleziona **amministratori Endpoint**.

4.  Nel **scheda membri** selezionare la **+ Aggiungi**.

5.  Digitare un **nome utente** e selezionare l'utente dall'elenco.

6.  Selezionare **salvare le modifiche.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    L'agente di compilazione nello Stack di Azure ottenga le istruzioni da VSTS, che comunica quindi informazioni sull'endpoint per la comunicazione con Azure Stack.

    Visual Studio Team Services alla connessione di Azure Stack è pronto.

    ![Alt text](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>Configurare le definizioni di compilazione e versione

Ora che le connessioni vengono stabilite, verrà manualmente mapping dell'endpoint di Azure creato, AKS e registro contenitori di Azure per la compilazione e definizioni di versione.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Creare la definizione di compilazione per il codice YAML The

1.  Selezionare la sezione si basa in hub di compilazione e rilascio e creare una nuova definizione.

    ![Alt text](media\azure-stack-solution-machine-learning\image104.png)

1.  Selezionare VSTS Git e selezionare il repository creato in precedenza.

    ![Alt text](media\azure-stack-solution-machine-learning\image105.png)

1.  Selezionare Pipeline vuota come modello

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nome della compilazione **dell'elemento di copia** e selezionare il Server di compilazione di Azure Stack per la coda agente.

    ![Alt text](media\azure-stack-solution-machine-learning\image107.png)

1.  Selezionare la fase 1 in processi e rinominarlo **dell'elemento di copia**, quindi **aggiungere un'attività** alla fase:

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

1.  Selezionare **pubblica artefatti di compilazione** dalle **utilità** elencare e selezionare **Add**.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Selezionare il **percorso per pubblicare** e selezionare il **iris_deployment.yaml** file.

    ![Alt text](media\azure-stack-solution-machine-learning\image110.png)

1.  Denominare l'artefatto **iris_deployment** e selezionare la località di pubblicazione sia **Visual Studio Team Services/TFS**.

    ![Alt text](media\azure-stack-solution-machine-learning\image111.png)

1.  Selezionare **Salva e accoda**.

    ![Alt text](media\azure-stack-solution-machine-learning\image112.png)

1.  Controllare lo stato della compilazione, selezionare l'ID compilazione.

    ![Alt text](media\azure-stack-solution-machine-learning\image113.png)

Esito positivo avrà un aspetto simile al seguente:

![Alt text](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Creare la definizione di versione per il codice YAML

1.  Selezionare la sezione relativa alle versioni in hub di compilazione e rilascio, una nuova definizione

    ![Alt text](media\azure-stack-solution-machine-learning\image115.png)

1.  Selezionare Pipeline vuota come modello.

    ![Alt text](media\azure-stack-solution-machine-learning\image106.png)

1.  Nome ambiente Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image116.png)

1.  Aggiungere un nuovo elemento selezionando **artefatti** e **+ Aggiungi**

2.  Selezionare compilazione come il tipo di origine e **HybridMLIris** del progetto.

3.  Selezionare quindi la definizione di compilazione creata in precedenza per l'origine.

4.  Quindi selezionare **aggiungere**.

    ![Alt text](media\azure-stack-solution-machine-learning\image117.png)

1.  Selezionare gli ambienti Azure Stack, quindi aggiungere una nuova attività in Azure Stack

    ![Alt text](media\azure-stack-solution-machine-learning\image118.png)


1.  Nella fase agente, impostare la coda dell'agente per il Server di compilazione ospitato di Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image119.png)

1.  Aggiungere una nuova attività per questa fase, selezionare la distribuzione all'attività di Kubernetes in distribuzione e scegliere Aggiungi.

    ![Alt text](media\azure-stack-solution-machine-learning\image120.png)


1.  Denominarlo **Kubectl Apply** (nome predefinito) e selezionare il comando apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image121.png)

    Ora creare una nuova connessione al servizio Kubernetes.

#### <a name="create-kubernetes-service-endpoint"></a>Creare Endpoint di servizio di Kubernetes

1.  Nel gruppo di connessione del servizio Kubernetes, selezionare la **+ nuovo** e selezionare**Kubernetes**dall'elenco. è possibile usare questo endpoint per la connessione di**VSTS**e il**del servizio contenitore di Azure (AKS)**.

2.  **Nome della connessione**: fornire il nome della connessione.

3.  **URL del server**: fornire l'indirizzo del servizio contenitore nel formathttp: / / {indirizzo del server API}

4.  **File Kubeconfig**: per ottenere il valore Kubeconfig, eseguire i seguenti comandi di Azure in un prompt dei comandi aperto con i privilegi di amministratore.

    > [!Important]  
    > Usare questa finestra di comando per completare i passaggi successivi.

6.  Accedere ad Azure nella finestra dell'interfaccia della riga di comando. [Altre informazioni su az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  Al prompt dei comandi immettere:

    ```CLI
        az login
    ```

10. Questo comando restituisce un codice da usare nel browser alla <https://aka.ms/devicelogin>.

11. Passare a <https://aka.ms/devicelogin> nel browser. Quando richiesto, immettere il codice, della riga di comando, ricevuto nel browser.

    ![Endpoint del servizio di Kubernetes](media\azure-stack-solution-machine-learning\image122.png)

1.  Digitare il comando seguente al prompt dei comandi per ottenere le credenziali di accesso per il cluster Kubernetes.

### <a name="azure-ml-workbench-cli"></a>Comando di Azure Machine Learning Workbench

AZ aks get-credentials-gruppo di risorse <yourResourceGroup> nome <yourazurecontainerservice>

![Endpoint del servizio di Kubernetes](media\azure-stack-solution-machine-learning\image123.png)

1.  Passare al **.kube**cartella sotto la home directory (ad esempio: c:\\utenti\\<user>\\documenti\\Kube)

2.  Copiare il contenuto del**config**file e incollarlo nella finestra di connessione a Kubernetes. Selezionare il**OK**pulsante.

    ![Endpoint del servizio di Kubernetes](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Dopo aver creato e selezionato l'endpoint di Kubernetes, selezionare la casella di controllo file di configurazione di utilizzo per aggiungere un file di configurazione. Selezionare quindi il file iris_deployment.yaml negli artefatti collegati.

    ![Alt text](media\azure-stack-solution-machine-learning\image125.png)

    ![Alt text](media\azure-stack-solution-machine-learning\image126.png)

4.  Salvare la definizione di versione.

#### <a name="check-the-status-of-the-release-definition"></a>Controllare lo stato della definizione di versione. 

Dopo aver salvato, la definizione di versione di Visual Studio Team Services deve avviare automaticamente un rilascio.

Controllare lo stato della distribuzione, eseguire un comando rapido al prompt dei comandi WSL, quindi verifica la UI Kubernetes.

```Bash  
kubectl get deployments
```

L'output dovrebbe essere simile al seguente, mentre nel processo di distribuzione.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Di kubernetes in esecuzione dell'interfaccia utente, passare alla distribuzione da parte [ **https://localhost:8001/** ](https://localhost:8001/) quindi passare al **carichi di lavoro -> set di repliche**.

![Alt text](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>Distribuire il servizio YAML

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Caricare il iris_service.yaml il repository e sincronizzazione delle modifiche

1.  Passare al repository appena clonato:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alt text](media\azure-stack-solution-machine-learning\image75.png)

1.  Copia il **iris_service.yaml** file nel repository.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Il commit della modifica in GIT

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alt text](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Aggiornare la definizione di compilazione per il codice YAML The

1.  Selezionare la sezione si basa in hub di compilazione e rilascio e la definizione creata in precedenza.

    ![Alt text](media\azure-stack-solution-machine-learning\image130.png)

2.  Selezionare il pulsante Modifica per modificare la definizione.

    ![Alt text](media\azure-stack-solution-machine-learning\image131.png)

3.  **Aggiungere un'attività** alla fase. Selezionare **pubblica artefatti di compilazione** dalle **utilità** elencare e selezionare **Add**.

    ![Alt text](media\azure-stack-solution-machine-learning\image108.png)

4.  Denominarlo **Kubectl Apply** (nome predefinito) e selezionare il comando apply.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Aggiornare la definizione di versione per il codice YAML

1.  Selezionare la sezione theReleases in hub di compilazione e versione e la definizione di versione creata in precedenza. Selezionare quindi il collegamento di modifica.

    ![Alt text](media\azure-stack-solution-machine-learning\image132.png)

1.  Selezionare l'ambiente **Azure Stack** quindi aggiungere una nuova attività in Azure Stack.

    ![Alt text](media\azure-stack-solution-machine-learning\image133.png)

1.  Aggiungi un **nuova attività** a questa fase, selezionare la **Distribuisci in Kubernetes** attività sotto **Distribuisci** e selezionare **Aggiungi**.

    ![Alt text](media\azure-stack-solution-machine-learning\image134.png)

1.  Denominarlo **Kubectl Apply** (nome predefinito) e selezionare il comando apply.

    ![Alt text](media\azure-stack-solution-machine-learning\image109.png)

1.  Impostare la connessione Kubernates Service per la connessione di Stack di Azure creata in precedenza e quindi selezionare il **usare i file di configurazione** casella di controllo per aggiungere un file di configurazione. Individuare il file iris_service.yaml negli artefatti collegati.

    ![Alt text](media\azure-stack-solution-machine-learning\image135.png)


    ![Alt text](media\azure-stack-solution-machine-learning\image136.png)

1.  Salvare la definizione di versione.

#### <a name="check-the-status-of-the-release-definition"></a>Controllare lo stato della definizione di versione

Dopo aver salvato, la definizione di versione di Visual Studio Team Services deve avviare automaticamente un rilascio.

Controllare lo stato della distribuzione, eseguire un comando rapido al prompt dei comandi WSL, quindi verifica la UI Kubernetes.

```Bash  
kubectl get deployments
```

L'output dovrebbe essere simile al seguente, mentre nel processo di distribuzione.

![Alt text](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Di kubernetes in esecuzione dell'interfaccia utente, passare alla distribuzione da parte [ **https://localhost:8001/** ](https://localhost:8001/) quindi passare al **carichi di lavoro -> set di repliche**.

![Alt text](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Assegnazione dei punteggi di Kubernetes e convalida

Avviare l'interfaccia utente Kubernetes

```Bash  
kubectl proxy
```

Individuare la UI Kubernetes quindi, passare a **distribuzioni** -> **Iris-Deployment** -> **nuovo Set di repliche**  ->  **Iris-distribuzione-xxxxxxxxx** (dove la x sono l'ID di distribuzione).

![Alt text](media\azure-stack-solution-machine-learning\image138.png)

Quindi passare al **Services** e selezionare il **endpoint esterno** il funzionamento del servizio da convalidare.

![Alt text](media\azure-stack-solution-machine-learning\image139.png)

Dovrebbe essere visualizzato un messaggio di convalida simile a quello riportato di seguito:

![Alt text](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Creare App per le funzioni di assegnazione dei punteggi nel portale di Azure Stack Azure Stack

App per le funzioni necessarie per ospitare l'esecuzione di ogni funzione. App per le funzioni consente il raggruppamento come un'unità logica per la gestione, distribuzione e la condivisione delle risorse (funzione).

1.  Il portale utenti di Azure Stack, selezionare la **+ nuovo** pulsante trovato nell'angolo superiore sinistro, quindi selezionare**Web e dispositivi mobili** >**App per le funzioni**.

    ![Alt text](media\azure-stack-solution-machine-learning\image141.png)

1.  Il nome della funzione **ad eccezione delle funzioni data** e inserirlo nello stesso gruppo di risorse con i rimanenti di Machine Learning contenuto. Consentire allo strumento di creazione automatica di un nuovo piano di servizio app per l'utilizzo e usare l'account di archiviazione creato in precedenza per l'archiviazione delle app.

    ![Definire nuove impostazioni dell'app (funzione)](media\azure-stack-solution-machine-learning\image142.png)

1.  Selezionare**Create**per effettuare il provisioning e distribuire l'app per le funzioni.

2.  Selezionare l'icona di notifica nell'angolo superiore sinistro del portale e controllare la**distribuzione ha avuto esito positivo** messaggio.

    ![Definire nuove impostazioni dell'app per le funzioni](media\azure-stack-solution-machine-learning\image143.png)

1.  Selezionare**Vai alla risorsa** per visualizzare la nuova app per le funzioni.

    ![Alt text](media\azure-stack-solution-machine-learning\image144.png)

1.  Creare una nuova funzione selezionando **funzioni**, il **+ nuova funzione** pulsante.

    ![Alt text](media\azure-stack-solution-machine-learning\image145.png)

1.  Selezionare il Trigger HTTP

    ![Alt text](media\azure-stack-solution-machine-learning\image146.png)

1.  Selezionare **C\#**  come linguaggio e il nome di funzione: **pulire dati di punteggio**e impostare il livello di autorizzazione **Anonymous**.

    ![Alt text](media\azure-stack-solution-machine-learning\image147.png)

1.  Copiare e incollare il contenuto dell'esempio di codice per pulire dati di punteggio alla funzione.

    ![Alt text](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Usare Postman per convalidare le funzioni

Per assicurarsi di che avere configurato il Kbernetes e funzioni correttamente è possibile utilizzare l'app Postman per testare e convalidare gli schemi e le funzioni. Per avviare questo processo, è innanzitutto necessario raccogliere alcune informazioni dall'istanza di Kubernetes.

1.  Individuare la UI Kubernetes quindi, passare a **distribuzioni** -> **Iris-Deployment** -> **nuovo Set di repliche**  ->  **Iris-distribuzione-xxxxxxxxx** (dove la x sono l'ID di distribuzione)

    ![Alt text](media\azure-stack-solution-machine-learning\image138.png)

1.  Quindi passare al **Services** e copiare la **endpoint esterno**.

    ![Alt text](media\azure-stack-solution-machine-learning\image149.png)

1.  Scaricare e installare l'app Postman [qui](https://www.getpostman.com/apps) se necessario.

2.  Accedi per l'App Postman e chiudere la finestra di dialogo Nuovo file.

    ![Alt text](media\azure-stack-solution-machine-learning\image150.png)

1.  All'interno dell'app postman, selezionare POST...

    ![Alt text](media\azure-stack-solution-machine-learning\image151.png)

1.  Incollare il **endpoint esterno** URL nell'app postman nel **URL richiesta** aggiunta  **\\punteggio** alla fine dell'URL come illustrato di seguito.

    ![Alt text](media\azure-stack-solution-machine-learning\image152.png)

1.  Selezionare il **corpo** scheda e quindi i tipo di dati come **raw**, quindi **JSON**.

    ![Alt text](media\azure-stack-solution-machine-learning\image153.png)

1.  Da un web browser, passare a **endpoint esterno**. Aggiungere il codice seguente all'URL **/swagger.json** ciò comporta il file Swagger Services usato per testare il programma di installazione.

    ![Alt text](media\azure-stack-solution-machine-learning\image154.png)

1.  Copiare l'esempio elencato nel **swagger** file.

2.  Nell'App Postman, incollare l'esempio nel corpo del Post, quindi selezionare **inviare**. Deve restituire un valore simile a quello riportato di seguito.

    ![Alt text](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Passaggio 7: Creare un account di archiviazione di Azure Stack e una coda di archiviazione

Creare un account di archiviazione di Azure Stack e una coda di archiviazione per i dati.

1.  Accedi al portale utenti di Azure Stack. (Ogni Stack di Azure dispone di un portale univoco URL)

2.  Nel portale di Azure Stack utente, espandere il menu a sinistra per aprire il menu dei servizi e scegliere **tutti i servizi**. Scorrere verso il basso **memorizzazione** e scegliere **account di archiviazione**. Nel **gli account di archiviazione** finestra scegliere **Add**.

3.  Immettere un nome per l'account di archiviazione.

4.  Selezionare l'opzione di replica dell'account di archiviazione: **archiviazione con ridondanza locale**.

5.  Specificare un nuovo gruppo di risorse o selezionarne uno esistente.

6.  Selezionare **locale** per il percorso per l'account di archiviazione.

7.  Selezionare**Create**per creare l'account di archiviazione.

    ![Alt text](media\azure-stack-solution-machine-learning\image156.png)

1.  Scegliere l'account di archiviazione creato di recente.

2.  Select on**code**.

    ![Alt text](media\azure-stack-solution-machine-learning\image157.png)

1.  Selezionare sul **+ coda** e assegnare un nome di coda e selezionare **OK.**

    ![Alt text](media\azure-stack-solution-machine-learning\image158.png)

1.  Ottenere il **stringa di connessione** per la coda di archiviazione e copiarlo.

    ![Alt text](media\azure-stack-solution-machine-learning\image159.png)

1.  Passare all'App di funzione di Azure e quindi selezionare **le impostazioni dell'applicazione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image160.png)

1.  All'interno delle impostazioni dell'applicazione dell'App per le funzioni, scorrere verso il basso le impostazioni dell'applicazione e selezionare **+ Aggiungi nuova impostazione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image161.png)

1.  Immettere il nome dell'account di archiviazione nel **nome** campo, aggiungendo alla fine; archiviazione

In questo modo l'applicazione per comprendere che si tratta di un endpoint dell'account di archiviazione.

1.  Quindi incollare la stringa di connessione nel **valore** campo.

    ![Alt text](media\azure-stack-solution-machine-learning\image162.png)

1.  Scorrere verso l'alto delle impostazioni dell'applicazione e selezionare **salvare**.

    ![Alt text](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Aggiornare la funzione di assegnazione dei punteggi per l'utilizzo della coda di archiviazione

1.  Selezionare sul **integrare** nella funzione e deselezionare le **ottenere** opzione.

2.  Selezionare **Salva**.

3.  Quindi selezionare **+ nuovo Output** dall'output.

    ![Alt text](media\azure-stack-solution-machine-learning\image164.png)

1.  Quindi selezionare **archiviazione code di Azure** e scegliere **seleziona**.

    ![Alt text](media\azure-stack-solution-machine-learning\image165.png)

1.  Aggiornamento di **nome coda** alla coda di archiviazione creato in precedenza e quindi impostare il **connessione dell'Account di archiviazione** per la connessione dell'account di archiviazione creata precedente e selezionare **salvare.**

    ![Alt text](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Passaggio 8: Creare una funzione per gestire i dati puliti

Creare una nuova funzione di Azure Stack per spostare i dati puliti dallo Stack di Azure in Azure.

1.  Creare una nuova funzione selezionando **funzioni**, il **+ nuova funzione** pulsante.

    ![Alt text](media\azure-stack-solution-machine-learning\image167.png)

1.  Selezionare **Trigger Timer**.

    ![Alt text](media\azure-stack-solution-machine-learning\image168.png)

1.  Selezionare **C\#**  come linguaggio e il nome di funzione: **upload-to-azure** e impostare la pianificazione **0 0 \*/1 \* \* \***  che in CRON, in notazione è ogni ora.

    ![Alt text](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Ottiene la stringa di connessione per l'Account di archiviazione ospitato di Azure

1.  Passare a <https://portal.azure.com> e quindi selezionare il **Account di archiviazione Azure** creato in precedenza.

2.  Selezionare **chiavi di accesso**, quindi copiare la **stringa di connessione** per l'Account di archiviazione.

    ![Alt text](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Aggiornare il caricamento ad azure usare archiviazione di Azure ospitati (funzione)

1.  Passare all'App di funzione di Azure e quindi selezionare **le impostazioni dell'applicazione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image171.png)

1.  All'interno delle impostazioni dell'applicazione dell'App per le funzioni, scorrere verso il basso le impostazioni dell'applicazione e selezionare **+ Aggiungi nuova impostazione**.

    ![Alt text](media\azure-stack-solution-machine-learning\image172.png)

1.  Immettere il nome dell'account di archiviazione nel **nome** campo, aggiungendo alla fine; archiviazione

In questo modo l'applicazione per comprendere che si tratta di un endpoint dell'account di archiviazione.

1.  Quindi incollare il Azure ospitati Storage Account stringa di connessione nel **valore** campo.

    ![Alt text](media\azure-stack-solution-machine-learning\image173.png)

1.  Scorrere verso l'alto delle impostazioni dell'applicazione e selezionare **salvare**.

    ![Alt text](media\azure-stack-solution-machine-learning\image174.png)

1.  Tornare al **caricamento in azure** (funzione).

2.  Selezionare sul **integrare** nella funzione.

3.  Quindi selezionare **+ nuovo Output** dall'output.

    ![Alt text](media\azure-stack-solution-machine-learning\image175.png)

1.  Quindi selezionare **archiviazione Blob di Azure** e scegliere **seleziona**.

    ![Alt text](media\azure-stack-solution-machine-learning\image176.png)

1.  Aggiornamento di **percorso** al contenitore di archiviazione creato in precedenza nel formato seguente: **uploadeddata / {rand-guid}. txt**e quindi impostare il **connessione dell'Account di archiviazione** per il Connessione dell'account di archiviazione di Azure creato in versioni precedenti e selezionare **salvare.**

    ![Alt text](media\azure-stack-solution-machine-learning\image177.png)

1.  Copiare e incollare il contenuto dell'esempio di codice per un **caricamento in azure** alla funzione.

2.  Modificare in base alle esigenze in modo da puntare alla stringa di connessione di account di archiviazione.

3.  Salvare ed eseguire il codice.

    ![Alt text](media\azure-stack-solution-machine-learning\image178.png)

1.  Verificare l'account di archiviazione ospitati di Azure per visualizzare i dati è stato analizzato backup nel cloud di Azure: esito positivo avrà un aspetto simile di sotto.

    ![Alt text](media\azure-stack-solution-machine-learning\image179.png)

I dati sono stato devono essere purificati dei dati riservati da Azure Stack ospitato Kubernetes Machine Learning e caricati nel Cloud pubblico di Azure dallo Stack di Azure dall'ambiente locale, tramite Azure Stack ospitato App per le funzioni e può gestire in modo temporaneo i dati per il caricamento nel disconnesso/perimetrale scenario.

## <a name="next-steps"></a>Passaggi successivi

 - Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).