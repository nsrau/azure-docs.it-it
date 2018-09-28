---
title: FQDN dell'infrastruttura per Firewall di Azure
description: Informazioni sugli FQDN dell'infrastruttura in Firewall di Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994140"
---
# <a name="infrastructure-fqdns"></a>FQDN dell'infrastruttura

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. 

La raccolta di regole predefinite include i servizi seguenti:

- Accesso di calcolo al repository di immagini della piattaforma di archiviazione
- Accesso alle risorse di archiviazione per lo stato dei dischi gestiti
- Diagnostica di Azure e registrazione (MDS)
- Azure Active Directory

## <a name="overriding"></a>Override 

È possibile eseguire l'override di questa raccolta di regole predefinite dell'infrastruttura creando una raccolta di regole di applicazione deny all che viene elaborata per ultima. Verrà sempre elaborata prima della raccolta di regole dell'infrastruttura. Qualsiasi elemento non incluso nella raccolta di regole dell'infrastruttura viene negato per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [distribuire e configurare Firewall di Azure](tutorial-firewall-deploy-portal.md).