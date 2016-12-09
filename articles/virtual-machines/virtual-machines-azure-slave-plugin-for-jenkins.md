---
title: Come usare il plug-in slave di Azure con una soluzione di integrazione continua Jenkins | Microsoft Docs
description: Descrive come usare il plug-in slave di Azure con una soluzione di integrazione continua Jenkins.
services: virtual-machines-linux
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 09233bd4-957d-41bf-bccc-9dd2355ed1bf
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 10/19/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a40d829c65067dba90a212390e858b7995452b6


---
# <a name="how-to-use-the-azure-slave-plugin-with-jenkins-continuous-integration"></a>Come usare il plug-in slave di Azure con una soluzione di integrazione continuata Jenkins
Il plug-in slave di Azure per Jenkins permette di eseguire più facilmente il provisioning di nodi slave in Azure quando si eseguono compilazioni distribuite, oltre a supportare la creazione di:

* Slave di Windows nel cloud di Azure usando SSH e il protocollo JNLP (Java Network Launch Protocol)
  
  * Per avviare un'immagine di Windows tramite SSH, l'immagine dovrà essere preconfigurata con SSH.
  * Per informazioni sulla preparazione di un'immagine Windows personalizzata, vedere [Come acquisire una macchina virtuale Windows nel modello di distribuzione di Resource Manager][windows-image-capture].
* Slave Linux nel cloud di Azure usando SSH
  
  * Per informazioni sulla preparazione di un'immagine Linux personalizzata, vedere [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][linux-image-capture].

### <a name="prerequisites"></a>Prerequisiti
Prima di eseguire le procedure descritte in questo articolo, è necessario registrare e autorizzare l'applicazione client e quindi recuperare l'ID e il segreto client che verranno inviati ad Azure Active Directory durante l'autenticazione. Per altre informazioni su questi prerequisiti, vedere gli articoli seguenti:

