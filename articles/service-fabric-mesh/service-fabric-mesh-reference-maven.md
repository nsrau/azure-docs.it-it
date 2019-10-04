---
title: Riferimento per il plug-in Maven di Azure Service Fabric Mesh | Microsoft Docs
description: Contiene informazioni di riferimento per l'uso del plug-in Maven per Service Fabric Mesh
services: service-fabric-mesh
keywords: maven, java, cli
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.service: service-fabric-mesh
manager: subramar
ms.openlocfilehash: 27651d19e276571cf49a0aa1a199ef35c87c3ba4
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537697"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Plug-in Maven per Service Fabric Mesh

## <a name="prerequisites"></a>Prerequisiti

- SDK per Java
- Maven
- Interfaccia della riga di comando di Azure con estensione Mesh
- Interfaccia della riga di comando di Service Fabric

## <a name="goals"></a>Obiettivi

### `azure-sfmesh:init`
- Crea una cartella `servicefabric` che contiene una cartella `appresources` con il file `application.yaml`. 

### `azure-sfmesh:addservice`
- Crea una cartella all'interno della cartella `servicefabric` con il nome del servizio e crea il file YAML del servizio. 

### `azure-sfmesh:addnetwork`
- Genera un file YAML `network` con il nome di rete specificato nella cartella `appresources` 

### `azure-sfmesh:addgateway`
- Genera un file YAML `gateway` con il nome di gateway specificato nella cartella `appresources` 

#### `azure-sfmesh:addvolume`
- Genera una `volume` YAML con il nome del volume fornito nel `appresources` cartella.

### `azure-sfmesh:addsecret`
- Genera un file YAML `secret` con il nome del segreto specificato nella cartella `appresources` 

### `azure-sfmesh:addsecretvalue`
- Genera un file YAML `secretvalue` con il segreto e il nome del valore del segreto specificati nella cartella `appresources` 

### `azure-sfmesh:deploy`
- Unisce i file YAML della cartella `servicefabric` e crea un file JSON del modello di Azure Resource Manager nella cartella corrente.
- Distribuisce tutte le risorse nell'ambiente di Azure Service Fabric Mesh 

### `azure-sfmesh:deploytocluster`
- Crea una cartella (`meshDeploy`) che contiene i JSON di distribuzione generati da file YAML applicabili per i cluster di Service Fabric
- Distribuisce tutte le risorse al cluster Service Fabric
 

## <a name="usage"></a>Uso

Per usare il plug-in Maven nell'app Java Maven, aggiungere il frammento di codice seguente al file pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Configurazione comune

Il plug-in Maven attualmente non supporta le configurazioni comuni dei plug-in Maven per Azure.

## <a name="how-to"></a>Procedura

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Inizializzare il progetto Maven per Azure Service Fabric Mesh
Eseguire il comando seguente per creare il file YAML delle risorse dell'applicazione.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Crea una cartella denominata `servicefabric->appresources` nella cartella radice che contiene un file YAML di applicazione denominato `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Aggiungere una risorsa all'applicazione

#### <a name="add-a-new-network-to-your-application"></a>Aggiungere una nuova rete all'applicazione
Eseguire il comando seguente per creare un file YAML di risorse di rete. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Crea nella cartella `servicefabric->appresources` un file YAML di rete denominato `network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Aggiungere un nuovo servizio all'applicazione
Eseguire il comando seguente per creare un file YAML di servizio. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Crea nella cartella `servicefabric->helloworldservice` un file YAML di servizio denominato `service_helloworldservice` che fa riferimento a `helloworldservicenetwork` e all'app `helloworldserver`
- Il servizio dovrebbe essere in ascolto sulla porta 8080
- Il servizio deve usare ***helloworldserver:latest*** come propria immagine del contenitore.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Aggiungere una nuova risorsa del gateway all'applicazione
Eseguire il comando seguente per creare un file YAML di risorse del gateway. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Crea nella cartella `servicefabric->appresources` un nuovo file YAML del gateway denominato `gateway_helloworldgateway`
- Fa riferimento a `helloworldservicelistener` come al listener del servizio in ascolto per le chiamate da questo gateway. Fa anche riferimento a `helloworldservice` come servizio, a `helloworldservicenetwork` come rete e a `helloworldserver` come applicazione. 
- Ascolta le richieste sulla porta 80

#### <a name="add-a-new-volume-to-your-application"></a>Aggiungere un nuovo volume all'applicazione
Eseguire il comando seguente per creare un file YAML di risorse di volume. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Crea nella cartella `servicefabric->appresources` un file YAML di volume denominato `volume_vol1`
- Imposta le proprietà per i parametri richiesti, `volumeAccountKey`, e `volumeShareName` come sopra
- Per altre informazioni su come fare riferimento al volume creato, vedere l'articolo sulla [distribuzione delle app usando il volume di File di Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Aggiungere una nuova risorsa di segreto all'applicazione
Eseguire il comando seguente per creare un file YAML di risorse di segreto. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Crea nella cartella `servicefabric->appresources` un file YAML di segreto denominato `secret_secret1`
- Per altre informazioni su come fare riferimento al segreto creato, visitare l'articolo sulla [gestione dei segreti](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Aggiungere una nuova risorsa secretvalue all'applicazione
Eseguire il comando seguente per creare un file YAML di risorse secretvalue. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Creare nella cartella `servicefabric->appresources` un file YAML secretvalue denominato `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale

Con l'aiuto dell'obiettivo `azure-sfmesh:deploytocluster`, è possibile eseguire l'applicazione in locale usando il comando seguente:

```cmd
mvn azure-sfmesh:deploytocluster
```

Per impostazione predefinita questo obiettivo consente di distribuire risorse nel cluster locale. Se la distribuzione avviene nel cluster locale, si presume che sia presente e operativo un cluster di Service Fabric locale. Il cluster di Service Fabric locale per le risorse è attualmente supportato solo in [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

- Crea documenti JSON da file YAML applicabili per i cluster di Service Fabric
- Distribuisce quindi nell'endpoint del cluster

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Distribuire l'applicazione in Azure Service Fabric Mesh

Con l'aiuto dell'obiettivo `azure-sfmesh:deploy`, è possibile effettuare la distribuzione nell'ambiente Service Fabric Mesh eseguendo il comando riportato di seguito:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Crea un gruppo di risorse denominato `todoapprg` se non esiste.
- Crea un file JSON del modello di Azure Resource Manager con l'unione di file YAML. 
- Distribuisce il JSON nell'ambiente di Azure Service Fabric Mesh.
