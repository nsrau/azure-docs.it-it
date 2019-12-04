---
title: 'Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico | Microsoft Docs'
description: Questo argomento descrive la funzionalità di aggiornamento automatico integrato nell'agente di provisioning di Azure AD Connect Cloud.
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
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794458"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico

Verificare che l'installazione dell'agente di provisioning di Azure AD Connect Cloud sia sempre aggiornata non è mai stata così semplice con la funzionalità di **aggiornamento automatico** . Questa funzionalità è abilitata per impostazione predefinita e non può essere disabilitata.

L'agente è installato qui: **"programma FILES\AZURE ad Connect provisioning Agent\AADConnectProvisioningAgent.exe"**

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione del file dell'agente](media/how-to-automatic-upgrade/agent1.png)

Agent Updater viene installato qui: **"Program FILES\AZURE ad Connect provisioning Agent Updater\AzureADConnectAgentUpdater.exe"**

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione di aggiornamento agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Disinstallazione dell'agente
Per rimuovere l'agente, passare a **Disinstalla o modificare un programma** e disinstallare quanto segue:

- Microsoft Azure AD Connect Agent Updater
- Microsoft Azure AD Connect Provisioning Agent
- Microsoft Azure AD Connect Provisioning Agent Package

![Rimozione agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Che cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect provisioning cloud?](what-is-cloud-provisioning.md)

