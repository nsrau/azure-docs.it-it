---
title: Esercitazione - Creare un modello Terraform di base in Azure con Yeoman
description: Informazioni su come creare un modello Terraform di base in Azure con Yeoman.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 2f8cbc495a4b46255e7eb31bc1ff8b04fffcad15
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969260"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Esercitazione: Creare un modello Terraform di base in Azure con Yeoman

In questa esercitazione si apprenderà come usare la combinazione di [Terraform](/azure/terraform/) e [Yeoman](https://yeoman.io/). Terraform è uno strumento per la creazione di un'infrastruttura in Azure. Yeoman semplifica la creazione di moduli Terraform.

In questo articolo si apprenderà come eseguire le attività seguenti:
> [!div class="checklist"]
> * Creare un modello Terraform di base usando il generatore di moduli Yeoman.
> * Testare il modello Terraform procedendo in due modi diversi.
> * Eseguire il modulo Terraform con un file Docker.
> * Eseguire il modulo Terraform in modalità nativa in Azure Cloud Shell.

## <a name="prerequisites"></a>Prerequisiti

- **Sottoscrizione di Azure**: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- **Visual Studio Code**: [scaricare Visual Studio Code](https://code.visualstudio.com/download) per la piattaforma in uso.
- **Terraform**: [installare Terraform](/azure/virtual-machines/linux/terraform-install-configure ) per eseguire il modulo creato da Yeoman.
- **Docker**: [installare Docker](https://www.docker.com/get-started) per eseguire il modulo creato dal generatore Yeoman.
- **Linguaggio di programmazione Go**: [installare Go](https://golang.org/) in quanto i test case generati da Yeoman sono codice in cui viene usato il linguaggio Go.

>[!NOTE]
>La maggior parte delle procedure riportate in questa esercitazione prevede l'uso dell'interfaccia della riga di comando. I passaggi descritti si applicano a tutti i sistemi operativi e gli strumenti da riga di comando. Per gli esempi, sono stati scelti PowerShell per l'ambiente locale e Git Bash per l'ambiente Cloud Shell.

## <a name="prepare-your-environment"></a>Preparare l'ambiente

### <a name="install-nodejs"></a>Installare Node.js

Per usare Terraform in Cloud Shell, è necessario [installare Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Per verificare che Node.js sia installato, aprire una finestra del terminale e immettere `node --version`.

### <a name="install-yeoman"></a>Installare Yeoman

Eseguire il comando seguente:

```bash
npm install -g yo
```

![Installare Yeoman](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Installare il modello Yeoman per il modulo Terraform

Eseguire il comando seguente:

```bash
npm install -g generator-az-terra-module
```

![Installare generator-az-terra-module](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

Per verificare che Yeoman sia installato, eseguire il comando seguente:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Creare una directory per il modulo generato da Yeoman

Il modello Yeoman genera i file nella directory corrente. Per questo motivo è necessario creare una directory.

Questa directory vuota deve essere inserita in $GOPATH/src. Per altre informazioni su questo percorso, vedere l'articolo [Impostazione di GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

1. Passare alla directory padre da cui creare una nuova directory.

1. Eseguire il comando seguente sostituendo il segnaposto. Per questo esempio, viene usato il nome di directory `GeneratorDocSample`.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Passare alla nuova directory:

    ```bash
    cd <new-directory-name>
    ```

    ![Passare alla nuova directory](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Creare un modello di modulo di base

1. Eseguire il comando seguente:

    ```bash
    yo az-terra-module
    ```

1. Seguire le istruzioni indicate sullo schermo per fornire le informazioni seguenti:

    - **Terraform module project Name**: per l'esempio viene usato il valore `doc-sample-module`.

        ![Project name (Nome progetto)](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Si desidera includere il file di immagine Docker?** : immettere `y`. Se si seleziona `n`, il codice del modulo generato supporterà solo l'esecuzione in modalità nativa.

        ![Includere il file di immagine Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. Elencare il contenuto della directory per visualizzare i file risultanti che vengono creati:

    ```bash
    ls
    ```

    ![Elencare i file creati](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Esaminare il codice di modulo generato

1. Avviare Visual Studio Code

1. Nella barra dei menu selezionare **File > Apri cartella** e selezionare la cartella creata.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

I file seguenti sono stati creati dal generatore di moduli Yeoman. Per altre informazioni su questi file e sul relativo utilizzo, vedere [Terratest nei moduli Terraform](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`: definisce un modulo denominato `random-shuffle`. L'input è un oggetto `string_list`. L'output è il numero di permutazioni.
- `variables.tf`: definisce le variabili di input e output usate dal modulo.
- `outputs.tf`: definisce l'output del modulo. In questo caso è il valore restituito da `random_shuffle`, ovvero un modulo Terraform predefinito.
- `Rakefile`: definisce i passaggi di compilazione. La procedura include i passaggi seguenti:
    - `build`: convalida la formattazione del file main.tf.
    - `unit`: lo scheletro del modulo generato non include codice per uno unit test. Se si desidera specificare uno scenario di unit test, si deve aggiungere codice qui.
    - `e2e`: esegue un test end-to-end del modulo.
- `test`
    - I test case sono scritti in Go.
    - Tutti i codici dei test sono test end-to-end.
    - I test end-to-end tentano di effettuare il provisioning di tutti gli elementi definiti in `fixture`. I risultati nel file `template_output.go` vengono confrontati con i valori previsti predefiniti.
    - `Gopkg.lock` e `Gopkg.toml`: definiscono le dipendenze. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testare il nuovo modulo Terraform con un file Docker

In questa sezione viene illustrato come testare un modulo Terraform con un file Docker.

>[!NOTE]
>Questo esempio esegue il modulo in locale, non in Azure.

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

    ![Messaggio che indica una compilazione con esito positivo](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. Al prompt dei comandi immettere `docker image ls` per vedere elencato il modulo creato `terra-mod-example`.

    ![Elenco contenente il nuovo modulo](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Immettere `docker run -it terra-mod-example /bin/sh`. Dopo aver eseguito il comando `docker run`, ci si trova nell'ambiente Docker. A questo punto è possibile individuare il file usando il comando `ls`.

    ![Elenco file in Docker](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Compilare il modulo

1. Eseguire il comando seguente:

    ```bash
    bundle install
    ```

1. Eseguire il comando seguente:

    ```bash
    rake build
    ```

    ![Compilazione di Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Eseguire il test end-to-end

1. Eseguire il comando seguente:

    ```bash
    rake e2e
    ```

1. Dopo qualche istante sarà visualizzato il messaggio **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Immettere `exit` per completare il test e uscire dall'ambiente Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Usare il generatore Yeoman per creare e testare un modulo in Cloud Shell

In questa sezione viene usato il generatore Yeoman per creare e testare un modulo in Cloud Shell. L'uso di Cloud Shell al posto di un file Docker semplifica notevolmente il processo. Se si usa Cloud Shell, i prodotti seguenti sono tutti preinstallati:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Avviare una sessione di Cloud Shell

1. Avviare una sessione di Azure Cloud Shell tramite il [portale di Azure](https://portal.azure.com/), [shell.azure.com](https://shell.azure.com) o l'[app per dispositivi mobili di Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. Verrà visualizzata la pagina **Benvenuto in Azure Cloud Shell**. Selezionare **Bash (Linux)** .

    ![Benvenuto in Azure Cloud Shell](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Se non si è già configurato un account di archiviazione di Azure, viene visualizzata la schermata seguente. Selezionare **Create storage** (Crea risorsa di archiviazione).

    ![Non sono state montate risorse di archiviazione](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Azure Cloud Shell viene avviato nella shell selezionata in precedenza e visualizza le informazioni per l'unità cloud appena creata per l'utente.

    ![L'unità cloud è stata creata](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Preparare una directory in cui inserire il modulo Terraform

1. A questo punto, Cloud Shell avrà già configurato automaticamente GOPATH nelle variabili di ambiente. Per visualizzare il percorso, immettere `go env`.

1. Creare la directory $GOPATH, se non ne esiste già una: Immettere `mkdir ~/go`.

1. Creare una directory all'interno della directory $GOPATH. Tale directory viene usata per contenere le diverse directory di progetto create in questo esempio. 

    ```bash
    mkdir ~/go/src
    ```

1. Creare una directory in cui inserire il modulo Terraform sostituendo il segnaposto. Per questo esempio, viene usato il nome di directory `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Passare alla directory del modulo: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Creare e testare il modulo Terraform

1. Eseguire questo comando e seguire le istruzioni. Quando viene chiesto se si vogliono creare i file Docker, immettere `N`.

    ```bash
    yo az-terra-module
    ```

1. Eseguire il comando seguente per installare le dipendenze:

    ```bash
    bundle install
    ```

1. Eseguire il comando seguente per compilare il modulo:

    ```bash
    rake build
    ```

    ![Compilazione di Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Eseguire il comando seguente per eseguire il test:

    ```bash
    rake e2e
    ```

    ![Risultati che indicano il superamento del test](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare e usare l'estensione Azure Terraform di Visual Studio Code](/azure/terraform/terraform-vscode-extension).