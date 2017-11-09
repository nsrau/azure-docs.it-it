---
title: Informazioni sui criteri di sicurezza dei microservizi di Azure | Documentazione Microsoft
description: Panoramica dell'esecuzione di un'applicazione di Service Fabric con account di sicurezza di sistema e locali, incluso il punto SetupEntry in cui un'applicazione deve eseguire un'azione con privilegi prima dell'avvio
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: aae828489b708a5b538df1d63c12be23d0423da7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Configurare i criteri di sicurezza per l'applicazione
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi. Service Fabric permette anche di proteggere le risorse usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.

Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Service Fabric consente anche di eseguire le applicazioni con un account utente locale o un account di sistema locale, specificato nel manifesto dell'applicazione. I tipi di account supportati dal sistema locale sono **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

 Quando si esegue Service Fabric in Windows Server nel data center usando il programma di installazione autonomo, è possibile usare gli account di dominio di Active Directory, inclusi gli account del servizio gestito del gruppo.

È possibile definire e creare gruppi di utenti per aggiungere uno o più utenti a ogni gruppo e gestirli insieme. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Configurare i criteri per il punto di ingresso dell'installazione del servizio
Come descritto nel [modello applicativo](service-fabric-application-model.md), il punto di ingresso dell'installazione, **SetupEntryPoint**, è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali di Service Fabric, in genere, l'account *NetworkService*, prima di qualsiasi altro punto di ingresso. L'eseguibile specificato da **EntryPoint** è in genere l'host del servizio a esecuzione prolungata. Un punto di ingresso dell'installazione separato consente di evitare di dover eseguire l'eseguibile dell'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Il processo risultante viene monitorato e riavviato ed inizia di nuovo con **SetupEntryPoint**, se termina o si arresta in modo anomalo.

Il seguente è un semplice esempio di manifesto del servizio in cui sono presenti SetupEntryPoint e l'elemento EntryPoint principale per il servizio.

