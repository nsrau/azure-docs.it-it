---
title: Configurare l'interfaccia della riga di comando di Azure Service Fabric mesh
description: L'interfaccia della riga di comando di Service Fabric Mesh è necessaria per distribuire e gestire risorse in locale e in Azure Service Fabric Mesh. Di seguito viene illustrato come configurarlo.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: ea4a7764cf1ede1cfaf53b1097034c5894660376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660679"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configurare l'interfaccia della riga di comando di mesh Service Fabric
L'interfaccia della riga di comando di Service Fabric Mesh è necessaria per distribuire e gestire risorse in locale e in Azure Service Fabric Mesh. Di seguito viene illustrato come configurarlo.

È possibile usare tre tipi di interfaccia della riga di comando, riepilogati nella tabella seguente.

| Modulo interfaccia della riga di comando | Ambiente di destinazione |  Descrizione | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Interfaccia della riga di comando principale, che consente di distribuire le applicazioni e gestire le risorse nell'ambiente di Azure Service Fabric Mesh. 
| sfctl | Cluster locali | Interfaccia della riga di comando di Service Fabric, che consente di distribuire e testare le risorse di Service Fabric sui cluster locali.  
| Interfaccia della riga di comando di Maven | Cluster locali e Azure Service Fabric Mesh | Un wrapper per `az mesh` e `sfctl` che consente agli sviluppatori Java di usare un'esperienza familiare da riga di comando per l'esperienza di sviluppo locale e Azure.  

Per l'anteprima l'interfaccia della riga di comando di Azure Service Fabric Mesh viene scritta come estensione dell'interfaccia della riga di comando di Azure. È possibile installarla in Azure Cloud Shell oppure in un'installazione locale dell'interfaccia della riga di comando di Azure. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0.67 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Installare l'interfaccia della riga di comando di Azure Service Fabric Mesh

Se non è già stato fatto, installare il modulo di estensione dell'interfaccia della riga di comando di Azure Service Fabric mesh usando il comando seguente: 
 
```azurecli-interactive
az extension add --name mesh
```

Se è già installato, aggiornare il modulo dell'interfaccia della riga di comando di Azure Service Fabric mesh esistente usando il comando seguente:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Installare l'interfaccia della riga di comando di Service Fabric (sfctl) 

Seguire le istruzioni in [Interfaccia della riga di comando di Azure Service Fabric](../service-fabric/service-fabric-cli.md). Il modulo **sfctl** può essere usato per la distribuzione di applicazioni basate sul modello di risorsa sui cluster di Service Fabric nel computer locale. 

## <a name="install-the-maven-cli"></a>Installare l'interfaccia della riga di comando di Maven 

Per usare l'interfaccia della riga di comando di Maven, è necessario installare i componenti seguenti nel computer: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Interfaccia della riga di comando di Azure Mesh (az mesh) per Azure Service Fabric Mesh 
* SFCTL (sfctl) per i cluster locali 

L'interfaccia della riga di comando di Maven per Service Fabric è ancora in versione di anteprima. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Per informazioni sulla sintassi dettagliata, vedere le [informazioni di riferimento sull'interfaccia della riga di comando Maven di Service Fabric](service-fabric-mesh-reference-maven.md).

## <a name="next-steps"></a>Passaggi successivi

È anche possibile configurare l'[ambiente di sviluppo Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Risposte alle [domande e ai problemi comuni](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