* [Integrazione di applicazioni con Azure Active Directory][integrate-apps-with-AAD]
* [Registrare un'app client][register-client-app]

Inoltre, per completare i passaggi indicati nella sezione [Creare un processo Jenkins eseguito su un nodo slave in Azure](#create-jenkins-project) di questo articolo, è necessario disporre di una configurazione di progetto su GitHub.

<a name="install-azure-slave-plugin"></a>

## <a name="install-the-azure-slave-plugin"></a>Installare il plug-in slave di Azure
1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
   
    ![Manage Jenkins (Gestisci Jenkins)][jenkins-dashboard-manage]
2. Nella pagina **Manage Jenkins** (Gestisci Jenkins) fare clic su **Manage Plugins** (Gestisci plug-in).
   
    ![Manage Plugins (Gestisci plug-in)][jenkins-manage-plugins]
3. Fare clic sulla scheda **Available** (Disponibili) e immettere "Azure" come filtro, quindi selezionare **Azure Slave Plugin** (Plug-in slave di Azure). 
   
    ![Azure Slave Plugin (Plug-in slave di Azure)][search-plugins]
   
    Se si sceglie di scorrere l'elenco di plug-in disponibili, si troverà il plug-in Azure slave nella sezione **Cluster Management and Distributed Build** (Gestione cluster e compilazione distribuita).
4. Fare clic su **Install without restart** (Installa senza riavviare) oppure su **Download now and install after restart** (Scarica subito e installa dopo il riavvio).
   
    ![Install Plugin (Installa plug-in)][install-plugin]

Dopo aver installato il plug-in, i passaggi successivi consistono nel configurarlo con il profilo della sottoscrizione di Azure e nel creare un modello che verrà usato durante la creazione della macchina virtuale per il nodo slave.

## <a name="configure-the-azure-slave-plugin-to-use-your-subscription-profile"></a>Configurare il plug-in Azure slave per usare il profilo della propria sottoscrizione
Un profilo di sottoscrizione, noto anche come impostazioni di pubblicazione, è un file XML contenente le credenziali protette e alcune informazioni aggiuntive necessarie per usare Azure nel proprio ambiente di sviluppo. Per configurare il plug-in slave di Azure sono necessari i seguenti elementi:

* ID sottoscrizione personale
* Un certificato di gestione per la sottoscrizione

Tali informazioni sono disponibili nel [profilo di sottoscrizione], che potrebbe essere simile all'esempio seguente:

        <?xml version="1.0" encoding="utf-8"?>
        <PublishData>
            <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">
                <Subscription
                    ServiceManagementUrl="https://management.core.windows.net"
                    Id="<Subscription ID value>"
                    Name="Pay-As-You-Go"
                    ManagementCertificate="<Management certificate value>" />
            </PublishProfile>
        </PublishData>

Dopo avere creato il profilo di sottoscrizione, seguire questa procedura per configurare il plug-in slave di Azure:

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
   
    ![Manage Jenkins (Gestisci Jenkins)][jenkins-dashboard-manage]
2. Fare clic su **Configure System**.
   
    ![Configure System (Configura sistema)][jenkins-configure-system]
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud** .
4. Fare clic su **Add a new cloud** (Aggiungere un nuovo cloud) e selezionare **Microsoft Azure**.
   
    ![Add a new cloud (Aggiungere un nuovo cloud)][cloud-section]
   
    Verranno visualizzati i campi in cui è necessario immettere i dettagli della sottoscrizione.
   
    ![Configurazione della sottoscrizione][subscription-configuration]
5. Copiare i valori dell'ID sottoscrizione e del certificato di gestione dal profilo di sottoscrizione e incollarli nei campi appropriati.
   
    Quando si copiano l'ID sottoscrizione e il certificato di gestione, non includere le virgolette che racchiudono i valori.
6. Fare clic su **Verify Configuration** (Verifica configurazione) per verificare che i parametri specificati siano validi, quindi fare clic su **Save** (Salva).

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plugin"></a>Configurare un modello di macchina virtuale per il plug-in slave di Azure
In questa sezione si potrà aggiungere un modello di macchina virtuale che definisce i parametri che verranno usati dal plug-in slave di Azure per creare un nodo slave in Azure. Nella procedura seguente verrà creato un modello per una macchina virtuale Ubuntu.

1. Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
   
    ![Manage Jenkins (Gestisci Jenkins)][jenkins-dashboard-manage]
2. Fare clic su **Configure System**.
   
    ![Configure System (Configura sistema)][jenkins-configure-system]
3. Scorrere verso il basso la pagina per trovare la sezione **Cloud** .
4. Nella sezione **Cloud** cercare **Add Azure Virtual Machine Template** (Aggiungere modello di macchina virtuale Azure) per il cloud aggiunto nella sezione precedente, quindi fare clic su **Add** (Aggiungi).
   
    ![Add Azure Virtual Machine Template (Aggiungere modello di macchina virtuale Azure)][add-vm-template]
   
    Jenkins visualizzerà un modulo contenente i campi in cui è possibile immettere i dettagli relativi al modello che si sta creando.
   
    ![Configurazione generale campo vuoto][blank-general-configuration]
5. Immettere le informazioni relative alla **configurazione generale** del modello:
   
   1. Nella casella **Name** (Nome) immettere un nome per il nuovo modello, che sarà solo per uso personale e non verrà usato per il provisioning di una macchina virtuale.
   2. Nella casella **Description** immettere il testo che descrive il modello che si sta creando. Queste informazioni sono esclusivamente a scopo di riferimento e non vengono usate nel provisioning di una macchina virtuale.
   3. La casella **Labels** consente di identificare il modello che si sta creando e viene successivamente usata per fare riferimento al modello durante la creazione di un processo Jenkins. Ai fini di questa esercitazione, immettere "**linux**" in questa casella.
   4. Nell'elenco **Region** (Area) selezionare l'area di Azure in cui verrà creata la macchina virtuale.
   5. Nell'elenco **Virtual Machine Size** (Dimensioni macchina virtuale) scegliere le dimensioni appropriate dal menu a discesa.
   6. Nella casella **Storage Account Name** specificare un account di archiviazione in cui verrà creata la macchina virtuale. Se si vuole che Jenkins crei un account di archiviazione usando il valore predefinito "jenkinsarmst", è possibile lasciare vuota la casella.
   7. Il campo **Retention Time** (Periodo di memorizzazione) specifica il numero di minuti prima dell'eliminazione di uno slave inattivo da parte di Jenkins. Lasciare il valore predefinito di 60.
      
      * È anche possibile arrestare lo slave invece di eliminarlo quando è inattivo. A tale scopo, selezionare la casella di controllo **Shutdown Only (Do Not Delete) After Retention Time** .
      * È anche possibile immettere 0 se non si vuole eliminare automaticamente gli slave inattivi.
   8. Nell'elenco **Usage** (Uso), è possibile scegliere tra le opzioni seguenti:
      
      * **Utilize this node as much as possible** (Usare questo nodo il più possibile): Jenkins può eseguire qualsiasi processo sullo slave fintanto che risulta disponibile.
      * **Leave this node for tied jobs only** (Lasciare questo nodo solo per i processi associati): Jenkins creerà un progetto (o un processo) su questo nodo solo quando tale progetto è stato specificamente associato a tale nodo, in modo da riservare uno slave a determinati tipi di processi.
      
      Per questa esercitazione, fare clic su **Utilize this node as much as possible** (Usare questo nodo il più possibile).
      
      A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:
      
      ![Configurazione modello generale][general-template-config]
6. Specificare la **configurazione dell'immagine** del modello:
   
   1. Per la famiglia di immagini sono disponibili due opzioni:
      
      * **Custom User Image** (Immagine utente personalizzata): per questa opzione è necessario indicare l'URL di un'immagine personalizzata che si trova nello stesso account di archiviazione in cui si intende creare i nodi slave.
      * **Image Reference** (Riferimento immagine): questa opzione richiede di specificare le informazioni *Publisher* (Editore), *Offer* (Offerta) e *Sku* relative all'immagine, reperibili nel [Marketplace delle macchine virtuali di Azure][azure-images].
        
        Per questa esercitazione, scegliere **Image Reference** (Riferimento immagine) e specificare i valori seguenti:
      * **Image Publisher** (Editore immagine): Canonical
      * **Image Offer** (Offerta immagine): UbuntuServer
      * **Image Sku** (Sku immagine): 14.04.4-LTS
   2. Per l'elenco **Launch Method** (Metodo di avvio) sono disponibili due opzioni: **SSH** o **JNLP**. Ai fini di questa esercitazione, scegliere **SSH**. Tuttavia, ci sono alcuni aspetti da considerare quando si sceglie il metodo di avvio:
      
      * Gli slave Linux possono essere avviati solo tramite SSH.
      * Gli slave Windows possono usare SSH o JNLP, ma per usare SSH l'immagine deve essere personalizzata con un server SSH.
        
        Quando si usa JNLP come metodo di avvio, è necessario assicurarsi di aver configurato gli elementi seguenti:
      * Lo slave di Azure deve essere in grado di raggiungere l'URL di Jenkins, pertanto è necessario configurare tutti i firewall di conseguenza. È possibile trovare l'URL di Jenkins facendo clic su **Manage Jenkins** (Gestisci Jenkin) e su **Configure System** (Configura sistema), per poi cercare la sezione **Jenkins Location** (Posizione Jenkins).
   3. Lo slave di Azure che viene avviato con JNLP deve essere in grado di raggiungere la porta TCP di Jenkins; a tal fine, è consigliabile usare una porta TCP predefinita così da poter configurare tutti i firewall di conseguenza. È possibile specificare la porta TCP di Jenkins facendo clic su **Manage Jenkins** (Gestisci Jenkins) e su **Configure Global Security** (Configura protezione globale), selezionare la casella di opzione **Enable security** (Abilita protezione) e usare una porta **Fixed** (Fissa), quindi specificare la porta da usare.
   4. Per il campo **Init Script** (Script di inizializzazione), è necessario fornire uno script di inizializzazione che verrà eseguito dopo la creazione della macchina virtuale, tenendo presente quanto segue:
      
      * Come minimo, lo script deve installare Java.
      * Se si usa JNLP, lo script di inizializzazione deve essere scritto in PowerShell.
      * Se si prevede un lungo tempo di esecuzione dello script di inizializzazione, ad esempio per installare un'ampia serie di pacchetti, è consigliabile preparare un'immagine personalizzata con tutti i software necessari preinstallati anziché effettuarne l'installazione attraverso lo script di inizializzazione.
        
        Per questa esercitazione, copiare lo script sotto e incollarlo nella casella **Init Script** (Script di inizializzazione):
        
              # Install Java
              sudo apt-get -y update
              sudo apt-get install -y openjdk-7-jdk
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y openjdk-7-jdk
              # Install git
              sudo apt-get install -y git
              # Install ant
              sudo apt-get install -y ant
              sudo apt-get -y update --fix-missing
              sudo apt-get install -y ant
        
        Nell'esempio sopra lo script di inizializzazione installa *Java*, *Git* e *ant*.
   5. Nelle caselle **Username** (Nome utente) e **Password** immettere i valori preferiti per l'account amministratore che verrà creato nella macchina virtuale.
      
      A questo punto, il modulo dovrebbe avere un aspetto simile al seguente:
      
      ![Configurazione dell'immagine Jenkins][jenkins-image-configuration]
7. Fare clic su **Verify Template** (Verifica modello) per verificare che i parametri specificati siano validi, quindi fare clic su **Save** (Salva).
   
    ![Salvataggio di un modello in Jenkins][jenkins-save-template]

<a name="create-jenkins-project"></a>

## <a name="create-a-jenkins-job-which-runs-on-a-slave-node-on-azure"></a>Creare un processo Jenkins che viene eseguito su un nodo slave in Azure
In questa sezione si creerà un'attività di Jenkins che verrà eseguita in un nodo slave in Azure. Per completare questi passaggi, sarà necessario disporre di un progetto in GitHub.

1. Nel dashboard di Jenkins fare clic su **New Item**.
   
    ![Nuovo elemento nel dashboard di Jenkins][jenkins-dashboard-new-item]
2. Immettere un nome per l'attività che si sta creando, fare clic su **Freestyle project** (Progetto Freestyle) per il tipo di progetto, quindi selezionare **OK**.
   
    ![Creazione di un nuovo elemento in Jenkins][jenkins-create-new-item]
3. Nella pagina di configurazione dell'attività, selezionare **Restrict where this project can be run** (Limitare l'area di esecuzione del progetto), quindi immettere "**linux**" nella casella **Label Expression** (Espressione etichetta); questo valore corrisponde all'etichetta del modello slave creato nella sezione precedente.
   
    ![Limitazione del nuovo elemento in Jenkins][jenkins-new-item-restrict]
4. Nella sezione **Build** (Compilazione) fare clic su **Add build step** (Aggiungi passaggio di compilazione) e selezionare **Execute shell** (Esegui shell).
   
    ![Compilazione del nuovo elemento in Jenkins][jenkins-new-item-build]
5. Modificare lo script seguente sostituendo **(your GitHub account name)** (nome account GitHub), **(your project name)** (nome progetto) e **(your project directory)** (directory progetto) con i valori appropriati e incollare lo script modificato nell'area di testo visualizzata.
   
            # Clone from git repo
            currentDir="$PWD"
            if [ -e (your project directory) ]; then
                cd (your project directory)
                git pull origin master
            else
                git clone https://github.com/(your GitHub account name)/(your project name).git
            fi
            # change directory to project
            cd $currentDir/(your project directory)
            #Execute build task
            ant
   
    Verrà visualizzato un modulo simile al seguente:
   
    ![Script del nuovo elemento in Jenkins][jenkins-new-item-script]
6. Dopo avere completato tutti i passaggi di configurazione, fare clic su **Save** (Salva).
   
    ![Salvataggio del nuovo elemento in Jenkins][jenkins-new-item-save]
7. Nel dashboard di Jenkins fare clic sulla freccia del menu a discesa accanto al progetto appena creato, quindi selezionare **Build now** (Compila ora).
   
    ![Compilazione immediata in Jenkins][jenkins-build-now]

Jenkins creerà un nodo slave usando il modello creato nella sezione precedente ed eseguirà lo script specificato nel passaggio di compilazione di questa attività.

<a name="image-template-considerations"></a>

## <a name="considerations-when-working-with-image-templates"></a>Considerazioni relative all'uso dei modelli di immagine
Le sezioni seguenti offrono informazioni utili per la configurazione di vari modelli.

### <a name="when-you-are-using-ubuntu-image-templates"></a>Quando si usano i modelli di immagine Ubuntu
* Se si prevede un lungo tempo di esecuzione dello script di inizializzazione, ad esempio per installare un'ampia serie di pacchetti, è consigliabile preparare un'immagine Ubuntu personalizzata con tutti i software necessari preinstallati anziché effettuarne l'installazione attraverso lo script di inizializzazione.
* Come minimo, lo script deve installare Java. Come illustrato negli esempi precedenti, è possibile usare lo script seguente per installare Java, Git e ant.
  
        # Install Java
        sudo apt-get -y update
        sudo apt-get install -y openjdk-7-jdk
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y openjdk-7-jdk
        # Install git
        sudo apt-get install -y git
        # Install ant
        sudo apt-get install -y ant
        sudo apt-get -y update --fix-missing
        sudo apt-get install -y ant

### <a name="when-you-are-using-windows-image-templates-and-jnlp-launch-method"></a>Quando si usano i modelli di immagine di Windows, ricorrendo al metodo di avvio di JNLP
* Se nell'unità master Jenkins non è stata configurata la sicurezza:
  
  * Lasciare vuoto il campo **Init Script** (Script di inizializzazione) per lo script predefinito da eseguire sullo slave.
* Se nell'unità master Jenkins è stata configurata la sicurezza: 
  
  * Copiare lo script da [Windows Slaves Setup][windows-slaves-setup] e modificarlo con le credenziali di Jenkins.
  * Come minimo, lo script deve essere modificato con un token API e un ID utente di Jenkins. Per recuperare il token API per un utente, seguire la procedura descritta qui sotto:
    
    1. Nel dashboard di Jenkins fare clic su **People** (Persone).
    2. Fare clic sull'account utente appropriato.
    3. Fare clic su **Configure** (Configura) nella barra dei menu a sinistra.
    4. Fare clic su **Mostra Token API**.

<a name="see-also"></a>

## <a name="see-also"></a>Vedere anche
Per altre informazioni su come usare Azure con Java, vedere il [Centro per sviluppatori Java di Azure].

Per altre informazioni sul plug-in slave di Azure per Jenkins, vedere il progetto [Azure Slave Plugin] (Plug-in slave di Azure) in GitHub.

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: https://azure.microsoft.com/develop/java/
[Azure Slave Plugin]: https://github.com/jenkinsci/azure-slave-plugin/
[profilo di sottoscrizione]: http://go.microsoft.com/fwlink/?LinkID=396395
[azure-images]: https://azure.microsoft.com/marketplace/virtual-machines/all/
[integrate-apps-with-AAD]: http://msdn.microsoft.com/library/azure/dn132599.aspx
[register-client-app]: http://msdn.microsoft.com/dn877542.aspx
[windows-slaves-setup]: https://gist.github.com/snallami/5aa9ea2c57836a3b3635

<!-- IMG List -->

[jenkins-dashboard-manage]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-manage.png
[jenkins-manage-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-manage-plugins.png
[jenkins-configure-system]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-configure-system.png
[jenkins-dashboard-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-dashboard-new-item.png
[search-plugins]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/search-for-azure-plugin.png
[install-plugin]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/install-plugin.png
[jenkins-create-new-item]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-create-new-item.png
[cloud-section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add-vm-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank-general-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[general-template-config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[jenkins-new-item-restrict]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-restrict.png
[jenkins-new-item-build]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-build.png
[jenkins-new-item-script]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-script.png
[jenkins-new-item-save]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-new-item-save.png
[jenkins-build-now]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-build-now.png
[jenkins-image-configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-image-configuration.png
[jenkins-save-template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-save-template.png
[windows-image-capture]: ./virtual-machines-windows-classic-capture-image.md
[linux-image-capture]: ./virtual-machines-linux-capture-image.md



<!--HONumber=Nov16_HO3-->


