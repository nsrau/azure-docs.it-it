---
title: FQDN dell'infrastruttura per Firewall di Azure
description: Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74168973"
---
# <a name="infrastructure-fqdns"></a>FQDN dell'infrastruttura

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. 

La raccolta di regole predefinite include i servizi seguenti:

- Accesso di calcolo al repository di immagini della piattaforma di archiviazione
- Accesso alle risorse di archiviazione per lo stato dei dischi gestiti
- Diagnostica di Azure e registrazione (MDS)

## <a name="overriding"></a>Override 

È possibile eseguire l'override di questa raccolta di regole predefinite dell'infrastruttura creando una raccolta di regole di applicazione deny all che viene elaborata per ultima. Verrà sempre elaborata prima della raccolta di regole dell'infrastruttura. Qualsiasi elemento non incluso nella raccolta di regole dell'infrastruttura viene negato per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare un firewall di Azure](tutorial-firewall-deploy-portal.md).