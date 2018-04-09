---
title: Eseguire un servizio di Azure Service Fabric come utente o gruppo di Active Directory | Microsoft Docs
description: Informazioni su come eseguire un servizio come utente o gruppo di Active Directory in un cluster Windows autonomo di Service Fabric.
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
ms.openlocfilehash: 1cf23a8f564553e65ac2c0fd34d44d81fe2327ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Eseguire un servizio come utente o gruppo di Active Directory
Azure Service Fabric consente di proteggere le applicazioni in esecuzione nel cluster con account utente diversi. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette. Per impostazione predefinita, le applicazioni di Service Fabric vengono eseguite con lo stesso account con cui viene eseguito il processo Fabric.exe. Per un cluster Windows Server autonomo, è possibile eseguire un servizio come [gMSA (group Managed Service Account, account del servizio gestito del gruppo)](service-fabric-run-service-as-gmsa.md) oppure come utente o gruppo di Active Directory usando criteri RunAs. Si noti che viene usato Active Directory locale all'interno del dominio e non Azure Active Directory (Azure AD).

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
