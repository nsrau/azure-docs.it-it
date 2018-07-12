---
title: Pool di agenti DC/OS per il servizio contenitore di Azure
description: Informazioni sul funzionamento dei pool di agenti pubblici e privati con un cluster DC/OS del servizio contenitore di Azure
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9dda6d45caf69734eb135779c8bac00fea721efd
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901059"
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Pool di agenti DC/OS per il servizio contenitore di Azure
I cluster DC/OS nel servizio contenitore di Azure contengono nodi di agenti in due pool, uno pubblico e uno privato. Un'applicazione può essere distribuita indifferentemente in uno dei due pool, ma ciò influisce sull'accessibilità tra i computer nel servizio contenitore. I computer possono essere esposti a Internet (pubblico) o rimanere interni (privato). Questo articolo descrive brevemente il motivo per cui esistono i pool pubblici e i pool privati.


* **Agenti privati**: i nodi di agenti privati vengono eseguiti tramite una rete non instradabile, accessibile unicamente dalla zona di amministrazione o attraverso il router perimetrale della zona pubblica. Per impostazione predefinita, il DC/OS avvia le applicazioni in nodi di agenti privati. 

* **Agenti pubblici**: i nodi di agenti pubblici eseguono app e servizi DC/OS attraverso una rete accessibile pubblicamente. 

Per altre informazioni sulla sicurezza della rete DC/OS, vedere la [documentazione di DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Distribuire pool di agenti

I pool di agenti DC/OS del servizio contenitore di Azure vengono creati come segue:

* Il **pool privato** contiene il numero di nodi di agenti che si specifica quando si [distribuisce il cluster DC/OS](container-service-deployment.md). 

* Il **pool pubblico** contiene inizialmente un numero predeterminato di nodi di agenti. Questo pool viene aggiunto automaticamente quando viene eseguito il provisioning del cluster DC/OS.

I pool pubblico e privato sono set di scalabilità di macchine virtuali di Azure. È possibile ridimensionare questi pool dopo la distribuzione.

## <a name="use-agent-pools"></a>Uso dei pool di agenti
Per impostazione predefinita, **Marathon** distribuisce le nuove applicazioni in nodi di agenti *privati* . Per distribuire l'applicazione nei nodi *pubblici*, è necessario farlo in modo esplicito mentre la si crea. Selezionare la scheda **Optional** (Facoltativo) e immettere **slave_public** come valore di **Accepted Resource Roles** (Ruoli risorsa accettati). Questo processo è documentato [qui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e nella documentazione di [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## <a name="next-steps"></a>Passaggi successivi
* Sono disponibili altre informazioni sulla [gestione dei contenitori DC/OS](container-service-mesos-marathon-ui.md).

* Informazioni su come [aprire il firewall](container-service-enable-public-access.md) fornito da Azure per consentire l'accesso pubblico ai contenitori DC/OS.

