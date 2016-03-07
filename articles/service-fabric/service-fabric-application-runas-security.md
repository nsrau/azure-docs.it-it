<properties
   pageTitle="Informazioni sui criteri di sicurezza RunAs dell'applicazione dell'infrastruttura di servizi | Microsoft Azure"
   description="Panoramica dell'esecuzione di un'applicazione di Service Fabric con account di sicurezza di sistema e locali, incluso il punto SetupEntry in cui un'applicazione deve eseguire un'azione con privilegi prima dell'avvio"
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
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# RunAs: Eseguire un'applicazione di Service Fabric con varie autorizzazioni di sicurezza
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi, noti come **RunAs**. Service Fabric permette anche di proteggere le risorse usate dalle applicazioni con l'account utente, ad esempio file, directory e certificati.

Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Service Fabric consente anche di eseguire le applicazioni con un account utente locale o un account di sistema locale, specificato nel manifesto dell'applicazione. I tipi di account supportati dal sistema locale per RunAs sono **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

> [AZURE.NOTE] Gli account di dominio sono supportati nelle distribuzioni di Windows Server in cui è disponibile Azure Active Directory.

È possibile definire e creare gruppi di utenti per aggiungere uno o più utenti a ogni gruppo e gestirli insieme. Questo aspetto è particolarmente utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo.

## Impostare i criteri RunAs per SetupEntryPoint

Come descritto nel [modello applicativo](service-fabric-application-model.md), l'elemento **SetupEntryPoint** è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali di Service Fabric (in genere, l'account *NetworkService*) prima di qualsiasi altro punto di ingresso. Dal momento che l'eseguibile specificato da **EntryPoint** è in genere l'host servizio a esecuzione prolungata, avere un punto di ingresso di configurazione separato evita di dover eseguire l'eseguibile dell'host servizio con privilegi elevati per periodi di tempo estesi. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Il processo risultante viene monitorato e riavviato (iniziando di nuovo con **SetupEntryPoint**) se termina o si arresta in modo anomalo.

Di seguito è illustrato un semplice esempio di manifesto del servizio in cui sono presenti un elemento SetupEntryPoint e il punto di ingresso principale per il servizio.

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
        <WorkingFolder>CodePackage</WorkingFolder>
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

### Configurare i criteri RunAs mediante un account locale

Dopo aver configurato un punto di ingresso di configurazione per il servizio, è possibile modificare le autorizzazioni di sicurezza in base alle quali viene eseguito nel manifesto dell'applicazione. L'esempio seguente illustra come configurare il servizio per l'esecuzione con i privilegi dell'account amministratore utenti.

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

Creare prima di tutto una sezione **Principals** con un nome utente, ad esempio SetupAdminUser. Ciò indica che l'utente è un membro del gruppo di sistema Administrators.

Successivamente, configurare nella sezione **ServiceManifestImport** un criterio per applicare tale entità a **SetupEntryPoint**. Questo indica a Service Fabric che, quando viene eseguito, il file **MySetup.bat** deve avere criteri RunAs con privilegi di amministratore. Dato che *non* sono stati applicati criteri al punto di ingresso principale, il codice in **MyServiceHost.exe** verrà eseguito con l'account **NetworkService** di sistema, ossia l'account predefinito con cui vengono eseguiti tutti i punti di ingresso del servizio.

A questo punto, aggiungere il file MySetup.bat al progetto di Visual Studio per testare i privilegi di amministratore. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio e aggiungere un nuovo file denominato MySetup.bat.

Verificare quindi che il file MySetup.bat sia incluso nel pacchetto del servizio. Per impostazione predefinita, non è incluso. Selezionare il file, fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida e scegliere **Proprietà**. Nella finestra di dialogo delle proprietà, assicurarsi che **Copia nella directory di output** sia impostato su **Copia se più recente**, come illustrato nella schermata seguente.

![CopyToOutput di Visual Studio per il file batch SetupEntryPoint][image1]

Aprire il file MySetup.bat e aggiungere i comandi seguenti:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Successivamente, compilare e distribuire la soluzione a un cluster di sviluppo locale. Dopo aver avviato il servizio, come visualizzato in Service Fabric Explorer, sarà possibile verificare la corretta esecuzione del file MySetup.bat in due modi. Avviare un prompt dei comandi di PowerShell e digitare:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Annotare il nome del nodo in cui il servizio è stato distribuito e avviato in Service Fabric Explorer, ad esempio, Node 1. Passare quindi alla cartella di lavoro dell'istanza dell'applicazione per trovare il file out.txt che mostra il valore di **TestVariable**. Se, ad esempio, il servizio è stato distribuito in Node 2, è possibile passare a questo percorso per **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  Configurare i criteri RunAs mediante gli account del sistema locale
Spesso è preferibile eseguire lo script di avvio usando un account di sistema locale anziché un account amministratore, come illustrato in precedenza. L'esecuzione dei criteri RunAs come amministratori in genere non ha esito positivo in quanto per i computer è abilitato il controllo di accesso dell'utente per impostazione predefinita. In questi casi, **si consiglia di eseguire SetupEntryPoint come LocalSystem anziché un utente locale aggiunto al gruppo amministratori**. L'esempio seguente illustra l'impostazione di SetupEntryPoint per l'esecuzione come LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  Avviare i comandi di PowerShell da SetupEntryPoint
Per eseguire PowerShell dal punto **SetupEntryPoint**, è possibile eseguire **PowerShell.exe** in un file batch che punta a un file di PowerShell. Aggiungere innanzitutto un file di PowerShell al progetto del servizio, ad esempio **MySetup.ps1**. Ricordarsi di impostare la proprietà *Copia se più recente* in modo che il file venga incluso anche nel Service Pack. L'esempio seguente illustra un file batch di esempio per avviare un file di PowerShell denominato MySetup.ps1, che imposta una variabile di ambiente di sistema denominata **TestVariable**.


