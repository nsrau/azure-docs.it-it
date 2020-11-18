---
title: Creare una funzione Java dalla riga di comando - Funzioni di Azure
description: Informazioni su come creare una funzione Java dalla riga di comando e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ms.openlocfilehash: 449f0a59cc8428ce8e19535d5cf0417bf4cf7ad0
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424975"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione Java in Azure dalla riga di comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione Java che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

> [!NOTE]
> Se si preferisce non usare Maven come strumento di sviluppo, vedere le esercitazioni simili per sviluppatori Java che usano [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) e [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x.

+ L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva.

+ [Java Developer Kit](https://aka.ms/azure-jdks), versione 8 o 11. È necessario impostare la variabile di ambiente `JAVA_HOME` sul percorso di installazione della versione corretta di JDK.     

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

+ In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 3.x.

+ Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.4 o successiva.

+ Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

## <a name="create-a-local-function-project"></a>Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

1. In una cartella vuota eseguire il comando seguente per generare il progetto di Funzioni da un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```
    
    ---

    > [!IMPORTANT]
    > + Usare `-DjavaVersion=11` se si vuole che le funzioni vengano eseguite in Java 11. Per altre informazioni, vedere [Versioni di Java](functions-reference-java.md#java-versions). 
    > + Per completare questo articolo, è necessario impostare la variabile di ambiente `JAVA_HOME` sul percorso di installazione della versione corretta di JDK.

1. Maven chiede i valori necessari per completare la generazione del progetto nella distribuzione.   
    Quando richiesto, specificare i valori seguenti:

    | Prompt | valore | Descrizione |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Un valore che identifica in modo univoco il progetto tra tutti gli altri, seguendo le [regole di denominazione dei pacchetti](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) per Java. |
    | **artifactId** | `fabrikam-functions` | Un valore che corrisponde al nome del jar, senza un numero di versione. |
    | **version** | `1.0-SNAPSHOT` | Scegliere il valore predefinito. |
    | **package** | `com.fabrikam` | Un valore che corrisponde al pacchetto Java per il codice della funzione generato. Usare il valore predefinito. |

1. Digitare `Y` o premere INVIO per confermare.

    Maven crea i file di progetto in una nuova cartella denominata _artifactId_, che in questo esempio è `fabrikam-functions`. 

1. Passare alla cartella del progetto:

    ```console
    cd fabrikam-functions
    ```

    Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.

### <a name="optional-examine-the-file-contents"></a>(Facoltativo) Esaminare il contenuto del file

Se si vuole, è possibile passare a [Eseguire la funzione localmente](#run-the-function-locally) ed esaminare il contenuto del file in un secondo momento.

#### <a name="functionjava"></a>Function.java
*Function.java* contiene un metodo `run` che riceve i dati della richiesta nella variabile `request` come oggetto [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) decorato con l'annotazione [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), che definisce il comportamento del trigger. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Il messaggio di risposta viene generato dall'API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

#### <a name="pomxml"></a>pom.xml

Le impostazioni per le risorse di Azure create per ospitare l'app sono definite nell'elemento **configuration** del plug-in con un elemento **groupId** di `com.microsoft.azure` nel file pom.xml generato. Ad esempio, l'elemento configuration seguente indica a una distribuzione basata su Maven di creare un'app per le funzioni nel gruppo di risorse `java-functions-group` nell'area `westus`. L'app per le funzioni viene eseguita in Windows ospitata nel piano `java-functions-app-service-plan`, che per impostazione predefinita è un piano a consumo serverless.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

È possibile modificare queste impostazioni per controllare la modalità di creazione delle risorse in Azure, ad esempio cambiando `runtime.os` da `windows` a `linux` prima della distribuzione iniziale. Per un elenco completo delle impostazioni supportate dal plug-in Maven, vedere i [dettagli di configurazione](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

L'archetipo genera anche uno unit test per la funzione. Quando si modifica la funzione per aggiungere i binding o si aggiungono nuove funzioni al progetto, sarà necessario modificare anche i test nel file *FunctionTest.java*.

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

1. Eseguire la funzione avviando l'host di runtime locale di Funzioni di Azure nella cartella *LocalFunctionProj*:

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    Verso la fine dell'output, verranno visualizzate le righe seguenti:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Se HttpExample non viene visualizzato come illustrato di seguito, è probabile che l'host non sia stato avviato dalla cartella radice del progetto. In tal caso, premere **CTRL**+**C** per arrestare l'host, passare alla cartella radice del progetto ed eseguire di nuovo il comando precedente.

1. Copiare l'URL della funzione `HttpExample` da questo output in un browser e aggiungere la stringa di query `?name=<YOUR_NAME>`, rendendo l'URL completo come `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser dovrebbe essere visualizzato un messaggio simile a `Hello Functions`:

    ![Risultato della funzione eseguita in locale nel browser](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Il terminale in cui è stato avviato il progetto mostra anche l'output del log quando si effettuano le richieste.

1. Al termine, premere **CTRL**+**C** e scegliere `y` per arrestare l'host di Funzioni.

## <a name="deploy-the-function-project-to-azure"></a>Distribuire il progetto di funzione in Azure

La prima volta che si distribuisce il progetto di Funzioni, in Azure vengono create un'app per le funzioni e le risorse correlate. Le impostazioni per le risorse di Azure create per ospitare l'app sono definite nel [file pom.xml](#pomxml). In questo articolo verranno accettate le impostazioni predefinite.

> [!TIP]
> Per creare un'app per le funzioni in esecuzione in Linux anziché in Windows, modificare l'elemento `runtime.os` nel file pom.xml da `windows` a `linux`. L'esecuzione di Linux in un piano a consumo è supportata in [queste aree](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Non è possibile avere nello stesso gruppo di risorse app eseguite in Linux e app eseguite in Windows.

1. Prima di procedere alla distribuzione, è necessario usare il comando [az login](/cli/azure/authenticate-azure-cli) dell'interfaccia della riga di comando di Azure per accedere alla sottoscrizione di Azure. 

    ```azurecli
    az login
    ```

1. Usare il comando seguente per distribuire il progetto in una nuova app per le funzioni.

    ```console
    mvn azure-functions:deploy
    ```
    
    In Azure verranno create le risorse seguenti:
    
    + Gruppo di risorse. Il nome è _java-functions-group_.
    + Account di archiviazione. Richiesto da Funzioni. Il nome viene generato in modo casuale in base ai requisiti di denominazione degli account di archiviazione.
    + Piano di hosting. Hosting serverless per l'app per le funzioni nell'area _westus_. Il nome è _java-functions-app-service-plan_.
    + App per le funzioni. Un'app per le funzioni è l'unità di distribuzione ed esecuzione per le funzioni. Il nome viene generato in modo casuale in base al valore di _artifactId_, a cui viene aggiunto un numero generato in modo casuale.
    
    La distribuzione inserisce i file di progetto in un pacchetto e li distribuisce nella nuova app per le funzioni tramite [ZipDeploy](functions-deployment-technologies.md#zip-deploy). Il codice viene eseguito dal pacchetto di distribuzione in Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si continua con il [passaggio successivo](#next-steps) e si aggiunge un binding di output della coda di Archiviazione di Azure, mantenere tutte le risorse esistenti per poterle riutilizzare.

In caso contrario, usare il comando seguente per eliminare il gruppo di risorse e tutte le relative risorse contenute per evitare di incorrere in costi aggiuntivi.

 # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
