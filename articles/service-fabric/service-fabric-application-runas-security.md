<properties
   pageTitle="Informazioni sui criteri di sicurezza RunAs dell'applicazione dell'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica dell'esecuzione di un'applicazione dell'infrastruttura di servizi con account di sicurezza di sistema e locali, incluso il punto SetupEntry in cui un'applicazione deve eseguire un'azione con privilegi prima dell'avvio"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# RunAs: Esecuzione di un'applicazione dell'infrastruttura di servizi con varie autorizzazioni di sicurezza
L'infrastruttura di servizi consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi, noti come "RunAs". Consente inoltre di proteggere le risorse usate dalle applicazioni con l'account utente, ad esempio file, directory e certificati.

Per impostazione predefinita, le applicazioni dell'infrastruttura di servizi vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. L'infrastruttura di servizi consente inoltre di eseguire le applicazioni con un account utente locale, specificato nel manifesto dell'applicazione. I tipi di account supportati per RunAs sono **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

> [AZURE.NOTE]Gli account di dominio sono supportati nelle distribuzioni di Windows Server in cui è disponibile Active Directory.

I gruppi di utenti possono essere definiti e creati dove è possibile aggiungere uno o più utenti al gruppo in modo da essere gestiti insieme. Questo aspetto è particolarmente utile se sono presenti più utenti per punti di ingresso del servizio differenti che devono disporre di determinati privilegi comuni disponibili a livello di gruppo.

## Impostazione dei criteri RunAs per SetupEntryPoint

Come descritto nel [modello applicativo](service-fabric-application-model.md), l'elemento **SetupEntryPoint** è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali dell'infrastruttura di servizi (in genere l'account *di rete*) prima di qualsiasi altro punto di ingresso. Poiché l'eseguibile specificato da **EntryPoint** è in genere l'host servizio a esecuzione prolungata, disporre di un punto di ingresso di configurazione separato evita di dover eseguire il file exe dell'host servizio con privilegi elevati per periodi di tempo estesi. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con **SetupEntryPoint**) se termina o si arresta in modo anomalo.

Di seguito è illustrato un semplice manifesto del servizio di esempio in cui sono presenti un elemento SetupEntryPoint e il punto di ingresso principale per il servizio.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### Configurazione dei criteri RunAs

Dopo aver configurato l'elemento SetupEntryPoint per il servizio, è possibile modificare le autorizzazioni di sicurezza in base alle quali viene eseguito il servizio nel manifesto dell'applicazione. L'esempio seguente illustra come configurare il servizio per l'esecuzione con i privilegi dell'account amministratore.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Creare innanzitutto una sezione **Principals** con un nome utente, ad esempio SetupAdminUser. Ciò indica che l'utente è un membro del gruppo di sistema Administrators.

Successivamente, configurare nella sezione **ServiceManifestImport** un criterio per applicare tale entità all'elemento **SetupEntryPoint**. Ciò indica all'infrastruttura di servizi che, quando viene eseguito, il file MySetup.bat deve disporre di criteri RunAs con privilegi di amministratore. Poiché *non* sono stati applicati criteri al punto di ingresso principale, il codice in MyServiceHost.exe verrà eseguito con l'account NetworkService di sistema, ossia l'account predefinito, per cui tutti i punti di ingresso del servizio dispongono di criteri RunAs.

Aggiungere a questo punto il file MySetup.bat al progetto di Visual Studio per testare i privilegi di amministratore. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio e aggiungere un nuovo file denominato MySetup.bat. Successivamente è necessario assicurarsi che questo file sia incluso nel pacchetto del servizio, poiché non è incluso per impostazione predefinita. Per assicurarsi che il file MySetup.bat sia incluso nel pacchetto, selezionare il file, fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida, scegliere Proprietà e nella finestra di dialogo Proprietà verificare che l'opzione **Copia nella directory di output** sia impostata su **Copia se più recente**, come illustrato nella schermata seguente.

![CopyToOutput di Visual Studio per il file batch SetupEntryPoint][image1]

Aprire il file MySetup.bat e aggiungere i comandi seguenti.

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Control\\Session Manager\\Environment" /v TestVariable /f
~~~

Successivamente compilare e distribuire la soluzione a un cluster di sviluppo locale. Una volta avviato il servizio, come visualizzato in Esplora infrastruttura di servizi, si noterà che il file MySetup.bat è stato completato in due modi. Aprire un prompt dei comandi di PowerShell e digitare
~~~
 [Environment]::GetEnvironmentVariable("TestVariable","Machine")
~~~
Like this
~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue
~~~

