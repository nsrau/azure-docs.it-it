---
title: Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4
description: Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, JavaEE, microprofile, Open Liberty, WebSphere-Liberty, Aro, OpenShift, Red Hat
ms.openlocfilehash: 41891b58942efbfd705747cc16219185f2a2daa2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018393"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Distribuire un'applicazione Java con Open Liberty/WebSphere Liberty in un cluster Azure Red Hat OpenShift 4

Questa guida illustra come eseguire l'applicazione Java, Java EE, [Jakarta EE](https://jakarta.ee/)o [Microprofile](https://microprofile.io/) in Open Liberty/WebSphere Liberty Runtime e quindi distribuire l'applicazione in contenitori in un cluster Azure Red Hat OpenShift (Aro) 4 usando l'operatore Open Liberty. Questo articolo illustra in modo dettagliato la preparazione di un'applicazione Liberty, la compilazione dell'immagine Docker dell'applicazione e l'esecuzione dell'applicazione in contenitori in un cluster ARO 4.  Per altri dettagli su Open Liberty, vedere [la pagina Open Liberty Project](https://openliberty.io/). Per altri dettagli su IBM WebSphere Liberty, vedere [la pagina del prodotto WebSphere Liberty](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Prerequisiti

Per esaminare correttamente questa guida, completare i prerequisiti seguenti.

> [!NOTE]
> Azure Red Hat OpenShift richiede almeno 40 core per creare ed eseguire un cluster OpenShift. La quota di risorse di Azure predefinita per una nuova sottoscrizione di Azure non soddisfa questo requisito. Per richiedere un aumento del limite di risorse, vedere [Quota standard: aumentare i limiti per serie di macchine virtuali](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests). Si noti che la sottoscrizione di valutazione gratuita non è idonea per un aumento della quota, è [possibile eseguire l'aggiornamento a una sottoscrizione con pagamento in base al](https://docs.microsoft.com/azure/cost-management-billing/manage/upgrade-azure-subscription) consumo prima di richiedere un aumento della quota.

1. Preparare un computer locale con un sistema operativo simile a UNIX installato (ad esempio, Ubuntu, macOS).
1. Installare un'implementazione di Java SE (ad esempio, [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Installare [Maven](https://maven.apache.org/download.cgi) 3.5.0 o versione successiva.
1. Installare [Docker](https://docs.docker.com/get-docker/) per il sistema operativo.
1. Installare l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 2.0.75.
1. Controllare e installare [`envsubst`](https://command-not-found.com/envsubst) se non è preinstallato nel sistema operativo.
1. Clonare il codice per questo esempio nel sistema locale. L'esempio è su [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Seguire le istruzioni riportate in [creare un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-create-cluster).

   Sebbene il passaggio "Get a Red Hat pull Secret" sia contrassegnato come facoltativo, è **obbligatorio per questo articolo**.  Il segreto di pull consente al cluster OpenShift di Azure Red Hat di trovare l'operatore Open Liberty.

   Se si prevede di eseguire applicazioni con utilizzo intensivo della memoria nel cluster, specificare le dimensioni appropriate della macchina virtuale per i nodi di lavoro utilizzando il `--worker-vm-size` parametro. Ad esempio, `Standard_E4s_v3` è la dimensione minima della macchina virtuale per installare l'operatore elasticsearch in un cluster. Per altre informazioni, vedere:

   * [INTERFACCIA della riga di comando di Azure per creare un cluster](https://docs.microsoft.com/cli/azure/aro?view=azure-cli-latest&preserve-view=true#az-aro-create)
   * [Dimensioni delle macchine virtuali supportate per l'ottimizzazione della memoria](/azure/openshift/support-policies-v4#memory-optimized)
   * [Prerequisiti per l'installazione dell'operatore elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Connettersi al cluster attenendosi alla procedura descritta in [connettersi a un cluster Azure Red Hat OpenShift 4](/azure/openshift/tutorial-connect-cluster).
   * Assicurarsi di seguire la procedura descritta in "installare l'interfaccia della riga di comando di OpenShift" perché il comando verrà usato `oc` più avanti in questo articolo.
   * Annotare l'URL della console del cluster simile a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Prendere nota delle `kubeadmin` credenziali.

1. Verificare che sia possibile accedere all'interfaccia della riga di comando di OpenShift con il token per l'utente `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Abilitare il registro contenitori incorporato per OpenShift

La procedura descritta in questa esercitazione crea un'immagine Docker di cui è necessario eseguire il push in un registro contenitori accessibile a OpenShift. L'opzione più semplice consiste nell'usare il registro di sistema incorporato fornito da OpenShift. Per abilitare il registro contenitori incorporato, seguire i passaggi in configurare il [Registro contenitori incorporato per Azure Red Hat OpenShift 4](built-in-container-registry.md). In questo articolo vengono usati tre elementi di questi passaggi.

* Nome utente e password dell'utente Azure AD per l'accesso alla console Web di OpenShift.
* L'output di `oc whoami` dopo aver seguito la procedura per accedere all'interfaccia della riga di comando di OpenShift.  Questo valore viene chiamato **AAD-User** per la discussione.
* URL del registro contenitori.

Si noti che questi elementi sono inattivi quando si completano i passaggi per abilitare il registro contenitori incorporato.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Creare uno spazio dei nomi OpenShift per l'app java

1. Accedere alla console Web di OpenShift dal browser usando le `kubeadmin` credenziali.
2. Passare a **Amministrazione**  >  **spazi dei** nomi  >  **Crea spazio dei nomi**.
3. Compilare `open-liberty-demo` per **nome** e selezionare **Crea**, come mostrato di seguito.

   ![Crea spazio dei nomi](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Creare un amministratore per il progetto demo

Oltre alla gestione delle immagini, all' **utente di AAD** verranno concesse anche le autorizzazioni amministrative per la gestione delle risorse nel progetto demo del cluster Aro 4.  Accedere all'interfaccia della riga di comando di OpenShift e concedere all' **utente di AAD** i privilegi necessari seguendo questa procedura.

1. Accedere alla console Web di OpenShift dal browser usando le `kubeadmin` credenziali.
1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente che ha eseguito l'accesso, quindi selezionare **Copy login Command**.
1. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
1. Selezionare **Visualizza token**.
1. Copiare il valore elencato sotto **login con questo token** negli Appunti ed eseguirlo in una shell, come illustrato di seguito.
1. Eseguire i comandi seguenti per concedere `admin` il ruolo all' **utente AAD** nello spazio dei nomi `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Installare l'operatore Open Liberty OpenShift

Dopo la creazione e la connessione al cluster, installare l'operatore Open Liberty.  La pagina iniziale principale per l'operatore Open Liberty è su [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Accedere alla console Web di OpenShift dal browser usando le `kubeadmin` credenziali.
2. Passare a **Operators**  >  **OperatorHub** e cercare **Open Liberty operator**.
3. Selezionare **Apri operatore Liberty** nei risultati della ricerca.
4. Selezionare **Installa**.
5. Nella sottoscrizione popup **create operator** selezionare tutti gli **spazi dei nomi nel cluster (impostazione predefinita)** per la **modalità di installazione**, la **versione beta** per il **canale di aggiornamento** e la strategia **automatica** per l' **approvazione**:

   ![creazione della sottoscrizione operator per l'operatore Open Liberty](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Selezionare **Subscribe (Sottoscrivi** ) e attendere un minuto o due finché non viene visualizzato l'operatore Open Liberty.
7. Osservare l'operatore Open Liberty con lo stato "succeeded".  In caso contrario, diagnosticare e risolvere il problema prima di continuare.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Viene installato un operatore installato che mostra la libertà di apertura.":::

## <a name="prepare-the-liberty-application"></a>Preparare l'applicazione Liberty

Si userà un'applicazione Java EE 8 come esempio in questa guida. Open Liberty è un server compatibile con [il profilo completo Java EE 8](https://javaee.github.io/javaee-spec/javadocs/) , che consente di eseguire facilmente l'applicazione.  Open Liberty è anche [Jakarta EE 8 full profile compatible](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Esegui l'applicazione in libertà aperta

Per eseguire l'applicazione in Open Liberty, è necessario creare un file di configurazione open Liberty server in modo che il plug-in [Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) possa comprimere l'applicazione per la distribuzione. Il plug-in Liberty Maven non è necessario per distribuire l'applicazione in OpenShift.  Tuttavia, verrà usato in questo esempio con la modalità Developer (sviluppo) di Open Liberty.  La modalità sviluppatore consente di eseguire facilmente l'applicazione in locale. Completare i passaggi seguenti nel computer locale.

1. Copiare `2-simple/src/main/liberty/config/server.xml` in `1-start/src/main/liberty/config` , sovrascrivendo il file di lunghezza zero esistente. Questo consente `server.xml` di configurare il server Open Liberty con le funzionalità Java EE.
1. Copiare `2-simple/pom.xml` in `1-start/pom.xml` .  Questo passaggio aggiunge al `liberty-maven-plugin` POM.
1. Passare alla directory `1-start` del clone locale.
1. Eseguire `mvn clean package` in una console di per generare un pacchetto War `javaee-cafe.war` nella directory `./target` .
1. Eseguire `mvn liberty:dev` per avviare Open Liberty in modalità dev.
1. Attendere l'avvio del server. L'output della console deve terminare con il messaggio seguente:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Aprire `http://localhost:9080/` nel browser per visitare l'applicazione Home page. L'applicazione sarà simile all'immagine seguente:

   ![Interfaccia utente Web di JavaEE Cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Premere **CTRL-C** per arrestare l'applicazione e aprire Liberty server.

La directory `2-simple` del clone locale Mostra il progetto Maven con le modifiche precedenti già applicate.

## <a name="prepare-the-application-image"></a>Preparare l'immagine dell'applicazione

Per distribuire ed eseguire l'applicazione Liberty in un cluster ARO 4, distribuire l'applicazione come immagine Docker usando le [Immagini del contenitore open Liberty](https://github.com/OpenLiberty/ci.docker) o [WebSphere Liberty container](https://github.com/WASdev/ci.docker)images.

### <a name="build-application-image"></a>Crea immagine applicazione

Completare i passaggi seguenti per compilare l'immagine dell'applicazione:

1. Passare alla directory `2-simple` del clone locale.
2. Eseguire `mvn clean package` per creare il pacchetto dell'applicazione.
3. Eseguire uno dei comandi seguenti per compilare l'immagine dell'applicazione.
   * Compila con immagine di base Open Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Compila con l'immagine di base di WebSphere Liberty:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Eseguire l'applicazione localmente con Docker

Prima di distribuire l'applicazione in contenitori in un cluster remoto, eseguire con il Docker locale per verificare se funziona:

1. Eseguire `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` nella console di.
2. Attendere l'avvio di Liberty server e l'applicazione per la distribuzione corretta.
3. Aprire `http://localhost:9080/` nel browser per visitare l'applicazione Home page.
4. Premere **CTRL-C** per arrestare l'applicazione e il server di libertà.

### <a name="push-the-image-to-the-container-image-registry"></a>Eseguire il push dell'immagine nel registro immagini del contenitore

Quando si è soddisfatti dello stato dell'applicazione, effettuarne il push nel registro immagini del contenitore predefinito seguendo le istruzioni riportate di seguito.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Accedere all'interfaccia della riga di comando di OpenShift come utente Azure AD

1. Accedere alla console Web di OpenShift dal browser usando le credenziali di un utente Azure AD.

   1. Usare una funzionalità InPrivate, incognito o un'altra finestra del browser equivalente per accedere alla console.
   1. Selezionare **OpenID**

   > [!NOTE]
   > Prendere nota del nome utente e della password usati per accedere a questa pagina. Questo nome utente e la password funzioneranno come amministratore per altre azioni in questo e in altri articoli.
1. Accedere con l'interfaccia della riga di comando di OpenShift usando la procedura seguente.  Per la discussione, questo processo è noto come `oc login` .
   1. Nella parte superiore destra della console Web espandere il menu di scelta rapida dell'utente che ha eseguito l'accesso, quindi selezionare **Copy login Command**.
   1. Accedere a una nuova finestra della scheda con lo stesso utente, se necessario.
   1. Selezionare **Visualizza token**.
   1. Copiare il valore elencato sotto **login con questo token** negli Appunti ed eseguirlo in una shell, come illustrato di seguito.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Eseguire il push dell'immagine del contenitore nel registro contenitori per OpenShift

Eseguire questi comandi per eseguire il push dell'immagine nel registro contenitori per OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

L'output riuscito avrà un aspetto simile al seguente.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Eseguire il push dell'immagine nel registro immagini del contenitore incorporato con il comando seguente.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Distribuire un'applicazione nel cluster ARO 4

A questo punto è possibile distribuire l'applicazione Liberty di esempio nel cluster Azure Red Hat OpenShift 4 creato in precedenza durante l'esecuzione dei prerequisiti.

### <a name="deploy-the-application-from-the-web-console"></a>Distribuire l'applicazione dalla console Web

Poiché si usa l'operatore Open Liberty per gestire le applicazioni Liberty, è necessario creare un'istanza della relativa *definizione di risorsa personalizzata*, di tipo "OpenLibertyApplication". L'operatore si occuperà quindi di tutti gli aspetti della gestione delle risorse OpenShift necessarie per la distribuzione.

1. Accedere alla console Web di OpenShift dal browser usando le credenziali dell'utente Azure AD.
1. Espandere **Home**, selezionare **progetti**  >  **Apri-libertà-demo**.
1. Passare a **Operators**  >  **installato Operators**.
1. Al centro della pagina, selezionare **Apri operatore di libertà**.
1. Al centro della pagina selezionare **Apri applicazione Liberty**.  La navigazione degli elementi nell'interfaccia utente rispecchia la gerarchia di contenimento effettiva delle tecnologie in uso.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Contenimento di ARO Java](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selezionare **Crea OpenLibertyApplication**
1. Sostituire l'oggetto YAML generato con il proprio, disponibile in `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selezionare **Crea**. Verrà restituito l'elenco di OpenLibertyApplications.
1. Selezionare **JavaEE-Café-Simple**.
1. Al centro della pagina selezionare **risorse**.
1. Nella tabella selezionare il collegamento per **JavaEE-Café-Simple** con il **tipo** di **Route**.
1. Nella pagina visualizzata selezionare il collegamento sotto il **percorso**.

Si noterà che l'applicazione home page aperta nel browser.

### <a name="delete-the-application-from-the-web-console"></a>Eliminare l'applicazione dalla console Web

Al termine dell'applicazione, attenersi alla procedura seguente per eliminare l'applicazione dal turno aperto.

1. Nel riquadro di spostamento a sinistra espandere la voce per **gli operatori**.
1. Selezionare **operatori installati**.
1. Selezionare **Open Liberty operator**.
1. Nella parte centrale della pagina selezionare **Open Liberty Application**.
1. Selezionare i puntini di sospensione verticali (tre punti verticali), quindi selezionare **Elimina applicazione OpenLiberty**.

### <a name="deploy-the-application-from-cli"></a>Distribuire l'applicazione dall'interfaccia della riga di comando

Invece di usare l'interfaccia utente grafica della console Web, è possibile distribuire l'applicazione dall'interfaccia della riga di comando. Se non è già stato fatto, scaricare e installare lo `oc` strumento da riga di comando seguendo la documentazione di Red Hat [Introduzione con l'interfaccia della](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)riga di comando.

1. Accedere alla console Web di OpenShift dal browser usando le credenziali dell'utente Azure AD.
2. Accedere all'interfaccia della riga di comando di OpenShift con il token per l'utente Azure AD.
3. Passare alla directory `2-simple` del clone locale ed eseguire i comandi seguenti per distribuire l'applicazione Liberty nel cluster Aro 4.  L'output del comando viene inoltre visualizzato inline.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. `1/1`Prima di continuare, verificare che sia visualizzata sotto la `READY` colonna.  In caso contrario, analizzare e risolvere il problema prima di continuare.
5. Individuare l'host di route per l'applicazione con il `oc get route` comando, come illustrato di seguito.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Quando l'applicazione Liberty è attiva e in esecuzione, aprire l'output dell' **host di route** nel browser per visitare l'applicazione Home page.

### <a name="delete-the-application-from-cli"></a>Eliminare l'applicazione dall'interfaccia della riga di comando

Eliminare l'applicazione dall'interfaccia della riga di comando eseguendo questo comando.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il cluster ARO attenendosi alla procedura descritta in [esercitazione: eliminare un cluster di Azure Red Hat OpenShift 4](/azure/openshift/tutorial-delete-cluster)

## <a name="next-steps"></a>Passaggi successivi

In questa guida si è appreso come:
> [!div class="checklist"]
>
> * Preparare l'applicazione Liberty
> * Compilare l'immagine dell'applicazione
> * Eseguire l'applicazione in contenitori in un cluster ARO 4 usando GUI e l'interfaccia della riga di comando

Per ulteriori informazioni, vedere i riferimenti utilizzati in questa guida:

* [Apri libertà](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Apri operatore Liberty](https://github.com/OpenLiberty/open-liberty-operator)
* [Apri configurazione di Liberty server](https://openliberty.io/docs/ref/config/)
* [Plug-in Liberty Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Apri le immagini del contenitore Liberty](https://github.com/OpenLiberty/ci.docker)
* [Immagini del contenitore di WebSphere Liberty](https://github.com/WASdev/ci.docker)
