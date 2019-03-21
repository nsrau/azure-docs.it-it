---
title: Eseguire un servizio di Azure Service Fabric in un account gMSA | Microsoft Docs
description: Informazioni su come eseguire un servizio come gMSA in un cluster autonomo Windows di Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 8d14d9191e74cb59c6696568ead425fca61d6f7c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873904"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Eseguire un servizio come account del servizio gestito del gruppo
In un cluster autonomo di Windows Server è possibile eseguire un servizio come account del servizio gestito del gruppo usando criteri RunAs.  Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. L'esecuzione di applicazioni con account diversi, anche in ambienti ospitati condivisi, aumenta la sicurezza reciproca. Si noti che è in uso Active Directory in locale nel dominio e non Microsoft Azure Active Directory (Azure AD). Se si usa un account del servizio gestito del gruppo, nel manifesto dell'applicazione non viene archiviata alcuna password o password crittografata.  È anche possibile eseguire un servizio come [utente o gruppo di Active Directory](service-fabric-run-service-as-ad-user-or-group.md).

L'esempio seguente mostra come creare un account gMSA denominato *svc-Test$*, come distribuire tale account del servizio gestito ai nodi del cluster e come configurare l'entità utente.

Prerequisiti:
- Il dominio richiede una chiave radice del Servizio distribuzione chiavi.
- Il dominio deve essere a livello funzionale di Windows Server 2012 o versione successiva.

1. Chiedere a un amministratore di dominio di Active Directory di creare un account di servizio gestito del gruppo usando il commandlet `New-ADServiceAccount` e di assicurarsi che `PrincipalsAllowedToRetrieveManagedPassword` includa tutti i nodi del cluster di Service Fabric. `AccountName`, `DnsHostName` e `ServicePrincipalName` devono essere univoci.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. In ognuno dei nodi del cluster di Service Fabric, ad esempio `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`, installare e testare il gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configurare l'entità utente e RunAsPolicy per fare riferimento all'utente.
    
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Come passaggio successivo, leggere gli articoli seguenti:
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
