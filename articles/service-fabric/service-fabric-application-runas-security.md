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
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 62917a1d342158ec2114a9204ee1ca9e447284fa
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>Eseguire un servizio come account utente locale o account di sistema locale
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi. Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Service Fabric consente anche di eseguire le applicazioni con un account utente locale o un account di sistema locale, specificando un criterio RunAs nel manifesto dell'applicazione. I tipi di account supportati dal sistema locale sono **LocalUser**, **NetworkService**, **LocalService** e **LocalSystem**.

È possibile anche definire e creare gruppi di utenti per aggiungere uno o più utenti a ogni gruppo e gestirli insieme. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo.

> [!NOTE] 
> Se si applica un criterio RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy**.  Per altre informazioni, vedere [Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

## <a name="create-local-user-groups"></a>Creare gruppi di utenti locali
È possibile definire e creare gruppi di utenti che permettono di aggiungere uno o più utenti a un gruppo. Questo aspetto è utile quando sono presenti più utenti per punti di ingresso del servizio differenti che devono avere determinati privilegi comuni disponibili a livello di gruppo. L'esempio seguente illustra un gruppo locale denominato **LocalAdminGroup** con privilegi di amministratore. Due utenti, Customer1 e Customer2, diventano membri di questo gruppo locale in questo manifesto dell'applicazione di esempio:

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

## <a name="create-local-users"></a>Creare utenti locali
È possibile creare un utente locale che può essere usato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account **LocalUser** nella sezione Principals del manifesto dell'applicazione, Service Fabric crea account utente locali nei computer in cui viene distribuita l'applicazione. Per impostazione predefinita, questi account non hanno gli stessi nomi di quelli specificati nel manifesto dell'applicazione (ad esempio Customer3 nel manifesto dell'applicazione seguente). Vengono invece generati in modo dinamico e hanno password casuali.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Se un'applicazione richiede che l'account utente e la password siano uguali in tutti i computer, ad esempio per abilitare l'autenticazione NTLM, il manifesto del cluster deve impostare **NTLMAuthenticationEnabled** su true. Il manifesto del cluster deve anche specificare un elemento **NTLMAuthenticationPasswordSecret** che verrà usato per generare la stessa password in tutti i computer.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>Assegnare criteri ai pacchetti di codice del servizio
La sezione **RunAsPolicy** di un oggetto **ServiceManifestImport** specifica l'account nella sezione Principals che deve essere usato per eseguire un pacchetto di codice. Associa anche i pacchetti di codice nel manifesto del servizio agli account utente nella sezione Principals. È possibile specificare questa opzione per i punti di ingresso principale o di configurazione oppure specificare `All` per applicarla a entrambi. L'esempio seguente illustra l'applicazione di diversi criteri:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Se **EntryPointType** non è specificato, il valore predefinito è impostato su `EntryPointType=”Main”`. Specificare l'oggetto **SetupEntryPoint** risulta particolarmente utile per eseguire determinate operazioni di configurazione con privilegi elevati con un account di sistema, Per altre informazioni, vedere [Eseguire uno script di avvio del servizio come account utente o di sistema locale](service-fabric-run-script-at-service-startup.md). mentre il codice del servizio effettivo può essere eseguito con un account con privilegi inferiori.

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>Applicare un criterio predefinito a tutti i pacchetti di codice del servizio
La sezione **DefaultRunAsPolicy** consente di specificare un account utente predefinito per tutti i pacchetti di codice per i quali non è definito un oggetto **RunAsPolicy** specifico. Se la maggior parte dei pacchetti di codice specificati nel manifesto del servizio usato da un'applicazione deve essere eseguita con lo stesso utente, l'applicazione può definire solo criteri RunAs predefiniti con questo account utente. L'esempio seguente mostra che se l'oggetto **RunAsPolicy** non è specificato per un pacchetto di codice, questo deve essere eseguito con l'account **MyDefaultAccount** specificato nella sezione Principals.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
