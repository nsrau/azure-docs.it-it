---
title: Importare certificati in un contenitore in esecuzione in Azure Service Fabric | Microsoft Docs
description: Informazioni su come importare i file di certificato in un servizio contenitore di Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: ac461c0d512a5a58ce687623a53e99eeeca399e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837692"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Importare un file di certificato in un contenitore in esecuzione in Service Fabric

È possibile proteggere i servizi del contenitore specificando un certificato. Service Fabric fornisce un meccanismo per i servizi all'interno di un contenitore per accedere a un certificato che viene installato nei nodi in un cluster di Windows o Linux (versione 5.7 o versioni successive). Questo certificato deve essere installato in un archivio certificati in LocalMachine in tutti i nodi del cluster. La chiave privata corrispondente al certificato deve essere disponibile, accessibile e, in Windows, esportabile. Le informazioni del certificato vengono fornite nel manifesto dell'applicazione sotto il tag `ContainerHostPolicies` come illustrato nel frammento di codice seguente:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Per i cluster Windows, all'avvio dell'applicazione, il runtime esporta ogni certificato a cui viene fatto riferimento e la relativa chiave privata corrispondente in un file PFX, protetto con una password generata in modo casuale. Il file PFX e quello della password sono accessibili all'interno del contenitore usando, rispettivamente, la prima e la seconda variabile di ambiente che seguono: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Per i cluster Linux, i certificati (PEM) vengono copiati nel contenitore dall'archivio specificato da X509StoreName. Le variabili di ambiente corrispondenti su Linux sono le seguenti:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

In alternativa, se si hanno già i certificati nel formato richiesto e si vuole accedervi all'interno del contenitore, è possibile creare un pacchetto di dati all'interno del pacchetto dell'app e specificare quanto segue nel manifesto dell'applicazione:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

Il servizio del contenitore o del processo è responsabile dell'importazione dei file di certificato nel contenitore. Per importare il certificato, è possibile usare gli script `setupentrypoint.sh` o eseguire il codice personalizzato all'interno del processo del contenitore. Ecco il codice di esempio in C# per l'importazione del file PFX:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Questo certificato PFX può essere usato per autenticare l'applicazione o il servizio o per proteggere la comunicazione con altri servizi. Per impostazione predefinita, solo a SYSTEM è garantito l'accesso ai file tramite ACL. È possibile garantirlo ad altri account come richiesto dal servizio.

Come passaggio successivo, leggere gli articoli seguenti:

* [Distribuire un contenitore Windows in Service Fabric su Windows Server 2016](service-fabric-get-started-containers.md)
* [Distribuire un contenitore Docker in Service Fabric su Linux](service-fabric-get-started-containers-linux.md)
