<properties linkid="develop-java-tutorials-jenkins-continuous-integration" urlDisplayName="Jenkins Continuous Integration" pageTitle="Using Azure Storage with a Jenkins Continuous Integration Solution | Microsoft Azure" metaKeywords="" description="This tutorial show how to use the Azure blob service as a repository for build artifacts created by a Jenkins continuous integration solution." metaCanonical="" services="storage" documentationCenter="Java" title="Using Azure Storage with a Jenkins Continuous Integration solution" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Utilizzo di Archiviazione di Azure con una soluzione di Integrazione continuata Jenkins
=======================================================================================

*Autore: [Microsoft Open Technologies Inc.](http://msopentech.com)*

Nell'articolo riportato di seguito viene descritto come utilizzare il servizio BLOB di Azure come archivio di elementi di compilazione creati dalla soluzione di Integrazione continuata Jenkins (CI). Queste informazioni possono rivelarsi utili nel caso in cui si codifichi in un ambiente di sviluppo Agile (utilizzando Java o altri linguaggi), le compilazioni vengano eseguite in base all'integrazione continuata e sia necessario un archivio per gli elementi di compilazione, ad esempio per poterli condividere con altri membri dell'organizzazione o clienti oppure per gestire un archivio.

In questa esercitazione si utilizzerà il plug-in di Archiviazione di Azure per l'Integrazione continuata Jenkins reso disponibile da Microsoft Open Technologies, Inc.

Sommario
--------

-   [Informazioni generali su Jenkins](#overview)
-   [Vantaggi dell'utilizzo del servizio BLOB](#benefits)
-   [Prerequisiti](#prerequisites)
-   [Utilizzo del servizio BLOB con l'Integrazione continuata Jenkins](#howtouse)
-   [Installazione del plug-in di Archiviazione di Azure](#howtoinstall)
-   [Configurazione del plug-in di Archiviazione di Azure per utilizzare l'account di archiviazione](#howtoconfigure)
-   [Creazione di un'operazione post-compilazione per il caricamento degli elementi di compilazione nell'account di archiviazione](#howtocreatepostbuild)
-   [Componenti utilizzati dal servizio BLOB](#components)

Informazioni generaliInformazioni generali su Jenkins
-----------------------------------------------------

Jenkins abilita l'integrazione continuata di un progetto software consentendo agli sviluppatori di integrare facilmente le modifiche apportate al codice e produrre compilazioni automaticamente e di frequente, aumentando così la produttività degli sviluppatori. Alle compilazioni è applicato il controllo delle versioni ed è possibile caricare gli elementi di compilazione in diversi archivi. In questo argomento viene illustrato come utilizzare l'archivio BLOB di Azure come archivio per gli elementi di compilazione.

Per ulteriori informazioni su Jenkins, vedere [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

VantaggiVantaggi dell'utilizzo del servizio BLOB
------------------------------------------------

Di seguito sono indicati i vantaggi dell'utilizzo del servizio BLOB per ospitare gli elementi di compilazione prodotti dallo sviluppo Agile:

-   Disponibilità elevata degli elementi di compilazione.
-   Migliori prestazioni nel caricamento degli elementi di compilazione da parte della soluzione di Integrazione continuata Jenkins.
-   Migliori prestazioni durante il download degli elementi di compilazione da parte di clienti e partner.
-   Controllo dei criteri di accesso da parte dell'utente, che prevede la possibilità di scelta tra accesso anonimo, accesso condiviso con scadenza, accesso con firma, accesso privato e così via.

PrerequisitiPrerequisiti
------------------------

Per utilizzare il servizio BLOB con la soluzione di Integrazione continuata Jenkins è necessario quanto segue:

-   Una soluzione di Integrazione continuata Jenkins.

    Se si è sprovvisti di una soluzione di Integrazione continuata Jenkins è possibile eseguire una soluzione di Integrazione continuata Jenkins applicando la tecnica seguente:

    1.  In un computer in cui è abilitato Java, scaricare il file jenkins.war dall'indirizzo http://jenkins-ci.org.
    2.  Al prompt dei comandi aperto nella cartella che contiene jenkins.war eseguire:

        `java -jar jenkins.war`

    3.  Nel browser aprire `http://localhost:8080/`. Verrà visualizzato il dashboard di Jenkins, che verrà utilizzato per installare e configurare il plug-in di Archiviazione di Azure.

        Sebbene sia necessario configurare una tipica soluzione di Integrazione continuata Jenkins per eseguirla come servizio, ai fini di questa esercitazione sarà sufficiente eseguire il file WAR di Jenkins nella riga di comando.

-   Un account Azure. È possibile effettuare l'iscrizione a un account Azure all'indirizzo http://www.windowsazure.com.

-   Un account di Archiviazione di Azure. Se non si dispone di un account di archiviazione, è possibile crearne uno attenendosi ai passaggi illustrati in [Come creare un account di archiviazione](http://go.microsoft.com/fwlink/?LinkId=279823).

-   La conoscenza della soluzione di Integrazione continuata Jenkins è consigliata ma non richiesta, poiché nel contenuto seguente verrà utilizzato un esempio semplice per illustrare i passaggi da seguire nell'utilizzo del servizio BLOB come archivio per elementi di compilazione dell'Integrazione continuata Jenkins.

Utilizzo di un servizio BLOBUtilizzo del servizio BLOB con l'Integrazione continuata Jenkins
--------------------------------------------------------------------------------------------

Per utilizzare il servizio BLOB con Jenkins, è necessario installare il plug-in di Archiviazione di Azure, configurare il plug-in per utilizzare l'account di archiviazione e creare un'operazione post-compilazione per il caricamento degli elementi di compilazione nell'account di archiviazione. Questi passaggi vengono descritti nelle sezioni seguenti.

InstallazioneInstallazione del plug-in di Archiviazione di Azure
----------------------------------------------------------------

1.  Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
2.  Nella pagina **Manage Jenkins** fare clic su **Manage Plugins**.
3.  Fare clic sulla scheda **Available**.
4.  Nella sezione **Artifact Uploaders** selezionare **Azure Storage plugin**.
5.  Fare clic su **Install without restart** oppure su **Download now and install after restart**.
6.  Riavviare Jenkins.

ConfigurazioneConfigurazione del plug-in di Archiviazione di Azure per l'utilizzo dell'account di archiviazione
---------------------------------------------------------------------------------------------------------------

1.  Nel dashboard di Jenkins fare clic su **Manage Jenkins**.
2.  Nella pagina **Manage Jenkins** fare clic su **Configure System**.
3.  Nella sezione **Azure Storage Account Configuration**:
    1.  Immettere il nome dell'account di archiviazione che è possibile ottenere dal portale di Azure, all'indirizzo https://manage.windowsazure.com.
    2.  Immettere la chiave dell'account di archiviazione che è possibile ottenere dal portale di Azure.
    3.  Se si utilizza il servizio Cloud di Azure pubblico, immettere il valore predefinito in **Blob Service Endpoint URL**. Se si utilizza un servizio Cloud di Azure diverso, utilizzare l'endpoint specificato nel portale di gestione di Azure per l'account di archiviazione.
    4.  Fare clic su **Validate Storage Credentials** per convalidare l'account di archiviazione.
    5.  [Facoltativo] Se si dispone di account di archiviazione aggiuntivi che si desidera rendere disponibili all'Integrazione continuata Jenkins, fare clic su **Add more Storage Accounts**.
    6.  Per salvare le impostazioni, fare clic su **Save**.

Creazione di un'operazione post-compilazioneCreazione di un'operazione post-compilazione per il caricamento degli elementi di compilazione nell'account di archiviazione
------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Ai fini di questa esercitazione, è necessario innanzitutto creare un processo che crei più file e quindi aggiungere l'operazione post-compilazione per caricare i file nell'account di archiviazione.

1.  Nel dashboard di Jenkins fare clic su **New Job**.
2.  Denominare il processo **MyJob**, fare clic su **Build a free-style software project** e quindi fare clic su **OK**.
3.  Nella sezione **Build** della configurazione del processo fare clic su **Add build step** e scegliere **Execute Windows batch command**.
4.  Nella sezione **Command** utilizzare i comandi seguenti:

         md text
         cd text
         echo Hello Azure Storage from Jenkins > hello.txt
         date /t > date.txt
         time /t >> date.txt

5.  Nella sezione **Post-build Actions** della configurazione del processo fare clic su **Add post-build action** e scegliere **Upload artifacts to Azure Blob storage**.
6.  In **Storage Account Name** scegliere l'account di archiviazione da utilizzare.
7.  In **Container Name** specificare il nome del contenitore. Il contenitore verrà creato se non esiste già al momento del caricamento degli elementi di compilazione. È possibile utilizzare variabili di ambiente, pertanto in questo esempio immettere **${JOB\_NAME}** come nome del contenitore.

    **Suggerimento**

    Sotto la sezione **Command** in cui è stato immesso uno script per **Execute Windows batch command** è presente un collegamento alle variabili di ambiente riconosciute da Jenkins. Fare clic sul collegamento per ottenere dettagli sui nomi e le descrizioni delle variabili di ambiente. Si noti che le variabili di ambiente che contengono caratteri speciali come la variabile di ambiente **BUILD\_URL** non sono ammesse come nome di contenitore o come percorso virtuale comune.

8.  Ai fini di questo esempio, fare clic su **Make container public**. Se si intende utilizzare un contenitore privato, è necessario creare una firma di accesso condiviso per consentire l'accesso. Questa operazione non rientra nell'ambito di questo argomento. Per ulteriori informazioni sulle firme di accesso condiviso, vedere [Creare e utilizzare una firma di accesso condiviso](http://go.microsoft.com/fwlink/?LinkId=279889).
9.  In **List of Artifacts to upload** immettere **text/\*.txt**.
10. In **Common virtual path for uploaded artifacts** immettere **${BUILD\_ID}/${BUILD\_NUMBER}**.
11. Per salvare le impostazioni, fare clic su **Save**.
12. Nel dashboard di Jenkins fare clic su **Build Now** per eseguire **MyJob**. Esaminare l'output di console per ottenere informazioni sullo stato. I messaggi di stato per il servizio di archiviazione di Azure verranno inclusi nell'output della console quando l'operazione post-compilazione avvierà il caricamento degli elementi di compilazione.
13. Dopo avere completato il processo, è possibile esaminare gli elementi di compilazione aprendo il BLOB pubblico.
    1.  Accedere al portale di gestione di Azure all'indirizzo https://manage.windowsazure.com.
    2.  Fare clic su **Storage**.
    3.  Fare clic sul nome dell'account di archiviazione utilizzato per Jenkins.
    4.  Fare clic su **Containers**.
    5.  Fare clic sul contenitore denominato **myjob**, ossia la versione in lettere minuscole del nome processo assegnato al momento della creazione del processo Jenkins. In Archiviazione di Azure i nomi di contenitori e i nomi di BLOB sono riportati in lettere minuscole (e si applica la distinzione maiuscole/minuscole). Nell'elenco di BLOB per il contenitore denominato **myjob** dovrebbero essere visualizzati **hello.txt** e **date.txt**. Copiare l'URL di uno di questi elementi e aprirlo nel browser. Verrà visualizzato il file di testo caricato come elemento di compilazione.

Componenti del servizio BLOBComponenti utilizzati dal servizio BLOB
-------------------------------------------------------------------

Di seguito è riportata una panoramica delle componenti del servizio BLOB.

-   **Account di archiviazione**: l'accesso ad Archiviazione di Azure viene eseguito esclusivamente tramite un account di archiviazione. Questo è il livello più alto dello spazio dei nomi per accedere ai BLOB. Un account può contenere un numero illimitato di contenitori, purché la dimensione totale di questi sia inferiore a 100 TB.
-   **Contenitore**: un contenitore è il raggruppamento di un set di BLOB. Tutti i BLOB devono trovarsi in un contenitore. In un account può esistere un numero illimitato di contenitori. In un contenitore può essere archiviato un numero illimitato di BLOB.
-   **BLOB**: file di qualsiasi tipo o dimensione. Vi sono due tipi di BLOB che possono essere archiviati in Archiviazione di Azure: BLOB di pagine e BLOB in blocchi. La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di dimensione. In questa esercitazione vengono utilizzati BLOB in blocchi. I BLOB dell'altro tipo, di pagine, possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando vi sono intervalli di byte all'interno del file soggetti a modifiche frequenti. Per ulteriori informazioni sui BLOB, vedere [Informazioni su BLOB in blocchi e BLOB di pagine](http://msdn.microsoft.com/en-us/library/windowsazure/ee691964.aspx).
-   **Formato dell'URL**: I BLOB sono indirizzabili utilizzando il formato di URL seguente:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    Il formato riportato sopra si riferisce al servizio Cloud Azure pubblico. Se si utilizza un servizio Cloud Azure diverso, utilizzare l'endpoint specificato nel portale di gestione di Azure per determinare l'endpoint dell'URL.

    Nel formato riportato sopra `storageaccount` rappresenta il nome dell'account di archiviazione, `container_name` rappresenta il nome contenitore e `blob_name` rappresenta il nome del BLOB. Nel nome contenitore è possibile avere percorsi multipli, separati da una barra **/**. Il nome di contenitore utilizzato come esempio in questa esercitazione è **MyJob**, mentre **${BUILD\_ID}/${BUILD\_NUMBER}** è stato utilizzato per il percorso virtuale comune, di conseguenza l'URL del BLOB ha il formato seguente:

    `http://example.blob.core.windows.net/myjob/2013-01-28_15-00-35/2/hello.txt`


