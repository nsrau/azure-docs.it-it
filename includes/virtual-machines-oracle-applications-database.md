---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361531"
---
### <a name="database-tier"></a>Livello database

Il livello Database contiene le istanze del database per l'applicazione. Il database può essere un sistema Oracle DB, Oracle RAC o Oracle Exadata Database. 

Se si desidera utilizzare Oracle DB, l'istanza del database può essere distribuita in Azure tramite le immagini Oracle DB disponibili in Azure Marketplace.If the choice is to use Oracle DB, the database instance may be deployed on Azure via the Oracle DB images available on the Azure Marketplace. In alternativa, è possibile usare l'interconnessione tra Azure e OCI per distribuire Oracle DB in un modello PaaS in OCI.

Per Oracle RAC, è possibile distribuire Oracle RAC in Azure CloudSimple nel modello IaaS o in OCI nel modello PaaS.For Oracle RAC, you can deploy Oracle RAC on Azure CloudSimple in IaaS model or in OCI in PaaS model. Si consiglia di utilizzare un sistema RAC a due nodi. 

Infine, per i sistemi Exadata, utilizzare l'interconnessione OCI e distribuire il sistema Exadata in OCI. Il diagramma dell'architettura precedente precedente mostra un sistema Exadata distribuito in OCI su due subnet.

Per gli scenari di produzione, distribuire più istanze del database in due zone di disponibilità (se si esegue la distribuzione in Azure) o in due domini di disponibilità (in OCI). Utilizzare Oracle Active Data Guard per sincronizzare i database primario e standby.

Il livello di database riceve solo le richieste dal livello intermedio. È consigliabile impostare un gruppo di sicurezza di rete (elenco di sicurezza se si distribuisce il database in OCI) per consentire solo le richieste sulla porta 1521 dal livello intermedio e sulla porta 22 dal server bastion per motivi amministrativi.

Per i database distribuiti in OCI, è necessario configurare una rete cloud virtuale separata con un gateway di routing dinamico (DRG) connesso al circuito FastConnect.