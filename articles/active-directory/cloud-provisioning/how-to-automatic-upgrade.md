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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190314"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico

Assicurarsi che l'installazione dell'agente di provisioning in Azure Active Directory (Azure AD) sia sempre aggiornata è facile con la funzionalità di aggiornamento automatico.

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