```xml
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
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Configurare i criteri usando un account locale
Dopo aver configurato un punto di ingresso di configurazione per il servizio, è possibile modificare le autorizzazioni di sicurezza in base alle quali viene eseguito nel manifesto dell'applicazione. L'esempio seguente illustra come configurare il servizio in modo che venga eseguito con i privilegi dell'account amministratore utenti.

```xml
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
```

Creare prima di tutto una sezione **Principals** con un nome utente, ad esempio SetupAdminUser. Ciò indica che l'utente è un membro del gruppo di sistema Administrators.

Successivamente, configurare nella sezione **ServiceManifestImport** un criterio per applicare tale entità a **SetupEntryPoint**. Ciò indica a Service Fabric che quando viene eseguito il file **mySetup.bat**, deve essere un profilo `RunAs` con privilegi di amministratore. Dato che *non* sono stati applicati criteri al punto di ingresso principale, il codice in **MyServiceHost.exe** viene eseguito con l'account **NetworkService** di sistema, ossia l'account predefinito con cui vengono eseguiti tutti i punti di ingresso del servizio.

A questo punto, aggiungere il file MySetup.bat al progetto di Visual Studio per testare i privilegi di amministratore. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio e aggiungere un nuovo file denominato MySetup.bat.

Verificare quindi che il file MySetup.bat sia incluso nel pacchetto del servizio. Per impostazione predefinita, non è incluso. Selezionare il file, fare clic con il pulsante destro del mouse per visualizzare il menu di scelta rapida e scegliere **Proprietà**. Nella finestra di dialogo delle proprietà verificare che **Copia nella directory di output** sia impostato su **Copia se più recente**. Vedere lo screenshot seguente.

![CopyToOutput di Visual Studio per il file batch SetupEntryPoint][image1]

Aprire il file MySetup.bat e aggiungere i comandi seguenti:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Successivamente, compilare e distribuire la soluzione a un cluster di sviluppo locale. Dopo aver avviato il servizio, come mostrato in Service Fabric Explorer, sarà possibile verificare la corretta esecuzione del file MySetup.bat in due modi. Avviare un prompt dei comandi di PowerShell e digitare:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Annotare il nome del nodo in cui il servizio è stato distribuito e avviato in Service Fabric Explorer, ad esempio Node 2. Passare quindi alla cartella di lavoro dell'istanza dell'applicazione per trovare il file out.txt che mostra il valore di **TestVariable**. Se, ad esempio, il servizio è stato distribuito in Node 2, è possibile passare a questo percorso per **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Configurare i criteri usando gli account di sistema locale
Spesso è preferibile eseguire lo script di avvio usando un account di sistema locale invece di un account amministratore. L'esecuzione dei criteri RunAs come membro del gruppo Administrators in genere non ha esito positivo in quanto per i computer è abilitato il controllo di accesso dell'utente per impostazione predefinita. In questi casi, **è consigliabile eseguire SetupEntryPoint come LocalSystem invece che come utente locale aggiunto al gruppo Administrators**. L'esempio seguente illustra l'impostazione di SetupEntryPoint per l'esecuzione come LocalSystem:

```xml
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
```

Per i cluster Linux, per eseguire un servizio o il punto di ingresso della configurazione come **root**, è possibile specificare **AccountType** come **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Avviare i comandi PowerShell da un punto di ingresso dell'installazione
Per eseguire PowerShell dal punto **SetupEntryPoint**, è possibile eseguire **PowerShell.exe** in un file batch che punta a un file di PowerShell. Aggiungere prima di tutto un file PowerShell al progetto del servizio, ad esempio **MySetup.ps1**. Ricordarsi di impostare la proprietà *Copia se più recente* in modo che il file venga incluso anche nel pacchetto servizio. L'esempio seguente illustra un file batch di esempio per avviare un file PowerShell denominato MySetup.ps1, che imposta una variabile di ambiente di sistema denominata **TestVariable**.

MySetup.bat per avviare il file PowerShell:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Nel file di PowerShell aggiungere quanto segue per impostare una variabile di ambiente di sistema:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Per impostazione predefinita, quando viene eseguito, il file batch cerca i file nella cartella dell'applicazione denominata **work**. In questo caso, quando MySetup.bat viene eseguito, il file MySetup.ps1 deve trovarsi nella stessa cartella, ovvero la cartella **code package** dell'applicazione. Per modificare questa cartella, impostare la cartella di lavoro:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>Uso del reindirizzamento della console per il debug locale
A volte può essere utile visualizzare l'output della console dall'esecuzione di uno script a scopo di debug. A questo scopo, è possibile impostare criteri di reindirizzamento della console che scrivono l'output in un file. L'output del file viene scritto nella cartella dell'applicazione denominata **log** nel nodo in cui l'applicazione viene distribuita ed eseguita. Vedere la posizione in cui si trova nell'esempio precedente.

> [!WARNING]
> Non usare mai i criteri di reindirizzamento della console in un'applicazione distribuita nell'ambiente di produzione, perché ciò può incidere sul failover dell'applicazione. Usare questa opzione *solo* a scopo di sviluppo e debug locale.  
> 
> 

L'esempio seguente illustra come impostare il reindirizzamento della console con un valore FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Se si modifica il file MySetup.ps1 per scrivere un comando **Echo**, questo verrà scritto nel file di output a scopo di debug:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**Dopo avere eseguito il debug dello script, rimuovere immediatamente i criteri di reindirizzamento della console**.

## <a name="configure-a-policy-for-service-code-packages"></a>Configurare i criteri per i pacchetti di codice del servizio
Nei passaggi precedenti è stato illustrato come applicare i criteri RunAs a SetupEntryPoint. A questo punto è possibile vedere come creare entità diverse che possono essere applicate come criteri del servizio.

### <a name="create-local-user-groups"></a>Creare gruppi di utenti locali
È possibile definire e creare gruppi di utenti che permettono di aggiungere uno o più utenti a un gruppo. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo. L'esempio seguente illustra un gruppo locale denominato **LocalAdminGroup** con privilegi di amministratore. Due utenti, Customer1 e Customer2, diventano membri di questo gruppo locale.

```xml
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
```

### <a name="create-local-users"></a>Creare utenti locali
È possibile creare un utente locale che può essere usato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account **LocalUser** nella sezione Principals del manifesto dell'applicazione, Service Fabric crea account utente locali nei computer in cui viene distribuita l'applicazione. Per impostazione predefinita, questi account non hanno gli stessi nomi di quelli specificati nel manifesto dell'applicazione (Customer3 nell'esempio seguente). Vengono invece generati in modo dinamico e hanno password casuali.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Se un'applicazione richiede che l'account utente e la password siano uguali in tutti i computer, ad esempio per abilitare l'autenticazione NTLM, il manifesto del cluster deve impostare NTLMAuthenticationEnabled su true. Il manifesto del cluster deve anche specificare un elemento NTLMAuthenticationPasswordSecret che verrà usato per generare la stessa password in tutti i computer.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Assegnare criteri ai pacchetti di codice del servizio
La sezione **RunAsPolicy** di un oggetto **ServiceManifestImport** specifica l'account nella sezione Principals che deve essere usato per eseguire un pacchetto di codice. Associa anche i pacchetti di codice nel manifesto del servizio agli account utente nella sezione Principals. È possibile specificare questa opzione per i punti di ingresso principale o di configurazione oppure specificare `All` per applicarla a entrambi. L'esempio seguente illustra l'applicazione di diversi criteri:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Se **EntryPointType** non è specificato, il valore predefinito è impostato su EntryPointType="Main". Specificare l'oggetto **SetupEntryPoint** risulta particolarmente utile per eseguire determinate operazioni di configurazione con privilegi elevati con un account di sistema, mentre il codice del servizio effettivo può essere eseguito con un account con privilegi inferiori.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Applicare un criterio predefinito a tutti i pacchetti di codice del servizio
La sezione **DefaultRunAsPolicy** consente di specificare un account utente predefinito per tutti i pacchetti di codice per i quali non è definito un oggetto **RunAsPolicy** specifico. Se la maggior parte dei pacchetti di codice specificati nel manifesto del servizio usato da un'applicazione deve essere eseguita con lo stesso utente, l'applicazione può definire solo criteri RunAs predefiniti con questo account utente. L'esempio seguente mostra che se l'oggetto **RunAsPolicy** non è specificato per un pacchetto di codice, questo deve essere eseguito con l'account **MyDefaultAccount** specificato nella sezione Principals.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Uso di un utente o un gruppo di dominio di Active Directory
Per un'istanza di Service Fabric installata in Windows Server con il programma di installazione autonomo, è possibile eseguire il servizio con le credenziali per un account utente o di gruppo di Active Directory. Si tratta di Active Directory locale nel dominio e non di Azure Active Directory (Azure AD). Usando un utente o un gruppo del dominio, sarà quindi possibile accedere ad altre risorse del dominio (ad esempio, condivisioni file) a cui sono state concesse le autorizzazioni.

L'esempio seguente illustra un utente di Active Directory denominato *TestUser* con la password del dominio crittografata con un certificato denominato *MyCert*. È possibile usare il comando `Invoke-ServiceFabricEncryptText` PowerShell per creare il testo crittografato segreto. Vedere [Gestione dei segreti nelle applicazioni di Service Fabric](service-fabric-application-secret-management.md).

È necessario distribuire la chiave privata del certificato per decrittografare la password nel computer locale con un metodo fuori banda; in Azure questa operazione viene eseguita usando Azure Resource Manager. Quando distribuisce il pacchetto servizio nel computer, Service Fabric può quindi decrittografare il segreto e, con il nome utente, eseguire l'autenticazione ad Active Directory per l'esecuzione con queste credenziali.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Usare un account del servizio gestito del gruppo.
Per un'istanza di Service Fabric installata in Windows Server con il programma di installazione autonomo, è possibile eseguire il servizio come account del servizio gestito del gruppo (gMSA). Si noti che si tratta di Active Directory locale nel dominio e non di Azure Active Directory (Azure AD). Usando un account gMSA, nel `Application Manifest` non viene archiviata alcuna password o password crittografata.

L'esempio seguente mostra come creare un account gMSA denominato *svc-Test$*, come distribuire tale account del servizio gestito ai nodi del cluster e come configurare l'entità utente.

##### <a name="prerequisites"></a>Prerequisiti.
- Il dominio richiede una chiave radice del Servizio distribuzione chiavi.
- Il dominio deve essere a livello funzionale di Windows Server 2012 o versione successiva.

##### <a name="example"></a>Esempio
1. Chiedere a un amministratore di dominio di Active Directory di creare un account del servizio gestito del gruppo usando il cmdlet `New-ADServiceAccount` e assicurarsi che `PrincipalsAllowedToRetrieveManagedPassword` includa tutti i nodi del cluster di Service Fabric. Si noti che `AccountName`, `DnsHostName` e `ServicePrincipalName` devono essere univoci.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. In ognuno dei nodi del cluster di Service Fabric, ad esempio `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`, installare e testare l'account del servizio gestito del gruppo.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Configurare l'entità utente e RunAsPolicy per fare riferimento all'utente.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Assegnare un criterio di accesso di sicurezza per gli endpoint HTTP e HTTPS
Se si applicano criteri RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy** per garantire che le porte allocate a questi endpoint siano correttamente inserite nell'elenco di controllo di accesso per l'account utente RunAs con cui viene eseguito il servizio. In caso contrario, **http.sys** non ha accesso al servizio e le chiamate del client hanno esito negativo. L'esempio seguente applica l'account Customer1 a un endpoint denominato **EndpointName**, a cui assegna diritti di accesso completi.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Per l'endpoint HTTPS, è necessario indicare anche il nome del certificato da restituire al client. A tale scopo, è possibile usare **EndpointBindingPolicy**con il certificato definito nella sezione Certificates del manifesto dell'applicazione.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Aggiornamento di più applicazioni con endpoint https
È necessario prestare attenzione a non utilizzare la **stessa porta** per istanze diverse della stessa applicazione quando si usa http**s**. Ciò è dovuto al fatto che Service Fabric non sarà in grado di aggiornare il certificato per una delle istanze dell'applicazione. Ad esempio, se sia l'applicazione 1 che l'applicazione 2 desiderano eseguire l'aggiornamento del certificato 1 al certificato 2. Al momento dell'aggiornamento, Service Fabric potrebbe avere eliminato la registrazione del certificato 1 con http.sys anche se è ancora utilizzato dall'altra applicazione. Per evitare questo problema, Service Fabric rileva che è già presente un'altra istanza dell'applicazione registrata sulla porta con il certificato (a causa di http.sys) e l'operazione ha esito negativo.

Pertanto Service Fabric non supporta l'aggiornamento di due servizi diversi mediante **la stessa porta** in istanze dell'applicazione diverse. In altre parole, non è possibile utilizzare lo stesso certificato in servizi diversi sulla stessa porta. Se è necessario avere un certificato condiviso sulla stessa porta, occorre verificare che i servizi si trovino su computer diversi con vincoli di posizionamento. In alternativa, utilizzare le porte dinamiche di Service Fabric se possibile per ogni servizio in ogni istanza dell'applicazione. 

Se si verifica un errore di aggiornamento con https, viene visualizzato un avviso indicante che l'API del server Windows HTTP non supporta più certificati per le applicazioni che condividono una porta.

## <a name="a-complete-application-manifest-example"></a>Esempio completo del manifesto dell'applicazione
Il manifesto dell'applicazione seguente illustra molte delle impostazioni:

```xml
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
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
