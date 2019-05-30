---
title: Creare un contenitore di Azure Service Fabric per il server Apache Tomcat in Linux | Microsoft Docs
description: Creare un contenitore Linux per esporre un'applicazione in esecuzione nel server Apache Tomcat su Azure Service Fabric. Creare un'immagine Docker con l'applicazione e il server Apache Tomcat, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: df3156688f018aee4717271557220396827dd9e2
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306823"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Creare un contenitore di Service Fabric in esecuzione nel server Apache Tomcat su Linux
Apache Tomcat è una nota implementazione open source delle tecnologie Java Servlet e Java Server. Questo articolo illustra come creare un contenitore con Apache Tomcat e una semplice applicazione Web, come distribuire il contenitore in un cluster di Service Fabric in esecuzione su Linux e come connettersi all'applicazione Web.  

Per altre informazioni su Apache Tomcat, vedere la [home page di Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Prerequisiti
* Un computer di sviluppo che esegue:
  * [SDK e strumenti di Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE per Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

* Un registro contenitori in Registro Azure Container. È possibile creare un registro contenitori nella sottoscrizione di Azure con il [portale di Azure](../container-registry/container-registry-get-started-portal.md) o l'[interfaccia della riga di comando di Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Creare un'immagine Tomcat ed eseguirla in locale
Seguire i passaggi descritti in questa sezione per creare un'immagine Docker basata su un'immagine Apache Tomcat e una semplice app Web e quindi eseguirla in un contenitore nel sistema locale. 
 
1. Clonare il repository degli esempi [Service Fabric getting started with Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) (Introduzione a Service Fabric con Java) nel computer di sviluppo.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Modificare le directory impostandole sulla directory di esempio del server Apache Tomcat (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Creare un file Docker basato sull'[immagine Tomcat](https://hub.docker.com/_/tomcat/) ufficiale nell'hub Docker e sull'esempio del server Tomcat. Nella directory *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* creare un file denominato *Dockerfile* (senza estensione). Aggiungere quanto segue a *Dockerfile* e salvare le modifiche:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Per altre informazioni, vedere [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informazioni di riferimento su Dockerfile).


4. Eseguire il comando `docker build` per creare l'immagine che esegue l'applicazione Web:

   ```bash
   docker build . -t tomcattest
   ```

   Questo comando crea la nuova immagine usando le istruzioni contenute nel Dockerfile e assegna un nome all'immagine `tomcattest` aggiungendo il tag -t. Per creare un'immagine del contenitore, viene prima scaricata l'immagine di base dall'hub Docker e quindi viene aggiunta l'applicazione all'immagine. 

   Al termine dell'esecuzione del comando di compilazione, eseguire il comando `docker images` per visualizzare le informazioni sulla nuova immagine:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Verificare che l'applicazione in contenitori venga eseguita in locale prima di eseguirne il push nel registro contenitori:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` assegna un nome al contenitore, è quindi possibile farvi riferimento usando un nome descrittivo anziché il relativo ID.
   * `-p` specifica il mapping delle porte tra il contenitore e il sistema operativo host. 

   > [!Note]
   > La porta che si apre con il parametro `-p` deve essere la porta su cui l'applicazione Tomcat è in ascolto delle richieste. Nell'esempio corrente è presente un connettore configurato nel file *ApacheTomcat/conf/server.xml* per l'ascolto sulla porta 8080 per le richieste HTTP. Di questa porta viene eseguito il mapping alla porta 8080 nell'host. 

   Per informazioni sugli altri parametri, vedere la [documentazione di Docker run](https://docs.docker.com/engine/reference/commandline/run/).

1. Per testare il contenitore, aprire un browser e immettere uno degli URL seguenti. Verrà visualizzata una variante della schermata iniziale "Hello World!" per ogni URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Arrestare il contenitore ed eliminarlo dal computer di sviluppo:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Eseguire il push dell'immagine Tomcat nel registro contenitori
Dopo aver verificato che l'immagine Tomcat venga eseguita in un contenitore nel computer di sviluppo, eseguirne il push in un repository in un registro contenitori. Questo articolo usa Registro Azure Container per archiviare l'immagine, ma è possibile usare qualsiasi registro contenitori modificando alcuni passaggi. Questo articolo presuppone che il nome del registro sia *myregistry* e che il nome completo del registro sia myregistry.azurecr.io. Modificare queste impostazioni in base al proprio scenario. 

1. Eseguire `docker login` per accedere al registro contenitori con il [le credenziali del Registro di sistema](../container-registry/container-registry-authentication.md).

   L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione. In alternativa, è possibile accedere usando il nome utente del Registro di sistema e la password.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Il comando seguente crea un tag, o alias, dell'immagine, con un percorso completo del registro. Questo esempio inserisce l'immagine dello spazio dei nomi `samples` per evitare confusione nella radice del registro.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Effettuare il push dell'immagine nel registro contenitori:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Creare e distribuire l'applicazione contenitore di Service Fabric
Dopo aver eseguito il push dell'immagine Tomcat in un registro contenitori, è possibile creare e distribuire un'applicazione contenitore di Service Fabric che esegue il pull dell'immagine Tomcat dal registro e la esegue come servizio in contenitore nel cluster. 

1. Creare una nuova directory all'esterno del clone locale (all'esterno dell'albero della directory *service-fabric-java-getting-started*). Passare a questa directory e usare Yeoman per creare uno scaffold per un'applicazione contenitore: 

   ```bash
   yo azuresfcontainer 
   ```
   Quando richiesto, immettere i valori seguenti:

   * Nome dell'applicazione: ServiceFabricTomcat
   * Nome del servizio dell'applicazione: TomcatService
   * Immettere il nome dell'immagine: Specificare l'URL dell'immagine del contenitore in Registro contenitori. ad esempio, myregistry.azurecr.io/samples/tomcattest.
   * Comandi: Lasciare vuoto. Dato che per l'immagine è stato definito un punto di ingresso del carico di lavoro, non è necessario specificare in modo esplicito i comandi di input, che vengono eseguiti all'interno del contenitore in modo che la relativa esecuzione continui dopo l'avvio.
   * Numero di istanze dell'applicazione contenitore guest: 1

   ![Generatore Yeoman di Service Fabric per i contenitori](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. Nel manifesto del servizio (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) aggiungere il codice XML seguente nel tag radice **ManifestoDelServizio** per aprire la porta su cui l'applicazione è in ascolto delle richieste. Il tag **Endpoint** dichiara il protocollo e la porta dell'endpoint. Per questo articolo, il servizio in contenitore è in ascolto sulla porta 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. Nel manifesto dell'applicazione (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*), nel tag **ServiceManifestImport**, aggiungere il codice XML seguente. Sostituire il **NomeAccount** e **Password** nel **RepositoryCredentials** tag con il nome del Registro di contenitori e la password necessaria per l'accesso.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Il tag **ContainerHostPolicies** specifica i criteri per l'attivazione degli host dei contenitori.
    
   * Il tag **PortBinding** configura i criteri di mapping tra porta del contenitore e porta dell'host. L'attributo **ContainerPort** è impostato su 8080 perché il contenitore espone la porta 8080, come specificato nel Dockerfile. L'attributo **EndpointRef** è impostato su "endpointTest", l'endpoint definito nel manifesto del servizio nel passaggio precedente. Quindi, per le richieste in ingresso al servizio sulla porta 8080 viene eseguito il mapping alla porta 8080 del contenitore. 
   * Il tag **RepositoryCredentials** specifica le credenziali necessarie al contenitore per eseguire l'autenticazione con il repository (privato) da cui esegue il pull dell'immagine. Questo criterio non è necessario se il pull dell'immagine viene eseguito da un repository pubblico.
    

12. Nella cartella *ServiceFabricTomcat* connettersi al cluster di Service Fabric. 

   * Per connettersi al cluster locale di Service Fabric, eseguire:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Per connettersi a un cluster sicuro di Azure, assicurarsi che il certificato client sia presente come file con estensione pem nella directory *ServiceFabricTomcat* ed eseguire: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Nel comando precedente sostituire `your-certificate.pem` con il nome del file del certificato client. Negli ambienti di sviluppo e test, il certificato del cluster viene spesso usato come certificato client. Se il certificato non è autofirmato, omettere il parametro `-no-verify`. 
       
     I certificati del cluster vengono in genere scaricati in locale come file con estensione pfx. Se non si ha già il certificato in formato PEM, è possibile eseguire il comando seguente per creare un file con estensione pem da un file con estensione pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Se il file con estensione pfx non è protetto da password, usare `-passin pass:` per l'ultimo parametro.


13. Eseguire lo script di installazione fornito nel modello per distribuire l'applicazione nel cluster. Lo script copia il pacchetto dell'applicazione nell'archivio immagini del cluster, registra il tipo di applicazione e crea un'istanza dell'applicazione.

     ```bash
     ./install.sh
     ```

   Dopo aver eseguito lo script di installazione, aprire un browser e passare a Service Fabric Explorer:
    
   * In un cluster locale usare `http://localhost:19080/Explorer`. Sostituire *localhost* con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X.
   * In un cluster sicuro di Azure usare `https://PublicIPorFQDN:19080/Explorer`. 
    
   Espandere il nodo delle **applicazioni**, nel quale sarà ora presente una voce per il tipo di applicazione, **ServiceFabricTomcatType**, e un'altra per la prima istanza del tipo. La distribuzione completa dell'applicazione può richiedere alcuni minuti.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Per accedere all'applicazione nel server Tomcat, aprire una finestra del browser e immettere uno degli URL seguenti. Se la distribuzione è stata eseguita nel cluster locale, usare *localhost* per *PublicIPorFQDN*. Verrà visualizzata una variante della schermata iniziale "Hello World!" per ogni URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Eseguire la pulizia
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione dal cluster e annullare la registrazione del tipo di applicazione.

```bash
./uninstall.sh
```

Dopo aver effettuato il push dell'immagine nel registro contenitori, è possibile eliminare l'immagine locale dal computer di sviluppo:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Passaggi successivi
* Per azioni rapide su altre funzionalità dei contenitori Linux, leggere [Creare la prima applicazione contenitore di Service Fabric in Linux](service-fabric-get-started-containers-linux.md).
* Per informazioni più dettagliate sui contenitori Linux, vedere l'esercitazione [Creare un'app contenitore Linux](service-fabric-tutorial-create-container-images.md).
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).


