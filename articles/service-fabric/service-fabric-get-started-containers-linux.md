---
title: Creare un'applicazione contenitore di Azure Service Fabric in Linux | Microsoft Docs
description: Creare la prima applicazione contenitore Linux in Azure Service Fabric.  Compilare un'immagine Docker con l'applicazione, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: ryanwi
ms.openlocfilehash: 0e7e0f1262ee8c31bc6e71b49e9ef62129887f2c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Creare la prima applicazione contenitore di Service Fabric in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Per eseguire un'applicazione esistente in un contenitore Linux in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questo articolo illustra come creare un'immagine Docker contenente un'applicazione Web Python [Flask](http://flask.pocoo.org/) e come distribuirla in un cluster di Service Fabric.  Si condividerà anche l'applicazione in contenitore tramite [Registro contenitori di Azure](/azure/container-registry/).  L'articolo presuppone una conoscenza di base di Docker. Per informazioni su Docker, vedere [Docker overview](https://docs.docker.com/engine/understanding-docker/) (Panoramica su Docker).

## <a name="prerequisites"></a>prerequisiti
* Un computer di sviluppo che esegue:
  * [SDK e strumenti di Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE per Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

* Un registro all'interno del registro contenitori di Azure. A questo scopo, [creare un registro contenitori](../container-registry/container-registry-get-started-portal.md) nella sottoscrizione di Azure. 

## <a name="define-the-docker-container"></a>Definire il contenitore Docker
Compilare un'immagine in base all'[immagine Python](https://hub.docker.com/_/python/) disponibile nell'hub Docker. 

Definire il contenitore Docker in un Dockerfile. Il Dockerfile contiene istruzioni per la configurazione dell'ambiente all'interno del contenitore, il caricamento dell'applicazione da eseguire e il mapping delle porte. Il file Dockerfile rappresenta l'input per il comando `docker build` che crea l'immagine. 

Creare una directory vuota e il file *Dockerfile* (senza estensione file). Aggiungere quanto segue a *Dockerfile* e salvare le modifiche:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Per altre informazioni, vedere [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informazioni di riferimento su Dockerfile).

## <a name="create-a-simple-web-application"></a>Creare un'applicazione Web semplice
Creare un'applicazione Web Flask in ascolto sulla porta 80 che restituisce "Hello World!".  Nella stessa directory creare il file *requirements.txt*.  Aggiungere quanto segue e salvare le modifiche:
```
Flask
```

Creare anche il file *app.py* e aggiungere quanto segue:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Compilare l'immagine
Eseguire il comando `docker build` per creare l'immagine che esegue l'applicazione Web. Aprire una finestra di PowerShell e passare a *c:\temp\helloworldapp*. Eseguire il comando seguente:

```bash
docker build -t helloworldapp .
```

Questo comando compila la nuova immagine usando le istruzioni contenute nel file Dockerfile e denomina l'immagine "helloworldapp" , ovvero le assegna un tag -t. La compilazione di un'immagine determina il pull dell'immagine di base dall'hub Docker e la creazione di una nuova immagine che aggiunge l'applicazione sopra l'immagine di base.  

Al termine dell'esecuzione del comando di compilazione, eseguire il comando `docker images` per visualizzare le informazioni sulla nuova immagine:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
Verificare l'esecuzione dell'applicazione in contenitore in locale prima di eseguirne il push nel registro contenitori.  

Eseguire l'applicazione, effettuando il mapping della porta 4000 del computer alla porta 80 esposta dal contenitore:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* assegna un nome al contenitore in esecuzione, anziché l'ID contenitore.

Connettersi al contenitore in esecuzione.  Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 4000, ad esempio "http://localhost:4000". Verrà visualizzata l'intestazione "Hello World!" nel browser.

![Hello World!][hello-world]

Per arrestare il contenitore, eseguire:

```bash
docker stop my-web-site
```

Per eliminare il contenitore dal computer di sviluppo, eseguire:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Effettuare il push dell'immagine nel registro contenitori
Dopo aver verificato l'esecuzione dell'applicazione in Docker, eseguire il push dell'immagine nel registro all'interno di Registro contenitori di Azure.

Eseguire `docker login` per accedere al registro di contenitori con le [credenziali del registro](../container-registry/container-registry-authentication.md).

L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/active-directory-application-objects.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione.  In alternativa, è possibile eseguire l'accesso usando il nome utente e la password del registro.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Il comando seguente crea un tag, o alias, dell'immagine, con un percorso completo del registro. Questo esempio inserisce l'immagine dello spazio dei nomi `samples` per evitare confusione nella radice del registro.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Effettuare il push dell'immagine nel registro contenitori:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Creare il pacchetto dell'immagine Docker con Yeoman
Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione dell'applicazione e l'aggiunta di un'immagine contenitore. È possibile usare Yeoman per creare un'applicazione con un singolo contenitore Docker denominato *SimpleContainerApp*.

Per creare un'applicazione contenitore di Service Fabric, aprire una finestra del terminale ed eseguire `yo azuresfcontainer`.  

Assegnare un nome all'applicazione (ad esempio, "mycontainer") e al servizio dell'applicazione (ad esempio, "myservice").

Per il nome dell'immagine, specificare l'URL dell'immagine del contenitore in un registro contenitori, ad esempio "myregistry.azurecr.io/samples/helloworldapp". 

Dato che per l'immagine è stato definito un punto di ingresso del carico di lavoro, non è necessario specificare in modo esplicito i comandi di input, che vengono eseguiti all'interno del contenitore in modo che la relativa esecuzione continui dopo l'avvio. 

Specificare "1" come numero di istanze.

![Generatore Yeoman di Service Fabric per i contenitori][sf-yeoman]

## <a name="configure-port-mapping-and-container-repository-authentication"></a>Configurare il mapping delle porte e l'autenticazione per il repository del contenitore
Il servizio in contenitore richiede un endpoint per la comunicazione.  Aggiungere ora il protocollo, la porta e il tipo a un `Endpoint` nel file ServiceManifest.xml sotto il tag 'Resources'. Per questo articolo, il servizio in contenitore è in ascolto sulla porta 4000: 

```xml

<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
 ```
 
Se si specifica `UriScheme`, l'endpoint del contenitore viene registrato automaticamente con Service Fabric Naming Service per l'individuazione. Alla fine di questo articolo viene fornito un file di esempio completo di ServiceManifest.xml. 

Configurare il mapping tra la porta del contenitore e la porta dell'host specificando i criteri `PortBinding` in `ContainerHostPolicies` nel file ApplicationManifest.xml.  Per questo articolo, il valore di `ContainerPort` è 80, dato che il contenitore espone la porta 80, come specificato nel Dockerfile, e il valore di `EndpointRef` è "myServiceTypeEndpoint", ossia l'endpoint definito nel manifesto del servizio.  Per le richieste in ingresso per il servizio sulla porta 4000 viene eseguito il mapping alla porta 80 del contenitore.  Se il contenitore deve eseguire l'autenticazione con un repository privato, aggiungere `RepositoryCredentials`.  Per questo articolo, aggiungere il nome e la password dell'account per il registro contenitori myregistry.azurecr.io. Verificare che il criterio venga aggiunto sotto il tag 'ServiceManifestImport' corrispondente al pacchetto del servizio corretto.

```xml
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0" />
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
   </ServiceManifestImport>
``` 
## <a name="configure-docker-healthcheck"></a>Configurare docker HEALTHCHECK 
A partire dalla versione 6.1, Service Fabric integra automaticamente gli eventi di [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) nel report relativo all'integrità del sistema. Se **HEALTHCHECK** è stato abilitato nel contenitore, Service Fabric fornirà informazioni sull'integrità ogni volta che lo stato dell'integrità del contenitore subisce modifiche, in base a quanto segnalato da Docker. Un report sull'integrità di tipo **OK** verrà visualizzato in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) quando il valore *health_status* è *healthy* e un report di tipo **AVVISO** verrà visualizzato quando il valore *health_status* è *unhealthy*. L'istruzione **HEALTHCHECK** che fa riferimento alla verifica effettiva eseguita per il monitoraggio dell'integrità dei contenitori deve essere presente nel file **dockerfile** usato durante la generazione dell'immagine del contenitore. 

![HealthCheckHealthy][1]

![HealthCheckUnealthyApp][2]

![HealthCheckUnhealthyDsp][3]

È possibile configurare il comportamento di **HEALTHCHECK** per ogni contenitore specificando le opzioni di **HealthConfig** come parte di **ContainerHostPolicies** in ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Per impostazione predefinita, l'opzione *IncludeDockerHealthStatusInSystemHealthReport* è impostata su **true** e l'opzione *RestartContainerOnUnhealthyDockerHealthStatus* è impostata su **false**. Se l'opzione *RestartContainerOnUnhealthyDockerHealthStatus* è impostata su **true**, un contenitore che segnala ripetutamente uno stato non integro viene riavviato, possibilmente su altri nodi.

Se si vuole disabilitare l'integrazione di **HEALTHCHECK** per l'intero cluster di Service Fabric, sarà necessario impostare [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) su **false**.

## <a name="build-and-package-the-service-fabric-application"></a>Compilare l'applicazione di Service Fabric e creare il pacchetto
I modelli Yeoman di Service Fabric includono uno script di compilazione per [Gradle](https://gradle.org/), che può essere usato per compilare l'applicazione dal terminale. Per compilare l'applicazione e creare il pacchetto, eseguire questo comando:

```bash
cd mycontainer
gradle
```

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Service Fabric.

Connettersi al cluster locale di Service Fabric.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

```bash
./install.sh
```

Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://localhost:19080/Explorer. Sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

Connettersi al contenitore in esecuzione.  Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 4000, ad esempio "http://localhost:4000". Verrà visualizzata l'intestazione "Hello World!" nel browser.

![Hello World!][hello-world]


## <a name="clean-up"></a>Eseguire la pulizia
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione dal cluster di sviluppo locale e annullare la registrazione del tipo di applicazione.

```bash
./uninstall.sh
```

Dopo aver effettuato il push dell'immagine nel registro contenitori, è possibile eliminare l'immagine locale dal computer di sviluppo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questo articolo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Aggiunta di altri servizi a un'applicazione esistente

Per aggiungere un altro servizio contenitore a un'applicazione già creata usando yeoman, seguire questa procedura:

1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurare l'intervallo di tempo prima della terminazione forzata del contenitore

È possibile configurare un intervallo di tempo di attesa del runtime prima che il contenitore venga rimosso dopo l'avvio dell'eliminazione del servizio (o di un passaggio a un altro nodo). Configurando l'intervallo di tempo, viene inviato il comando `docker stop <time in seconds>` al contenitore.   Per altri dettagli, vedere [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). L'intervallo di tempo per l'attesa viene specificato nella sezione `Hosting`. Il frammento di manifesto del cluster seguente illustra come impostare l'intervallo di attesa:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

L'intervallo di tempo predefinito è impostato su 10 secondi. Poiché questa configurazione è dinamica, un aggiornamento della sola configurazione nel cluster aggiorna il timeout. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurare il runtime per rimuovere le immagini del contenitore non usate

È possibile configurate il cluster di Service Fabric per rimuovere le immagini del contenitore non usate dal nodo. Questa configurazione consente di riacquisire spazio su disco se nel nodo sono presenti troppe immagini del contenitore.  Per abilitare questa funzionalità, aggiornare la sezione `Hosting` nel manifesto del cluster, come illustrato nel frammento seguente: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Le immagini che non devono essere eliminate possono essere specificate nel parametro `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Configurare il tempo di download delle immagini del contenitore

Per impostazione predefinita, il runtime di Service Fabric alloca 20 minuti per il download e l'estrazione delle immagini del contenitore, perché tale limite è appropriato per la maggior parte delle immagini del contenitore. Per immagini di grandi dimensioni o in caso di connessione di rete lenta potrebbe essere necessario aumentare il tempo di attesa prima dell'interruzione del download e dell'estrazione dell'immagine. Questo valore può essere configurato tramite l'attributo **ContainerImageDownloadTimeout** nella sezione **Hosting** del manifesto del cluster, come mostrato nel frammento di codice seguente:

```json
{
"name": "Hosting",
        "parameters": [
          {
              "name": " ContainerImageDownloadTimeout ",
              "value": "1200"
          }
]
}
```


## <a name="set-container-retention-policy"></a>Configurare i criteri di conservazione dei contenitori

Per semplificare la diagnosi degli errori di avvio dei contenitori, Service Fabric (versione 6.1 o versioni successive) supporta la conservazione di contenitori terminati o il cui avvio non è riuscito. Questo criterio può essere configurato nel file **ApplicationManifest.xml**, come mostrato nel frammento di codice seguente:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

L'impostazione **ContainersRetentionCount** specifica il numero di contenitori da conservare in caso di errore. Se viene specificato un valore negativo, verranno conservati tutti i contenitori con errori. Quando l'attributo **ContainersRetentionCount** non viene specificato, non verrà conservato alcun contenitore. L'attributo **ContainersRetentionCount** supporta anche i parametri dell'applicazione, quindi gli utenti possono specificare valori diversi per cluster di test e di produzione. È consigliabile usare vincoli di posizionamento per specificare come destinazione un nodo specifico per il servizio contenitore quando si usano queste funzionalità, in modo da evitare che il servizio contenitore passi ad altri nodi. Eventuali contenitori conservati tramite questa funzionalità devono essere rimossi manualmente.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Vedere l'esercitazione [Distribuire un'applicazione .NET in un contenitore](service-fabric-host-app-in-a-container.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) in GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
