---
title: Eseguire un service di Azure Service Fabric nel sistema e negli account di sicurezza locali | Microsoft Docs
description: Informazioni su come eseguire un'applicazione di Service Fabric nel sistema e negli account di sicurezza locali.  Creare entità di sicurezza e applicare i criteri RunAs per eseguire in modo sicuro i servizi.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 471e5a79600d6a963a4fa5b6cec8d2cc16137489
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Eseguire un servizio come account utente locale o account di sistema locale
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi. Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Service Fabric consente anche di eseguire le applicazioni in un account utente o di sistema locale. I tipi di account supportati dal sistema locale sono **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.  Se si esegue Service Fabric su un cluster Windows autonomo, è possibile eseguire un servizio con [account di dominio di Active Directory](service-fabric-run-service-as-ad-user-or-group.md) o [account del servizio gestiti del gruppo](service-fabric-run-service-as-gmsa.md).

Nel manifesto dell'applicazione si definiscono gli account utente necessari per eseguire i servizi o proteggere le risorse nella sezione **Principals**. È anche possibile definire e creare gruppi di utenti per gestire uno o più utenti insieme. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che necessitano di privilegi comuni disponibili a livello di gruppo.  Viene quindi fatto riferimento agli utenti in un criterio RunAs, che viene applicato a un servizio specifico o a tutti i servizi dell'applicazione. 

Per impostazione predefinita, il criterio RunAs viene applicato al punto di ingresso principale.  È anche possibile applicare un criterio RunAs al punto di ingresso dell'installazione, se è necessario [eseguire determinate operazioni di installazione con privilegi elevati in un account di sistema](service-fabric-run-script-at-service-startup.md), oppure sia al punto di ingresso principale che a quello dell'installazione.  

> [!NOTE] 
> Se si applica un criterio RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy**.  Per altre informazioni, vedere [Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="run-a-service-as-a-local-user"></a>Eseguire un servizio come utente locale
È possibile creare un utente locale che può essere usato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account **LocalUser** nella sezione Principals del manifesto dell'applicazione, Service Fabric crea account utente locali nei computer in cui viene distribuita l'applicazione. Per impostazione predefinita, questi account non hanno gli stessi nomi di quelli specificati nel manifesto dell'applicazione (ad esempio *Customer3* nel manifesto dell'applicazione seguente). Vengono invece generati in modo dinamico e hanno password casuali.

Nella sezione **RunAsPolicy** di un oggetto **ServiceManifestImport** specificare l'account utente della sezione **Principals** per eseguire il pacchetto di codice del servizio.  L'esempio seguente illustra come creare un utente locale e applicare un criterio RunAs al punto di ingresso principale:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="Customer3" />
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="create-a-local-user-group"></a>Creare un gruppo di utenti locale
È possibile creare gruppi di utenti e aggiungere uno o più utenti al gruppo. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo. L'esempio di manifesto dell'applicazione seguente illustra un gruppo locale denominato *LocalAdminGroup* con privilegi di amministratore. Due utenti, *Customer1* e *Customer2*, diventano membri di questo gruppo locale. Nella sezione **ServiceManifestImport** viene applicato un criterio RunAs per eseguire il pacchetto di codice *Stateful1Pkg* come *Customer2*.  Un altro criterio RunAs viene applicato per eseguire il pacchetto di codice *Web1Pkg* come *Customer1*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer2" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" EntryPointType="Main"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="Stateful1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
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
</ApplicationManifest>
```

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Applicare un criterio predefinito a tutti i pacchetti di codice del servizio
La sezione **DefaultRunAsPolicy** consente di specificare un account utente predefinito per tutti i pacchetti di codice per i quali non è definito un oggetto **RunAsPolicy** specifico. Se la maggior parte dei pacchetti di codice specificati nel manifesto del servizio usato da un'applicazione deve essere eseguita con lo stesso utente, l'applicazione può definire solo criteri RunAs predefiniti con questo account utente. L'esempio seguente mostra che, se l'oggetto **RunAsPolicy** non è specificato per un pacchetto di codice, questo deve essere eseguito con l'utente **MyDefaultAccount** specificato nella sezione Principals.  I tipi di account supportati sono LocalUser, NetworkService, LocalSystem e LocalService.  Se si usa un utente o un servizio locale, specificare anche il nome e la password dell'account.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application7Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    
  </ServiceManifestImport>
  <DefaultServices>    
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <Policies>
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
  </Policies>
</ApplicationManifest>
```

## <a name="debug-a-code-package-locally-using-console-redirection"></a>Eseguire il debug di un pacchetto di codice localmente usando il reindirizzamento della console
A scopo di debug può talvolta essere utile visualizzare l'output generato nella console da un servizio in esecuzione. È possibile impostare criteri di reindirizzamento della console per il punto di ingresso nel manifesto del servizio, che scrive l'output in un file. L'output del file viene scritto nella cartella dell'applicazione denominata **log** nel nodo del cluster in cui l'applicazione viene distribuita ed eseguita. 

> [!WARNING]
> Non usare mai i criteri di reindirizzamento della console in un'applicazione distribuita nell'ambiente di produzione, perché ciò può incidere sul failover dell'applicazione. Usare questa opzione *solo* a scopo di sviluppo e debug locale.  
> 
> 

L'esempio di manifesto del servizio seguente illustra come abilitare il reindirizzamento della console con un valore FileRetentionCount:

```xml
<CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
        <ConsoleRedirection FileRetentionCount="10"/>
      </ExeHost>
    </EntryPoint>
</CodePackage>

```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
