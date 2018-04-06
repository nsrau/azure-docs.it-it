---
title: Assegnare criteri di accesso agli endpoint di servizio di Azure Service Fabric | Microsoft Docs
description: Informazioni su come assegnare criteri di accesso di sicurezza agli endpoint HTTP o HTTPS nel servizio di Service Fabric.
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
ms.openlocfilehash: 65f47d238d4e591ddde8937d0eb3c55931c01c3f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS
Se si applicano criteri run-as e il manifesto del servizio dichiara le risorse di endpoint HTTP, è necessario specificare **SecurityAccessPolicy**.  **SecurityAccessPolicy** assicura che le porte allocate a questi endpoint siano limitate all'account utente usato per l'esecuzione del servizio. In caso contrario, **http.sys** non ha accesso al servizio e le chiamate del client hanno esito negativo. L'esempio seguente applica l'account Customer1 a un endpoint denominato **EndpointName**, a cui assegna diritti di accesso completi.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Per un endpoint HTTPS, indicare anche il nome del certificato da restituire al client. Per fare riferimento al certificato, usare **EndpointBindingPolicy**.  Il certificato è definito nella sezione **Certificates** del manifesto dell'applicazione.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Per i passaggi successivi, leggere gli articoli seguenti:
* [Informazioni sul modello applicativo](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
