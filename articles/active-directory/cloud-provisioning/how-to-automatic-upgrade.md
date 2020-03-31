---
title: 'Agente di provisioning cloud di Azure AD Connect: Aggiornamento automatico Documenti Microsoft'
description: Questo articolo descrive la funzionalità di aggiornamento automatico incorporata nell'agente di provisioning cloud di Azure AD Connect.This article describes the built-in automatic upgrade feature in the Azure AD Connect cloud provisioning agent.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190314"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning cloud di Azure AD Connect: aggiornamento automaticoAzure AD Connect cloud provisioning agent: Automatic upgrade

Assicurarsi che l'installazione dell'agente di provisioning cloud di Azure Active Directory (Azure AD) Connect sia sempre aggiornata sia semplice con la funzionalità di aggiornamento automatico.

L'agente viene installato qui: "File di programma, Agente di provisioning di Azure AD Connect, AADConnectProvisioningAgent.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sull'eseguibile, selezionare le proprietà, quindi i dettagli.

![Versione del file dell'agente](media/how-to-automatic-upgrade/agent1.png)

L'agente di aggiornamento viene installato qui: "File di programma, Aggiornamento dell'agente di provisioning di Azure AD Connect, AzureADConnectAgentUpdater.exe",

Per verificare la versione, fare clic con il pulsante destro del mouse sull'eseguibile, selezionare le proprietà, quindi i dettagli.

![Versione del programma di aggiornamento dell'agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Disinstallare l'agente
Per rimuovere l'agente, passare a **Disinstallare o modificare un programma** e disinstallare quanto segue:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![Rimozione dell'agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

