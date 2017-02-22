---
title: Service Fabric e distribuzione di contenitori in Linux | Documentazione Microsoft
description: "Service Fabric e uso di contenitori Docker per la distribuzione di applicazioni di microservizi. Questo articolo illustra le funzionalità offerte da Service Fabric per i contenitori e la modalità di distribuzione di un immagine contenitore Docker in un cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/16/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 93e0493e6a62a70a10b8315142765a3c3892acd1
ms.openlocfilehash: 056968900d8078dfe53948a2da1daa26cb04a713


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Distribuire un contenitore Docker in Service Fabric
> [!div class="op_single_selector"]
> * [Distribuire un contenitore Windows](service-fabric-deploy-container.md)
> * [Distribuire un contenitore Docker](service-fabric-deploy-container-linux.md)
>
>

Questo articolo descrive la creazione di servizi in contenitori Docker su Linux.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, Questi servizi sono denominati servizi in contenitori.

Le funzionalità includono:

* Distribuzione e attivazione di immagini contenitore
* Governance delle risorse
* Autenticazione nel repository
* Mapping tra porta del contenitore e porta dell'host
* Individuazione e comunicazione da contenitore a contenitore
* Possibilità di configurare e impostare variabili di ambiente

## <a name="packaging-a-docker-container-with-yeoman"></a>Creazione del pacchetto di un contenitore Docker con yeoman
Quando si crea il pacchetto di un contenitore in Linux, si può scegliere di usare un modello yeoman oppure di [creare manualmente il pacchetto dell'applicazione](#manually).

Un'applicazione di Service Fabric può contenere uno o più contenitori, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Service Fabric SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che semplifica la creazione dell'applicazione e l'aggiunta di un'immagine contenitore. È possibile usare Yeoman per creare una nuova applicazione con un singolo contenitore Docker denominato *SimpleContainerApp*. È possibile aggiungere altri servizi in un secondo momento modificando i file manifesto generati.

## <a name="create-the-application"></a>Creare l'applicazione
1. In un terminale digitare **yo azuresfguest**.
2. Per il framework scegliere **Container** (Contenitore).
3. Assegnare un nome dell'applicazione, ad esempio SimpleContainerApp
4. Fornire l'URL per l'immagine contenitore da un repository DockerHub. Il parametro immagine sarà nel formato [repository]/[nome immagine]

![Generatore Yeoman di Service Fabric per i contenitori][sf-yeoman]

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Azure.

1. Connettersi al cluster locale di Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```
2. Usare lo script di installazione messo a disposizione nel modello per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```
3. Aprire un browser e passare a Service Fabric Explorer all'indirizzo http://localhost:19080/Explorer. Sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa Vagrant in Mac OS X.
4. Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.
5. Usare lo script di disinstallazione fornito nel modello per eliminare l'istanza dell'applicazione e annullare la registrazione del tipo di applicazione.

    ```bash
    ./uninstall.sh
    ```
Per un'applicazione di esempio [vedere gli esempi di codice di contenitore Service Fabric su GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="adding-more-services-to-an-existing-application"></a>Aggiunta di altri servizi a un'applicazione esistente

Per aggiungere un altro servizio contenitore a un'applicazione già creata mediante `yo`, seguire questa procedura: 
1. Modificare la directory impostandola sulla radice dell'applicazione esistente.  Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfguest:AddService`

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Creare manualmente il pacchetto e distribuire un'immagine del contenitore
Il processo per creare manualmente il pacchetto di un servizio in contenitore prevede i passaggi seguenti:

1. Pubblicare i contenitori nel repository.
2. Creare la struttura di directory del pacchetto.
3. Modificare il file manifesto del servizio.
4. Modificare il file manifesto dell’applicazione.

## <a name="deploy-and-activate-a-container-image"></a>Distribuire e attivare un'immagine contenitore
Nel [modello applicativo](service-fabric-application-model.md)di Service Fabric, un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi. Per distribuire e attivare un contenitore, inserire il nome dell'immagine contenitore in un elemento `ContainerHost` nel manifesto del servizio.

Nel manifesto del servizio aggiungere un `ContainerHost` per il punto di ingresso. Impostare quindi il nome dell'immagine e del repository del contenitore in `ImageName`. Il manifesto parziale seguente mostra un esempio di come distribuire il contenitore denominato `myimage:v1` da un repository denominato `myrepo`:

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

È possibile fornire comandi di input specificando l'elemento `Commands` facoltativo con un set delimitato da virgole di comandi da eseguire all'interno del contenitore.

## <a name="understand-resource-governance"></a>Informazioni sulla governance delle risorse
La governance delle risorse è una funzionalità del contenitore che limita le risorse che possono essere usate dal contenitore nell'host. L'impostazione `ResourceGovernancePolicy`, specificata nel manifesto dell'applicazione, viene usata per dichiarare limiti di risorse per il pacchetto di codice di un servizio. È possibile impostare limiti per le risorse seguenti:

* Memoria
* MemorySwap
* CpuShares (peso relativo CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo BlockIO)

> [!NOTE]
> In una versione futura sarà incluso il supporto della definizione di specifici limiti di I/O di blocco, come operazioni di I/O al secondo, BPS in lettura/scrittura e altri.
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Autenticare un repository
Per scaricare un contenitore potrebbe essere necessario specificare credenziali di accesso per il relativo repository. Le credenziali di accesso, specificate nel manifesto dell'applicazione, vengono usate per specificare le informazioni di accesso o la chiave SSH per scaricare l'immagine contenitore dal repository delle immagini. L'esempio seguente illustra un account denominato *TestUser* con password non crittografata (scenario *non* consigliato):

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

È consigliabile crittografare la password con un certificato distribuito nel computer.

L'esempio seguente illustra un account denominato *TestUser* con password crittografata con un certificato denominato *MyCert*. È possibile usare il comando `Invoke-ServiceFabricEncryptText` di PowerShell per creare il testo crittografato segreto per la password. Per altre informazioni, vedere [Gestione dei segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

La chiave privata del certificato usata per decrittografare la password deve essere distribuita nel computer locale con un metodo fuori banda. (In Azure, questo metodo è Azure Resource Manager.) Quando Service Fabric distribuisce il pacchetto del servizio nel computer, potrà quindi decrittografare il segreto. Usando il segreto insieme al nome dell'account, potrà quindi eseguire l'autenticazione nel repository del contenitore.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>Configurare il mapping tra porta del contenitore e porta dell'host
È possibile configurare una porta dell'host per la comunicazione con il contenitore specificando `PortBinding` nel manifesto dell'applicazione. Il binding di porta esegue il mapping tra la porta su cui il servizio è in ascolto all'interno del contenitore e una porta nell'host.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Configurare individuazione e comunicazione da contenitore a contenitore
Usando il criterio `PortBinding`, è possibile eseguire il mapping tra la porta di un contenitore e un `Endpoint` nel manifesto del servizio, come illustrato nell'esempio seguente. L'endpoint `Endpoint1` può specificare una porta fissa, ad esempio, la porta 80, oppure non specificarne alcuna. Nel secondo caso verrà scelta automaticamente una porta casuale nell'intervallo di porte dell'applicazione del cluster.

Se si specifica un endpoint, usando il tag `Endpoint` nel manifesto del servizio di un contenitore guest, Service Fabric può pubblicare automaticamente questo endpoint per Naming Service. Altri servizi in esecuzione nel cluster possono così individuare questo contenitore usando query REST per la risoluzione.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Eseguendo la registrazione in Naming Service, è possibile includere facilmente la comunicazione da contenitore a contenitore nel codice all'interno del contenitore usando il [proxy inverso](service-fabric-reverseproxy.md). La comunicazione avviene specificando la porta di ascolto HTTP del proxy inverso e il nome dei servizi con cui si vuole comunicare come variabili di ambiente. Per altre informazioni, vedere la sezione seguente. 

## <a name="configure-and-set-environment-variables"></a>Configurare e impostare variabili di ambiente
È possibile specificare variabili di ambiente per ogni pacchetto di codice nel manifesto del servizio, per servizi distribuiti sia in contenitori che come processi/eseguibili guest. È possibile eseguire specificamente l'override dei valori di queste variabili di ambiente nel manifesto dell'applicazione oppure specificarli durante la distribuzione come parametri dell'applicazione.

Il frammento XML del manifesto del servizio seguente offre un esempio di come specificare le variabili di ambiente per un pacchetto di codice:

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

È possibile eseguire l'override di queste variabili di ambiente a livello di manifesto dell'applicazione:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

Nell'esempio precedente è stato specificato un valore esplicito per la variabile di ambiente `HttpGateway` (19000), mentre il valore per il parametro `BackendServiceName` viene impostato tramite il parametro dell'applicazione `[BackendSvc]`. Queste impostazioni consentono di specificare il valore per `BackendServiceName` quando si distribuisce l'applicazione anziché avere un valore fisso nel manifesto.

## <a name="complete-examples-for-application-and-service-manifest"></a>Esempi completi per il manifesto dell'applicazione e del servizio

Di seguito è illustrato un manifesto dell'applicazione di esempio:

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

Di seguito è riportato un manifesto del servizio di esempio (specificato nel manifesto dell'applicazione precedente):

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato distribuito un servizio in contenitori, vedere [Ciclo di vita dell'applicazione Service Fabric](service-fabric-application-lifecycle.md) per informazioni su come gestire il ciclo di vita del servizio.

* [Panoramica di Service Fabric e contenitori](service-fabric-containers-overview.md)
* [Uso dell'interfaccia della riga di comando di Azure per interagire con un cluster di Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Feb17_HO2-->


