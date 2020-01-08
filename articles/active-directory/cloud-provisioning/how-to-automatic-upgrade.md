---
title: 'Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico | Microsoft Docs'
description: Questo articolo descrive la funzionalità di aggiornamento automatico integrato nell'agente di provisioning di Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549398"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico

Assicurarsi che l'installazione dell'agente di provisioning in Azure Active Directory (Azure AD) sia sempre aggiornata è facile con la funzionalità di aggiornamento automatico. Questa funzionalità è abilitata per impostazione predefinita e non può essere disabilitata.

L'agente è installato qui: "programma files\Azure AD Connect provisioning Agent\AADConnectProvisioningAgent.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione del file dell'agente](media/how-to-automatic-upgrade/agent1.png)

Agent Updater viene installato qui: "Program files\Azure AD Connect provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione di aggiornamento agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Disinstallare l'agente
Per rimuovere l'agente, passare a **Disinstalla o modificare un programma** e disinstallare quanto segue:

- **Aggiornamento dell'agente di Microsoft Azure AD Connect**
- **Microsoft Azure AD connettere l'agente di provisioning**
- **Microsoft Azure AD connettere il pacchetto dell'agente di provisioning**

![Rimozione agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

