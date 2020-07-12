---
title: Configurare gMSA per i servizi contenitore di Azure Service Fabric
description: Informazioni su come configurare gli account del servizio gestito del gruppo (gMSA) per un contenitore in esecuzione in Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: d34b4c6e11628b6a4843f8a9077ebf69c9e023fe
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260876"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurare gMSA per i contenitori Windows in esecuzione in Service Fabric

Per configurare gMSA (account del servizio gestito del gruppo), un file di specifica delle credenziali (`credspec`) viene posizionato in tutti i nodi del cluster. Il file può essere copiato in tutti i nodi tramite un'estensione della macchina virtuale.  Il file `credspec` deve contenere le informazioni dell'account gMSA. Per altre informazioni sul `credspec` file, vedere [creare una specifica delle credenziali](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). La specifica delle credenziali e il `Hostname` tag sono specificati nel manifesto dell'applicazione. Il tag `Hostname` deve corrispondere al nome dell'account gMSA in cui viene eseguito il contenitore.  Il tag `Hostname` consente al contenitore di autenticarsi presso altri servizi nel dominio tramite l'autenticazione Kerberos.  Un esempio per specificare `Hostname` e `credspec` nel manifesto dell'applicazione è illustrato nel frammento seguente:

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Come passaggio successivo, leggere gli articoli seguenti:

* [Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-get-started-containers-linux.md)
