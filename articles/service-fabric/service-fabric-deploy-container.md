<properties
   pageTitle="Service Fabric e distribuzione di contenitori | Microsoft Azure"
   description="Service Fabric e l'uso di contenitori per la distribuzione di applicazioni di microservizi. Questo articolo illustra le funzionalità offerte da Service Fabric per i contenitori e la distribuzione di un immagine contenitore in un cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>  

# Anteprima: Distribuire un contenitore in Service Fabric

>[AZURE.NOTE] Questa funzionalità è in anteprima per Linux e non è attualmente disponibile in Windows Server. Sarà disponibile in anteprima per Windows Server nella prossima versione di Service Fabric dopo la disponibilità generale di Windows Server 2016 e sarà quindi supportata nella versione successiva.

Service Fabric offre diverse funzionalità relative ai contenitori che consentono di creare applicazioni costituite da microservizi inseriti in contenitori, denominati servizi in contenitori. Le funzionalità includono:

- Distribuzione e attivazione di immagini contenitore
- Governance delle risorse
- Autenticazione nel repository
- Mapping tra porta del contenitore e porta dell'host
- Individuazione e comunicazione da contenitore a contenitore
- Possibilità di configurare e impostare variabili di ambiente

Verranno ora esaminate le singole funzionalità coinvolte nella creazione del pacchetto di un servizio in contenitore da includere nell'applicazione.

## Creazione del pacchetto di un contenitore