Prendere nota del nome del nodo in cui il servizio è stato distribuito e avviato in Service Fabric Explorer, ad esempio Node 1, e passare alla cartella di lavoro dell'istanza dell'applicazione per trovare il file out.txt con il valore di **TestVariable**. Se ad esempio il servizio è stato distribuito in Node 2, è possibile passare a questo percorso per MyApplicationType.

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  Avvio dei comandi di PowerShell da SetupEntryPoint
Per eseguire PowerShell dal punto **SetupEntryPoint**, è possibile eseguire PowerShell.exe in un file batch che punta a un file di PowerShell. Aggiungere innanzitutto un file di PowerShell al progetto del servizio, ad esempio MySetup.ps1. Ricordarsi di impostare la proprietà *Copia se più recente* in modo che il file venga incluso anche nel pacchetto del servizio. L'esempio seguente illustra un file batch di esempio per avviare un file di PowerShell denominato MySetup.ps1, che imposta una variabile di ambiente di sistema denominata *TestVariable*.

MySetup. bat per avviare il file di PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Nel file di PowerShell aggiungere il comando seguente per impostare una variabile di ambiente di sistema
~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

## Applicazione di criteri RunAs ai servizi
Nei passaggi precedenti è stato illustrato come applicare i criteri RunAs a SetupEntryPoint. A questo punto, è possibile analizzare la modalità per creare entità diverse che possono essere applicate come criteri del servizio.

### Creare gruppi di utenti locali
I gruppi di utenti possono essere definiti e creati dove è possibile aggiungere uno o più utenti al gruppo. Questo aspetto è particolarmente utile se sono presenti più utenti per punti di ingresso del servizio differenti che devono disporre di determinati privilegi comuni disponibili a livello di gruppo. L'esempio seguente illustra un gruppo locale denominato **LocalAdminGroup** con privilegi di amministratore. Due utenti, Customer1 e Customer2, diventano membri di questo gruppo locale.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### Creare utenti locali
È possibile creare un utente locale che può essere usato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account LocalUser nella sezione Principals del manifesto dell'applicazione, l'infrastruttura di servizi crea account utente locali nei computer in cui viene distribuita l'applicazione. Per impostazione predefinita, per questi account non viene specificato lo stesso nome dell'applicazione manifesto (ad esempio "Customer3" nell'esempio seguente), ma ne viene generato uno dinamicamente con password casuali.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Assegnazione di criteri ai pacchetti di codice del servizio
La sezione **RunAsPolicy** per **ServiceManifestImport** specifica l'account della sezione Principals da usare per eseguire un pacchetto di codice e associa i pacchetti di codice del manifesto del servizio agli account utente nella sezione Principals. È possibile specificare questa opzione per i punti di ingresso principale o di configurazione o specificare Tutti per applicarla a entrambi. L'esempio seguente illustra l'applicazione di diversi criteri.

~~~
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se **EntryPointType** non è specificato, il valore predefinito è impostato su EntryPointType="Main". Specificare l'elemento **SetupEntryPoint** è particolarmente utile quando si desidera eseguire determinate operazioni di configurazione con privilegi elevati con un account di sistema, mentre il codice del servizio effettivo può essere eseguito con un account con privilegi inferiori.

### Applicazione di un criterio predefinito a tutti i pacchetti di codice del servizio
La sezione **DefaultRunAsPolicy** consente di specificare un account utente predefinito per tutti i pacchetti di codice per i quali non sono definiti **RunAsPolicy** specifici. Se la maggior parte dei pacchetti di codice specificati nei manifesti del servizio usati da un'applicazione deve essere eseguita con lo stesso utente RunAs, l'applicazione può definire solo un criterio RunAs predefinito con tale account utente anziché specificare **RunAsPolicy** per ogni pacchetto di codice. L'esempio seguente specifica, ad esempio, che se per un pacchetto di codice **RunAsPolicy** non è specificato, il pacchetto di codice deve essere eseguito con l'account MyDefaultAccount specificato nella sezione Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Assegnazione di SecurityAccessPolicy per gli endpoint HTTP e HTTPS
Se si applica un criterio RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy** per garantire che le porte allocate a tali endpoint siano correttamente inserite nell'elenco di controllo di accesso per l'account utente RunAs con cui viene eseguito il servizio. In caso contrario, http.sys non avrà accesso al servizio e le chiamate del client avranno esito negativo. L'esempio seguente applica l'account Customer3 all'endpoint denominato *ServiceEndpointName* e assegna ad esso diritti di accesso completi.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Per gli endpoint HTTPS è necessario inoltre indicare il nome del certificato da restituire al client con **EndpointBindingPolicy**, insieme al certificato definito in una sezione di certificati nel manifesto dell'applicazione.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## Esempio completo del manifesto dell'applicazione
Il manifesto dell'applicazione seguente illustra molte delle impostazioni descritte in precedenza.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_1125_2015-->