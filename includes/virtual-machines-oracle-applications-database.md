---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361531"
---
### <a name="database-tier"></a>Livello database

Il livello database contiene le istanze di database per l'applicazione. Il database può essere un sistema di database Oracle DB, Oracle RAC o Oracle Exadata. 

Se si sceglie di usare Oracle DB, l'istanza del database può essere distribuita in Azure tramite le immagini Oracle DB disponibili in Azure Marketplace. In alternativa, è possibile usare l'interconnessione tra Azure e OCI per distribuire il Oracle DB in un modello PaaS in OCI.

Per Oracle RAC è possibile distribuire Oracle RAC in Azure CloudSimple nel modello IaaS o in OCI nel modello PaaS. Si consiglia di utilizzare un sistema RAC a due nodi. 

Infine, per i sistemi Exadata, usare l'interconnessione OCI e distribuire il sistema Exadata in OCI. Il diagramma dell'architettura precedente mostra un sistema Exadata distribuito in OCI tra due subnet.

Per gli scenari di produzione, distribuire più istanze del database tra due zone di disponibilità (se distribuite in Azure) o due domini di disponibilità (in OCI). Usare Oracle Active Data Guard per sincronizzare i database primario e di standby.

Il livello database riceve solo le richieste dal livello intermedio. Si consiglia di configurare un gruppo di sicurezza di rete (elenco di sicurezza se si distribuisce il database in OCI) in modo da consentire solo le richieste sulla porta 1521 dal livello intermedio e la porta 22 dal server Bastion per motivi amministrativi.

Per i database distribuiti in OCI, una rete cloud virtuale separata deve essere configurata con un gateway di routing dinamico (DRG) connesso al circuito FastConnect.