Quando si crea il pacchetto di un contenitore, si può scegliere di usare un modello di progetto di Visual Studio oppure di [creare manualmente il pacchetto dell'applicazione](#manually). Se si usa Visual Studio, la struttura del pacchetto dell'applicazione e i file manifesto vengono creati automaticamente dalla Creazione guidata nuovo progetto.

## Uso di Visual Studio per creare il pacchetto di un eseguibile esistente

>[AZURE.NOTE] In una versione futura dell'SDK degli strumenti di Visual Studio sarà possibile aggiungere un contenitore a un'applicazione analogamente a come può ora essere aggiunto un eseguibile guest. Vedere l'argomento [Distribuire un eseguibile guest in Service Fabric](service-fabric-deploy-existing-app.md). Attualmente è necessario creare manualmente il pacchetto come descritto di seguito.

<a id="manually"></a>
## Creazione manuale del pacchetto e distribuzione del contenitore
Il processo per creare manualmente il pacchetto di un servizio in contenitore prevede i passaggi seguenti:

1. Pubblicare i contenitori nel repository.
2. Creare la struttura di directory del pacchetto.
3. Modificare il file manifesto del servizio.
4. Modificare il file manifesto dell’applicazione.

## Distribuzione e attivazione di immagini contenitore
Nel [modello applicativo](service-fabric-application-model.md) di Service Fabric, un contenitore rappresenta un host applicazione in cui sono inserite più repliche dei servizi. Per distribuire e attivare un contenitore, inserire il nome dell'immagine contenitore in un elemento `ContainerHost` nel manifesto del servizio.

Nel manifesto del servizio aggiungere un elemento `ContainerHost` per il punto di ingresso e impostare il nome dell'immagine e del repository del contenitore in `ImageName`. Il manifesto parziale seguente mostra l'esempio della distribuzione del contenitore denominato *myimage:v1* da un repository denominato *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

È possibile fornire comandi di input per l'immagine contenitore specificando l'elemento `Commands` facoltativo con un set delimitato da virgole di comandi da eseguire all'interno del contenitore.

## Governance delle risorse
La governance delle risorse è una funzionalità del contenitore e limita le risorse che possono essere usate dal contenitore nell'host. L'impostazione `ResourceGovernancePolicy`, specificata nel manifesto dell'applicazione, offre la possibilità di dichiarare limiti di risorse per il pacchetto di codice di un servizio. È possibile impostare limiti di risorse per:

- Memoria
- MemorySwap
- CpuShares (peso relativo CPU)
- MemoryReservationInMB
- BlkioWeight (peso relativo BlockIO)

>[AZURE.NOTE] In una versione futura sarà supportata la definizione di specifici limiti di I/O di blocco, come operazioni di I/O al secondo, BPS in lettura/scrittura e altri.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## Autenticazione nel repository
Per scaricare un contenitore potrebbe essere necessario specificare credenziali di accesso per il relativo repository. Le credenziali di accesso, indicate nel manifesto dell'*applicazione*, vengono usate per specificare le informazioni di accesso o la chiave SSH per scaricare l'immagine contenitore dal repository immagini. L'esempio seguente illustra un account denominato *TestUser* con password non crittografata. Questa impostazione **non** è consigliata.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

È possibile e consigliabile crittografare la password con un certificato distribuito nel computer.

L'esempio seguente illustra un account denominato *TestUser* con password crittografata con un certificato denominato *MyCert*. È possibile usare il comando `Invoke-ServiceFabricEncryptText` di Powershell per creare il testo crittografato segreto per la password. Per informazioni dettagliate in proposito, vedere l'articolo sulla [gestione dei segreti in Service Fabric](service-fabric-application-secret-management.md). La chiave privata del certificato per decrittografare la password deve essere distribuita nel computer locale con un metodo fuori banda (in Azure, tramite Resource Manager). Quando distribuisce il pacchetto servizio nel computer, Service Fabric può quindi decrittografare il segreto e, con il nome dell'account, eseguire l'autenticazione nel repository del contenitore usando queste credenziali.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## Mapping tra porta del contenitore e porta dell'host
È possibile configurare una porta dell'host per la comunicazione con il contenitore specificando `PortBinding` nel manifesto dell'applicazione. Il binding di porta esegue il mapping tra la porta su cui il servizio è in ascolto all'interno del contenitore e una porta nell'host.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## Individuazione e comunicazione da contenitore a contenitore
Usando il criterio `PortBinding`, è possibile eseguire il mapping tra la porta di un contenitore e un `Endpoint` nel manifesto del servizio come illustrato nell'esempio seguente. L'endpoint `Endpoint1` può specificare una porta fissa, ad esempio la porta 80, oppure non specificarne alcuna. Nel secondo caso verrà scelta automaticamente una porta casuale nell'intervallo di porte dell'applicazione del cluster.

Per i contenitori guest, la specifica di un elemento `Endpoint` come questo nel manifesto del servizio consente a Service Fabric di pubblicare automaticamente l'endpoint nel servizio Naming in modo che altri servizi in esecuzione nel cluster possano individuare il contenitore usando query REST del servizio di risoluzione.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Eseguendo la registrazione nel servizio Naming, è possibile includere facilmente la comunicazione da contenitore a contenitore nel codice all'interno del contenitore con il [proxy inverso](service-fabric-reverseproxy.md). È sufficiente specificare la porta di ascolto HTTP del proxy inverso e il nome dei servizi con cui si vuole comunicare impostandoli come variabili di ambiente. Per informazioni su come eseguire questa operazione, vedere la sezione successiva.

## Configurare e impostare variabili di ambiente
È possibile specificare variabili di ambiente per ogni pacchetto di codice nel manifesto del servizio, per servizi distribuiti sia in contenitori che come processi/eseguibili guest. È possibile eseguire specificamente l'override dei valori di queste variabili di ambiente nel manifesto dell'applicazione oppure specificarli durante la distribuzione come parametri dell'applicazione.

Il frammento XML del manifesto del servizio seguente offre un esempio di come specificare le variabili di ambiente per un pacchetto di codice.

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

È possibile eseguire l'override di queste variabili di ambiente a livello di manifesto dell'applicazione:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

Nell'esempio precedente è stato specificato un valore esplicito per la variabile di ambiente `HttpGateway` (19000), mentre il valore per il parametro `BackendServiceName` viene impostato tramite il parametro dell'applicazione `[BackendSvc]`. In questo modo è possibile specificare il valore per `BackendServiceName` al momento della distribuzione dell'applicazione anziché avere un valore fisso nel manifesto.

## Esempi completi per il manifesto dell'applicazione e del servizio
Di seguito è riportato un manifesto dell'applicazione di esempio che illustra le funzionalità dei contenitori.


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


Di seguito è riportato un manifesto del servizio di esempio (specificato nel manifesto dell'applicazione precedente) che illustra le funzionalità dei contenitori.

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>

<!---HONumber=AcomDC_0928_2016-->