MySetup. bat per avviare il file di PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Nel file di PowerShell aggiungere quanto segue per impostare una variabile di ambiente di sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Nota:** per impostazione predefinita, quando viene eseguito il file batch cerca i file nella cartella dell'applicazione denominata **work**. In questo caso, quando MySetup.bat viene eseguito si desidera che trovi MySetup.ps1 nella stessa cartella, ovvero la cartella **code package** dell'applicazione. Per modificare questa cartella, impostare la cartella di lavoro come illustrato di seguito.
    
~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint> 
~~~

## Uso dei criteri di reindirizzamento della console per il debug locale dei punti di ingresso
A volte può essere utile visualizzare l'output della console dall'esecuzione di uno script a scopo di debug. A questo scopo, è possibile impostare criteri di reindirizzamento della console che scrivono l'output in un file. L'output del file viene scritto nella cartella dell'applicazione denominata **log** sul nodo in cui l'applicazione viene distribuita ed eseguita (vedere la posizione in cui si trova nell'esempio precedente).

**Nota: mai** usare i criteri di reindirizzamento della console in un'applicazione distribuita nell'ambiente di produzione, poiché ciò può influire sul failover dell'applicazione. Usare questa opzione **SOLO** a scopo di sviluppo e debug locale.

L'esempio seguente illustra come impostare il reindirizzamento della console con un valore FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint> 
~~~

Se si modifica il file MySetup.ps1 scrivendo un comando **Echo**, questo verrà scritto nel file di output a scopo di debug.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to" 
~~~

**Dopo aver eseguito il debug dello script, rimuovere immediatamente i criteri di reindirizzamento della console**

## Applicare i criteri RunAs ai servizi
Nei passaggi precedenti è stato illustrato come applicare i criteri RunAs a SetupEntryPoint. A questo punto è possibile vedere come creare entità diverse che possono essere applicate come criteri del servizio.

### Creare gruppi di utenti locali
È possibile definire e creare gruppi di utenti che permettono di aggiungere uno o più utenti a un gruppo. Questo aspetto è particolarmente utile se sono presenti più utenti per punti di ingresso del servizio differenti che devono disporre di determinati privilegi comuni disponibili a livello di gruppo. L'esempio seguente illustra un gruppo locale denominato **LocalAdminGroup** con privilegi di amministratore. Due utenti, Customer1 e Customer2, diventano membri di questo gruppo locale.

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
È possibile creare un utente locale che può essere usato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account **LocalUser** nella sezione Principals del manifesto dell'applicazione, Service Fabric crea account utente locali nei computer in cui viene distribuita l'applicazione. Per impostazione predefinita, questi account non hanno gli stessi nomi di quelli specificati nel manifesto dell'applicazione (Customer3 nell'esempio riportato di seguito). Vengono invece generati in modo dinamico e hanno password casuali.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## Assegnare criteri ai pacchetti di codice del servizio
La sezione **RunAsPolicy** di un oggetto **ServiceManifestImport** specifica l'account nella sezione Principals che deve essere usato per eseguire un pacchetto di codice. Associa anche i pacchetti di codice nel manifesto del servizio agli account utente nella sezione Principals. È possibile specificare questa opzione per i punti di ingresso principale o di configurazione oppure specificare Tutti per applicarla a entrambi. L'esempio seguente illustra l'applicazione di diversi criteri:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se **EntryPointType** non è specificato, il valore predefinito è impostato su EntryPointType="Main". Specificare l'oggetto **SetupEntryPoint** risulta particolarmente utile per eseguire determinate operazioni di configurazione con privilegi elevati con un account di sistema, mentre il codice del servizio effettivo può essere eseguito con un account con privilegi inferiori.

### Applicare un criterio predefinito a tutti i pacchetti di codice del servizio
La sezione **DefaultRunAsPolicy** consente di specificare un account utente predefinito per tutti i pacchetti di codice per i quali non è definito un oggetto **RunAsPolicy** specifico. Se la maggior parte dei pacchetti di codice specificati nei manifesti del servizio usati da un'applicazione deve essere eseguita con lo stesso utente RunAs, l'applicazione può definire solo criteri RunAs predefiniti con tale account utente anziché specificare **RunAsPolicy** per ogni pacchetto di codice. L'esempio seguente mostra che se l'oggetto **RunAsPolicy** non è specificato per un pacchetto di codice, questo deve essere eseguito con l'account **MyDefaultAccount** specificato nella sezione Principals.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## Assegnare SecurityAccessPolicy per gli endpoint HTTP e HTTPS
Se si applicano criteri RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy** per garantire che le porte allocate a tali endpoint siano correttamente inserite nell'elenco di controllo di accesso per l'account utente RunAs con cui viene eseguito il servizio. In caso contrario, **http.sys** non avrà accesso al servizio e le chiamate del client avranno esito negativo. L'esempio seguente applica l'account Customer3 a un endpoint denominato **ServiceEndpointName**, a cui assegna diritti di accesso completi.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Per l'endpoint HTTPS, è necessario indicare anche il nome del certificato da restituire al client. A tale scopo, è possibile usare **EndpointBindingPolicy** con il certificato definito nella sezione Certificates del manifesto dell'applicazione.

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
Il manifesto dell'applicazione riportato di seguito illustra molte delle impostazioni descritte in precedenza:

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
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
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

<!---HONumber=AcomDC_0224_2016-->