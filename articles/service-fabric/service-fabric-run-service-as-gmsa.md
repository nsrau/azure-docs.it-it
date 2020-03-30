---
title: Eseguire un servizio azure Service Fabric con un account gMSARun an Azure Service Fabric service under a gMSA account
description: Informazioni su come eseguire un servizio come account del servizio gestito dal gruppo (gMSA) in un cluster autonomo di Windows di Service Fabric.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988397"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Eseguire un servizio come account del servizio gestito del gruppo

In un cluster autonomo di Windows Server è possibile eseguire un servizio come account del servizio gestito di gruppo (gMSA) usando un criterio RunAs.On a Windows Server standalone cluster, you can run a service as a *group managed service account* (gMSA) using a *RunAs* policy.  Per impostazione predefinita, le applicazioni `Fabric.exe` Service Fabric vengono eseguite con l'account con cui viene eseguito il processo. L'esecuzione di applicazioni con account diversi, anche in ambienti ospitati condivisi, aumenta la sicurezza reciproca. Se si usa un account del servizio gestito del gruppo, nel manifesto dell'applicazione non viene archiviata alcuna password o password crittografata.  È anche possibile eseguire un servizio come [utente o gruppo di Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

Nell'esempio seguente viene illustrato come creare un account gMSA denominato *svc-Test,* come distribuire tale account del servizio gestito nei nodi del cluster e come configurare l'entità utente.

> [!NOTE]
> L'uso di un cluster gMSA con un cluster Service Fabric autonomo richiede Active Directory locale all'interno del dominio (anziché Azure Active Directory (Azure AD).Using a gMSA with a standalone Service Fabric cluster requires Active Directory on-premises within your domain (rather than Azure Active Directory (Azure AD)).

Prerequisiti:

- Il dominio richiede una chiave radice del Servizio distribuzione chiavi.
- Nel dominio deve essere presente almeno un controller di dominio Windows Server 2012 (o R2).

1. Fare in modo che un amministratore di dominio `New-ADServiceAccount` di Active `PrincipalsAllowedToRetrieveManagedPassword` Directory crei un account di servizio gestito dal gruppo utilizzando il cmdlet e assicurarsi che includa tutti i nodi del cluster di Service Fabric. `AccountName`, `DnsHostName` e `ServicePrincipalName` devono essere univoci.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. In ognuno dei nodi del cluster di Service Fabric, ad esempio `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`, installare e testare il gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configurare l'entità utente `RunAsPolicy` e configurare il per fare riferimento all'utente . [User](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas)
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

> [!NOTE]
> Se si applica un criterio RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare **SecurityAccessPolicy**.  Per altre informazioni, vedere [Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md).
>

I seguenti articoli ti guideranno attraverso i passaggi successivi:

- [Informazioni sul modello applicativo](service-fabric-application-model.md)
- [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
- [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
