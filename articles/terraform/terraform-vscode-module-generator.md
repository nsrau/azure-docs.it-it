---
title: Azure Terraform contro il generatore di moduli di codice
description: Informazioni su come usare Yeoman per creare un modello di base di Terraform.
services: terraform
ms.service: terraform
keywords: terraform, devops, macchina virtuale, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396361"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Creare un modello Terraform di base con Yeoman

[Terraform](https://docs.microsoft.com/azure/terraform/
) fornisce un modo per creare con facilità l'infrastruttura in Azure. [Yeoman](http://yeoman.io/) semplifica notevolmente il lavoro dello sviluppatore di moduli Terraform fornendo al contempo un eccellente framework di *procedure consigliate*.

Questo articolo descrive come usare il generatore di moduli Yeoman per creare un modello Terraform di base.

## <a name="prerequisites"></a>Prerequisiti

- Un computer che esegue Windows 10, Linux o macOS 10.10+.
- **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
- **Visual Studio Code**: si userà [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) per esaminare i file creati dal generatore Yeoman. In alternativa è possibile usare qualsiasi editor di codice di propria scelta.
- **Terraform**: è necessaria un'installazione di [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) per l'esecuzione del modulo creato da Yeoman.
- **Docker**: si userà [Docker](https://www.docker.com/get-started) per eseguire il modulo creato dal generatore Yeoman. Se si preferisce, è possibile usare Ruby al posto di Docker per eseguire il modulo di esempio.
- **Linguaggio di programmazione Go**: è necessaria un'installazione di [Go](https://golang.org/) poiché i test case generati da Yeoman sono scritti in Go.

>[!NOTE]
>La maggior parte delle procedure riportate in questa esercitazione prevede l'uso della riga di comando. I passaggi descritti di seguito si applicano a tutti i sistemi operativi e gli strumenti da riga di comando. In questi esempi si è scelto di usare PowerShell, ma ci sono diverse alternative, come Git Bash, il prompt dei comandi di Windows o i comandi della riga di comando di Linux o macOS.

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
>Per verificare che Yeoman ia installato, da una finestra del terminale immettere `yo --version`.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Creare una cartella vuota per contenere il modulo generato da Yeoman

Il modello Yeoman genera i file nella **directory corrente**. Per questo motivo è necessario creare una directory.

>[!Note]
>Questa directory vuota deve essere inserita in $GOPATH/src. Le istruzioni necessarie sono disponibili [qui](https://github.com/golang/go/wiki/SettingGOPATH).

Da un prompt dei comandi:

1. Passare alla directory padre in cui includere la nuova directory vuota che sarà creata.
1. Immettere `mkdir <new-directory-name>`.

    >[!NOTE]
    >Sostituire < new-directory-name > con il nome della nuova directory. In questo esempio la nuova directory viene denominata `GeneratorDocSample`.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Passare alla nuova directory digitando `cd <new directory's name>` e premendo **Invio**.

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

        ![Include il file di immagine Docker?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Immettere `y`. Se si seleziona **n**, il codice di modulo generato supporterà solo l'esecuzione in modalità nativa.

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

## <a name="test-the-module-using-docker"></a>Testare il modulo usando Docker

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

1. Immettere `bundle install`.

    Attendere che sia visualizzato il messaggio **Bundle complete** (Aggregazione completa), quindi continuare con il passaggio successivo.

1. Immettere `rake build`.

    ![Compilazione di Rake](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>Eseguire il test end-to-end

1. Immettere `rake e2e`.

1. Dopo qualche istante sarà visualizzato il messaggio **PASS**.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Immettere `exit` per completare il test end-to-end.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Installare e usare l'estensione Azure Terraform di Visual Studio Code.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
