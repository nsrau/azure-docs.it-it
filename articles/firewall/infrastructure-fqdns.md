---
title: FQDN dell'infrastruttura per Firewall di Azure
description: Informazioni sugli FQDN dell'infrastruttura in Firewall di Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130211"
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

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).