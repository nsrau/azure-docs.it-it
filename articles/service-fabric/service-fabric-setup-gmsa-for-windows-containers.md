---
title: Configurare gMSA per i servizi contenitore di Azure Service Fabric | Microsoft Docs
description: Informazioni su come configurare gMSA per un contenitore in esecuzione in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 4ad697e01ef9e023232e2a2a16e4584a2779f84a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806301"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Configurare gMSA per i contenitori Windows in esecuzione in Service Fabric

Per configurare gMSA (account del servizio gestito del gruppo), un file di specifica delle credenziali (`credspec`) viene posizionato in tutti i nodi del cluster. Il file può essere copiato in tutti i nodi tramite un'estensione della macchina virtuale.  Il file `credspec` deve contenere le informazioni dell'account gMSA. Per altre informazioni sul file `credspec`, vedere [Service Accounts](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts) (Account del servizio). La specifica della credenziale e il tag `Hostname` vengono specificati nel manifesto dell'applicazione. Il tag `Hostname` deve corrispondere al nome dell'account gMSA in cui viene eseguito il contenitore.  Il tag `Hostname` consente al contenitore di autenticarsi presso altri servizi nel dominio tramite l'autenticazione Kerberos.  Un esempio per specificare `Hostname` e `credspec` nel manifesto dell'applicazione è illustrato nel frammento seguente:

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
