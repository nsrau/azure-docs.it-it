---
title: Configurare i certificati per le applicazioni Azure Service Fabric in Linux | Microsoft Docs
description: Configurare i certificati per l'app con il runtime di Service Fabric in un cluster Linux
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: chackdan
ms.openlocfilehash: 9599d59f7f23de4e54ce323aa4a2ad837d8ed074
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773263"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certificati e sicurezza nei cluster Linux

Questo articolo fornisce informazioni sulla configurazione dei certificati X.509 nei cluster Linux.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Percorso e formato dei certificati X.509 nei nodi Linux

Service Fabric prevede, in genere, che i certificati X.509 si trovino nella directory */var/lib/sfcerts* dei nodi del cluster Linux. Questa situazione si verifica per i certificati del cluster, i certificati client e così via. In alcuni casi, per i certificati è possibile specificare un percorso diverso dalla cartella *var/lib/sfcerts*. Con i servizi Reliable Services creati usando Service Fabric Java SDK, ad esempio, è possibile specificare un percorso diverso tramite il pacchetto di configurazione (Settings.xml) per alcuni certificati specifici dell'applicazione. Per altre informazioni, vedere [Certificati a cui fa riferimento il pacchetto di configurazione (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Per i cluster Linux, Service Fabric prevede che i certificati siano presenti come un file con estensione pem contenente il certificato e la chiave privata oppure come un file con estensione crt contenente il certificato e un file con estensione key contenente la chiave privata. Tutti i file devono essere in formato PEM. 

Se si installa il certificato da Azure Key Vault usando un [modello di Resource Manager](./service-fabric-cluster-creation-create-template.md) o i comandi [PowerShell](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0), il certificato viene installato nel formato corretto nella directory */var/lib/sfcerts* in ogni nodo. Se si installa un certificato con un altro metodo, è necessario assicurarsi che il certificato sia installato correttamente nei nodi del cluster.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certificati a cui fa riferimento il manifesto dell'applicazione

I certificati specificati nel manifesto dell'applicazione, ad esempio tramite gli elementi [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) o [**EndpointCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element), devono trovarsi nella directory */var/lib/sfcerts*. Gli elementi usati per specificare i certificati nel manifesto dell'applicazione non accettano un attributo di percorso, pertanto i certificati devono trovarsi nella directory predefinita. Questi elementi accettano un attributo facoltativo **X509StoreName**. Il valore predefinito è "My", che fa riferimento alla directory */var/lib/sfcerts* nei nodi Linux. Qualsiasi altro valore non è definito in un cluster Linux. È consigliabile omettere l'attributo **X509StoreName** per le app che vengono eseguite in cluster Linux. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certificati a cui fa riferimento il pacchetto di configurazione (Settings.xml)

Per alcuni servizi, è possibile configurare i certificati X.509 in [ConfigPackage](./service-fabric-application-and-service-manifests.md) (per impostazione predefinita, Settings.xml). Ad esempio, ciò avviene quando si dichiarano i certificati usati per proteggere canali RPC per servizi Reliable Services creati con .NET Core SDK o Java SDK per Service Fabric. Esistono due modi per fare riferimento ai certificati nel pacchetto di configurazione. Il supporto varia in base all'uso di .NET Core SDK e Java SDK.

### <a name="using-x509-securitycredentialstype"></a>Uso di X509 per SecurityCredentialsType

Con .NET SDK o Java SDK, è possibile specificare **X509** per **SecurityCredentialsType**. Questo corrisponde al tipo `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials)) di `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

Il riferimento **X509** identifica la posizione del certificato in un archivio certificati. Il codice XML seguente illustra i parametri usati per specificare il percorso del certificato:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

Per un servizio in esecuzione in Linux, **LocalMachine**/**My** punta al percorso predefinito per i certificati, la directory */var/lib/sfcerts*. Per Linux, tutte le altre combinazioni di **CertificateStoreLocation** e **CertificateStoreName** non sono definite. 

Specificare sempre **LocalMachine** per il parametro **CertificateStoreLocation**. Non è necessario specificare il parametro **CertificateStoreName** perché il valore predefinito è "My". Con un riferimento **X509**, i file di certificato devono trovarsi nella directory */var/lib/sfcerts* nel nodo del cluster.  

Il codice XML seguente illustra una sezione **TransportSettings** che si basa su questo stile:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>Uso di X509_2 per SecurityCredentialsType

Con Java SDK, è possibile specificare **X509_2** per **SecurityCredentialsType**. Questo corrisponde al tipo `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)) di `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Con un riferimento **X509_2**, si specifica un parametro di percorso, in modo da poter identificare la posizione del certificato in una directory diversa da */var/lib/sfcerts*.  Il codice XML seguente illustra i parametri usati per specificare il percorso del certificato: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Il codice XML seguente illustra una sezione **TransportSettings** che si basa su questo stile.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Il certificato è specificato come file con estensione crt nel codice XML precedente. Ciò implica che nello stesso percorso sia presente anche un file con estensione key contenente la chiave privata.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Configurare un'app di Reliable Services da eseguire su cluster Linux

Gli SDK di Service Fabric consentono di comunicare con le API di runtime di Service Fabric per sfruttare i vantaggi offerti dalla piattaforma. Quando si esegue un'applicazione che usa questa funzionalità in cluster Linux sicuri, è necessario configurare l'applicazione con un certificato che può usare per la convalida con il runtime di Service Fabric. Questa configurazione è richiesta dalle applicazioni che contengono servizi di Service Fabric Reliable Services scritte tramite .NET Core SDK o Java SDK. 

Per configurare un'applicazione, aggiungere un elemento [**SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) al tag **Certificates**, che si trova nel tag **ApplicationManifest** nel file *ApplicationManifest.xml*. Il codice XML seguente illustra un certificato a cui viene fatto riferimento dalla relativa identificazione personale: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

È possibile fare riferimento al certificato del cluster o a un certificato installato in ogni nodo del cluster. In Linux i file del certificato devono trovarsi nella directory */var/lib/sfcerts*. Per altre informazioni, vedere [Percorso e formato dei certificati X.509 nei nodi Linux](#location-and-format-of-x509-certificates-on-linux-nodes).



