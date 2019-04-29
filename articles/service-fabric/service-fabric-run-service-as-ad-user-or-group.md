---
title: Eseguire un servizio di Azure Service Fabric come utente o gruppo di Active Directory | Microsoft Docs
description: Informazioni su come eseguire un servizio come utente o gruppo di Active Directory in un cluster Windows autonomo di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837726"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Eseguire un servizio come utente o gruppo di Active Directory
In un cluster autonomo di Windows Server è possibile eseguire un servizio come utente o gruppo di Active Directory usando i criteri RunAs.  Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. L'esecuzione di applicazioni con account diversi, anche in ambienti ospitati condivisi, aumenta la sicurezza reciproca. Si noti che è in uso Active Directory in locale nel dominio e non Microsoft Azure Active Directory (Azure AD).  È anche possibile eseguire un servizio come [account del servizio gestito del gruppo](service-fabric-run-service-as-gmsa.md).

Usando un utente o un gruppo del dominio, sarà quindi possibile accedere ad altre risorse del dominio (ad esempio, condivisioni file) a cui sono state concesse le autorizzazioni.

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

> [!NOTE] 
> Se si applicano criteri RunAs a un servizio e il manifesto del servizio dichiara le risorse dell'endpoint con il protocollo HTTP, è necessario specificare anche **SecurityAccessPolicy**.  Per altre informazioni, vedere [Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Come passaggio successivo, leggere gli articoli seguenti:
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
