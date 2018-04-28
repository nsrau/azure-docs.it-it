---
title: Eseguire un processo Apache Spark con il servizio contenitore di Azure (AKS)
description: Usare il servizio di contenitore di Azure (AKS) per eseguire un processo Apache Spark
services: container-service
author: lenadroid
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 627cb80a055fb4b66fcc428a1de87d754af6991e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2018
---
# <a name="running-apache-spark-jobs-on-aks"></a>Esecuzione di processi Apache Spark in AKS

[Apache Spark][apache-spark] è un motore ad alta velocità per l'elaborazione di dati su larga scala. A partire dalla [versione 2.3.0][spark-latest-release], Apache Spark supporta l'integrazione nativa con i cluster Kubernetes. Il servizio di contenitore di Azure (AKS) è un ambiente Kubernetes gestito in esecuzione in Azure. Questo documento descrive la preparazione e l'esecuzione di processi Apache Spark in un cluster del servizio contenitore di Azure (AKS).

## <a name="prerequisites"></a>prerequisiti

Per completare la procedura descritta in questo articolo è necessario quanto segue.

* Conoscenza di base di Kubernetes e [Apache Spark][spark-quickstart].
* Un account [Docker Hub][docker-hub] o un [Registro contenitori di Azure][acr-create].
* Interfaccia della riga di comando di Azure [installata][azure-cli] nel sistema di sviluppo.
* [JDK 8][java-install] installato nel sistema.
* SBT ([strumento di compilazione Scala][sbt-install]) installato nel sistema.
* Strumenti da riga di comando di GIT installati nel sistema.

## <a name="create-an-aks-cluster"></a>Creare un cluster del servizio contenitore di Azure

Spark viene usato per l'elaborazione di dati su larga scala ed è necessario che le dimensioni dei nodi Kubernetes soddisfino i requisiti di Spark in termini di risorse. Per i nodi del servizio contenitore di Azure (AKS) si consigliano dimensione di `Standard_D3_v2`.

Se è necessario un cluster AKS che soddisfi questo requisito minimo, eseguire i comandi seguenti.

Creare un gruppo di risorse per il cluster.

```azurecli
az group create --name mySparkCluster --location eastus
```

