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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d0f7093f44a284ec26907d7c4bcfb2bdfd04763
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85360912"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico

Assicurarsi che l'installazione dell'agente di provisioning in Azure Active Directory (Azure AD) sia sempre aggiornata è facile con la funzionalità di aggiornamento automatico.

L'agente è installato qui: "programma files\Azure AD Connect provisioning Agent\AADConnectProvisioningAgent.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione del file dell'agente](media/how-to-automatic-upgrade/agent1.png)

Agent Updater viene installato qui: "Program files\Azure AD Connect provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione di aggiornamento agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Disinstallazione dell'agente
Per rimuovere l'agente, passare a **Disinstalla o modificare un programma** e disinstallare quanto segue:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![Rimozione agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

