---
title: Creare un modello Terraform di base in Azure con Yeoman
description: Informazioni su come creare un modello Terraform di base in Azure con Yeoman.
services: terraform
ms.service: azure
keywords: terraform, devops, macchina virtuale, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/08/2018
ms.openlocfilehash: 7e66f374a1f5f4fb050f366fdad0e787292101f8
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526742"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Creare un modello Terraform di base in Azure con Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) fornisce un modo per creare con facilità l'infrastruttura in Azure. [Yeoman](https://yeoman.io/) semplifica notevolmente il lavoro dello sviluppatore di moduli Terraform fornendo al contempo un eccellente framework di *procedure consigliate*.

Questo articolo descrive come usare il generatore di moduli Yeoman per creare un modello Terraform di base. Verrà quindi illustrato come testare il nuovo modello Terraform con due diversi metodi:

- Esecuzione del modulo Terraform con un file Docker creato in questo articolo
- Esecuzione del modulo Terraform in modalità nativa in Azure Cloud Shell

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- **Visual Studio Code**: si userà [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) per esaminare i file creati dal generatore Yeoman. In alternativa è possibile usare qualsiasi editor di codice di propria scelta.
- **Terraform**: è necessaria un'installazione di [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) per l'esecuzione del modulo creato da Yeoman.
- **Docker**: si userà [Docker](https://www.docker.com/get-started) per eseguire il modulo creato dal generatore Yeoman. Se si preferisce, è possibile usare Ruby al posto di Docker per eseguire il modulo di esempio.
- **Linguaggio di programmazione Go**: è necessaria un'installazione di [Go](https://golang.org/) poiché i test case generati da Yeoman sono scritti in Go.

>[!NOTE]
>La maggior parte delle procedure riportate in questa esercitazione prevede l'uso della riga di comando. I passaggi descritti di seguito si applicano a tutti i sistemi operativi e gli strumenti da riga di comando. Negli esempi si è scelto di usare PowerShell per l'ambiente locale e Git Bash per l'ambiente Cloud Shell.

## <a name="prepare-your-environment"></a>Preparare l'ambiente

### <a name="install-nodejs"></a>Installare Node.js

Per usare Terraform in Cloud Shell, è necessario [installare Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Per verificare che Node.js sia installato, aprire una finestra del terminale e immettere `node --version`.

### <a name="install-yeoman"></a>Installare Yeoman

Da un prompt dei comandi immettere `npm install -g yo`

![Installare Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installare il modello Yeoman per il modulo Terraform

Da un prompt dei comandi immettere `npm install -g generator-az-terra-module`.

![Installare generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>Per verificare che Yeoman sia installato, da una finestra del terminale immettere `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Creare una cartella vuota per contenere il modulo generato da Yeoman

Il modello Yeoman genera i file nella **directory corrente**. Per questo motivo è necessario creare una directory.

>[!Note]
>Questa directory vuota deve essere inserita in $GOPATH/src. Le istruzioni necessarie sono disponibili [qui](https://github.com/golang/go/wiki/SettingGOPATH).

Da un prompt dei comandi:

1. Passare alla directory padre in cui includere la nuova directory vuota che sarà creata.
1. Immettere `mkdir <new-directory-name>`.

    > [!NOTE]
    > Sostituire `<new-directory-name>` con il nome della nuova directory. In questo esempio la nuova directory viene denominata `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Passare alla nuova directory digitando `cd <new directory's name>` e premendo **INVIO**.

    ![Passare alla nuova directory](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Per assicurarsi che la directory sia vuota, immettere `ls`. L'output del comando non deve mostrare file.

## <a name="create-a-base-module-template"></a>Creare un modello di modulo di base

Da un prompt dei comandi:

1. Immettere `yo az-terra-module`.

1. Seguire le istruzioni indicate sullo schermo per fornire le informazioni seguenti:

    - *Nome di progetto del modulo Terraform*

        ![Project name (Nome progetto)](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >In questo esempio è stato immesso `doc-sample-module`.

    - *Si desidera includere il file di immagine Docker?*

        ![Includere il file di immagine Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Immettere `y`. Se si seleziona **n**, il codice del modulo generato supporterà solo l'esecuzione in modalità nativa.

3. Immettere `ls` per visualizzare i file risultanti che vengono creati.

    ![Elencare i file creati](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Esaminare il codice di modulo generato

1. Avviare Visual Studio Code

1. Nella barra dei menu selezionare **File > Apri cartella** e selezionare la cartella creata.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Si esamineranno ora alcuni dei file creati dal generatore di moduli Yeoman.

>[!Note]
>In questo articolo si useranno i file main.tf, variables.tf e outputs.tf creati dal generatore di moduli Yeoman. Tuttavia, durante la creazione di moduli personalizzati, si modificheranno questi file per supportare la funzionalità del modulo Terraform. Per una discussione più dettagliata di questi file e il relativo utilizzo, vedere [Terratest in Terraform Modules](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) (Terratest nei moduli Terraform).

### <a name="maintf"></a>main.tf

Definisce un modulo denominato *random-shuffle*. L'input è una *string_list*. L'output è il numero di permutazioni.

### <a name="variablestf"></a>variables.tf

Definisce le variabili di input e output utilizzate dal modulo.

### <a name="outputstf"></a>outputs.tf

Definisce l'output del modulo. In questo caso è il valore restituito da **random_shuffle**, ovvero un modulo Terraform predefinito.

### <a name="rakefile"></a>Rakefile

Definisce i passaggi di compilazione. La procedura include i passaggi seguenti:

- **build**: convalida la formattazione del file main.tf.
- **unit**: lo scheletro del modulo generato non include codice per uno unit test. Se si desidera specificare uno scenario di unit test, si deve aggiungere codice qui.
- **e2e**: esegue un test end-to-end del modulo.

### <a name="test"></a>test

- I test case sono scritti in Go.
- Tutti i codici dei test sono test end-to-end.
- I test end-to-end provano a usare Terraform per effettuare il provisioning di tutti gli elementi definiti in **fixture** e quindi confrontano l'output nel codice **template_output.go** con i valori previsti predefiniti.
- **Gopkg.lock** e **gopkg**: definiscono le dipendenze. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testare il nuovo modulo Terraform con un file Docker

>[!NOTE]
>In questo esempio eseguiamo il modulo come modulo locale senza toccare Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Verificare che Docker sia installato e in esecuzione

Da un prompt dei comandi immettere `docker version`.

![Versione di Docker](media/terraform-vscode-module-generator/ymg-docker-version.png)

L'output risultante conferma che Docker è installato.

Per verificare che Docker sia in esecuzione, immettere `docker info`.

![Informazioni su Docker](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Configurare un contenitore Docker

1. Da un prompt dei comandi immettere

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Sarà visualizzato il messaggio **Successfully built** (Compilazione riuscita).

    ![Compilazione riuscita](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Al prompt dei comandi immettere `docker image ls`.

    Il modulo appena creato, *terra-mod-example*, comparirà nell'elenco.

    ![Risultati del repository](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >Il nome del modulo, *terra-mod-example*, è stato specificato nel comando immesso nel passaggio 1 in precedenza.

1. Immettere `docker run -it terra-mod-example /bin/sh`.

    Ora l'esecuzione avviene in Docker ed è possibile elencare il file immettendo `ls`.

    ![Elencare il file Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Compilare il modulo

1. Immettere `bundle install`.

    Attendere che sia visualizzato il messaggio **Bundle complete** (Aggregazione completa), quindi continuare con il passaggio successivo.

1. Immettere `rake build`.

    ![Compilazione di Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Eseguire il test end-to-end

1. Immettere `rake e2e`.

1. Dopo qualche istante sarà visualizzato il messaggio **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Immettere `exit` per completare il test end-to-end e uscire dall'ambiente Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Usare il generatore Yeoman per creare e testare un modulo in Cloud Shell

Nella sezione precedente è stato illustrato come testare un modulo Terraform con un file Docker. In questa sezione si userà il generatore Yeoman per creare e testare un modulo in Cloud Shell.

L'uso di Cloud Shell al posto di un file Docker semplifica notevolmente il processo. Con Cloud Shell:

- Non è necessario installare Node.js
- Non è necessario installare Yeoman
- Non è necessario installare Terraform

Tutti questi elementi sono preinstallati in Cloud Shell.

### <a name="start-a-cloud-shell-session"></a>Avviare una sessione di Cloud Shell

1. Avviare una sessione di Azure Cloud Shell tramite il [portale di Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) o l'[app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Verrà visualizzata la pagina **Benvenuto in Azure Cloud Shell**. Selezionare **Bash (Linux)**. PowerShell non è supportato.

    ![Benvenuto in Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >In questo esempio è stato selezionato Bash (Linux).

1. Se non si è già configurato un account di archiviazione di Azure, viene visualizzata la schermata seguente. Selezionare **Create storage** (Crea risorsa di archiviazione).

    ![Non sono state montate risorse di archiviazione](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell viene avviato nella shell selezionata in precedenza e visualizza le informazioni per l'unità cloud appena creata per l'utente.

    ![L'unità cloud è stata creata](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Preparare una cartella in cui inserire il modulo Terraform

1. A questo punto, Cloud Shell avrà già configurato automaticamente GOPATH nelle variabili di ambiente. Per visualizzare il percorso, immettere `go env`.

1. Creare la cartella $GOPATH, se non ne esiste già una: Immettere `mkdir ~/go`.

1. Creare una cartella all'interno della cartella $GOPATH: Immettere `mkdir ~/go/src`. In questa cartella verranno inserite e organizzate le diverse cartelle di progetto eventualmente create, come la cartella `<your-module-name>` che verrà creata nel passaggio successivo.

1. Creare una cartella in cui inserire il modulo Terraform: Immettere `mkdir ~/go/src/<your-module-name>`.

    >[!NOTE]
    >In questo esempio, come nome della cartella è stato scelto `my-module-name`.

1. Passare alla cartella del modulo: Immettere `cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Creare e testare il modulo Terraform

1. Immettere `yo az-terra-module` e seguire le istruzioni della procedura guidata.

    >[!NOTE]
    >Quando viene chiesto se si vogliono creare i file Docker, si può immettere `N`.

1. Immettere `bundle install` per installare le dipendenze.

    Attendere che sia visualizzato il messaggio **Bundle complete** (Aggregazione completa), quindi continuare con il passaggio successivo.

1. Immettere `rake build` per compilare il modulo.

    ![Compilazione di Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Immettere `rake e2e` per eseguire il test end-to-end.

1. Dopo qualche istante sarà visualizzato il messaggio **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare e usare l'estensione Azure Terraform di Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