Creare il cluster AKS con nodi di dimensioni `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Connettersi al cluster AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Se si usa il Registro contenitori di Azure per archiviare le immagini dei contenitori, configurare l'autenticazione tra AKS e Registro contenitori di Azure. Vedere la [documentazione sull'autenticazione del Registro contenitori di Azure][acr-aks] per questa procedura.

## <a name="build-the-spark-source"></a>Compilare l'origine Spark

Prima di eseguire processi di Spark su un cluster AKS, è necessario compilare il codice sorgente di Spark e crearne il pacchetto in un'immagine contenitore. L'origine Spark contiene script che è possibile usare per completare questo processo.

Clonare il repository del progetto Spark nel sistema di sviluppo.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Passare alla directory del repository clonato e salvare il percorso dell'origine Spark in una variabile.

```bash
cd spark
sparkdir=$(pwd)
```

Se sono installate più versioni di JDK, impostare `JAVA_HOME` in modo da usare la versione 8 per la sessione corrente.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Eseguire il comando seguente per compilare il codice sorgente di Spark con supporto per Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

I comandi seguenti creano le immagini del contenitore di Spark e ne eseguono il push in un registro di immagini contenitore. Sostituire `registry.example.com` con il nome del registro contenitori e `v1` con il tag che si preferisce usare. Se si usa Hub Docker, questo valore è il nome del registro. Se si usa Registro contenitori di Azure, questo valore è il nome del server di accesso del Registro contenitori di Azure.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Eseguire il push dell'immagine del contenitore nel registro di immagini contenitore.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Preparare un processo Spark

Successivamente, preparare un processo Spark. Per contenere il processo Spark viene usato un file JAR, necessario per l'esecuzione del comando `spark-submit`. Il file JAR può essere reso accessibile tramite un URL pubblico o preinserito in un'immagine del contenitore. In questo esempio viene creato un file JAR di esempio per calcolare il valore di pi greco. Il file Jar viene quindi caricato in Archiviazione di Azure. Se si dispone di un file JAR esistente, è possibile usarlo

Creare una directory in cui creare il progetto per un processo Spark.

```bash
mkdir myprojects
cd myprojects
```

Creare un nuovo progetto Scala da un modello.

```bash
sbt new sbt/scala-seed.g8
```

Quando viene richiesto, immettere `SparkPi` come nome del progetto.

```bash
name [Scala Seed Project]: SparkPi
```

Passare alla directory del progetto appena creato.

```bash
cd sparkpi
```

Eseguire i comandi seguenti per aggiungere un plug-in SBT, che consente di creare il pacchetto del progetto come file JAR.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Eseguire questi comandi per copiare il codice di esempio nel progetto appena creato e aggiungere tutte le dipendenze necessarie.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Per creare un pacchetto del progetto in formato JAR, eseguire il comando seguente.

```bash
sbt assembly
```

Dopo la creazione del pacchetto, l'output dovrebbe essere simile al seguente.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Copiare il processo nella risorsa di archiviazione

Creare un account di archiviazione di Azure e un contenitore per il file JAR.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Caricare il file JAR nell'account di archiviazione di Azure con i comandi seguenti.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

La variabile `jarUrl` contiene ora il percorso accessibile pubblicamente del file JAR.

## <a name="submit-a-spark-job"></a>Inviare un processo Spark

Avviare kube-proxy in una riga di comando separata con il codice seguente.

```bash
kubectl proxy
```

Tornare alla radice del repository Spark.

```bash
cd $sparkdir
```

Inviare il processo usando `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Questa operazione avvia il processo Spark, che trasmette lo stato del processo alla sessione della shell. Durante l'esecuzione del processo, è possibile vedere i pod dei processi driver ed executor di Spark usando il comando kubectl get pods. Aprire una seconda sessione terminal per eseguire questi comandi.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Durante l'esecuzione del processo, è anche possibile accedere all'interfaccia utente di Spark. Nella seconda sessione terminal usare il comando `kubectl port-forward` per fornire accesso all'interfaccia utente di Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Per accedere all'interfaccia utente di Spark, aprire l'indirizzo `127.0.0.1:4040` in un Web browser.

![Interfaccia utente di Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Ottenere i log e i risultati del processo

Al termine il processo, il pod dei driver sarà in stato "Completato". Ottenere il nome del pod con il comando seguente.

```bash
kubectl get pods --show-all
```

Output:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Usare il comando `kubectl logs` per ottenere i log dal pod dei driver di Spark. Sostituire il nome del pod con il nome del pod dei driver.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

In questi log si può visualizzare il risultato del processo Spark, che corrisponde al valore di pi greco.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>File JAR del pacchetto con l'immagine del contenitore

Nell'esempio precedente, il file JAR di Spark è stato caricato nell'archiviazione di Azure. Un'altra possibilità è creare un pacchetto del file JAR come immagine Docker personalizzata.

A questo scopo, trovare il `dockerfile` per l'immagine Spark nella directory `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`. Aggiungere un'istruzione `ADD` per il processo Spark `jar` tra le dichiarazioni `WORKDIR` e `ENTRYPOINT`.

Aggiornare il percorso del file JAR nel percorso del file `SparkPi-assembly-0.1.0-SNAPSHOT.jar` nel sistema di sviluppo. È anche possibile usare un file JAR personalizzato.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Compilare ed eseguire il push dell'immagine con gli script Spark inclusi.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Quando si esegue il processo, anziché indicare un URL JAR remoto, è possibile usare lo schema `local://` con il percorso del file JAR nell'immagine Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> La [documentazione][spark-docs] di Spark riporta che l'utilità di pianificazione di Kubernetes è attualmente in fase sperimentale. Nelle versioni future potrebbero essere apportate modifiche funzionali alla configurazione, alle immagini del contenitore e ai punti di ingresso.

## <a name="next-steps"></a>Passaggi successivi

Per altri dettagli, vedere la documentazione di Spark.

> [!div class="nextstepaction"]
> [Documentazione di Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
