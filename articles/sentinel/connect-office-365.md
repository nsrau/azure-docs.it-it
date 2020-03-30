---
title: Connettere i dati di Office 365 ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere i dati di Office 365 ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252506"
---
# <a name="connect-data-from-office-365-logs"></a>Connettere i dati dai registri di Office 365



È possibile trasmettere i log di controllo da [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) in Azure Sentinel con un solo clic. È possibile trasmettere i log di controllo da Office 365 all'area di lavoro di Azure Sentinel nello stesso tenant. Il connettore del log attività di Office 365 fornisce informazioni dettagliate sulle attività degli utenti in corso. Otterrai informazioni su varie azioni ed eventi relativi a utenti, amministratori, di sistema e criteri da Office 365. Connettendo i log di Office 365 in Azure Sentinel è possibile usare questi dati per visualizzare i dashboard, creare avvisi personalizzati e migliorare il processo di indagine.

> [!IMPORTANT]
> Se si dispone di una licenza E3, prima di poter accedere ai dati tramite l'API dell'attività di gestione di Office 365, è necessario abilitare la registrazione di controllo unificata per l'organizzazione di Office 365. A tale scopo, attivare il registro di controllo di Office 365. Per istruzioni, vedere [Attivare o disattivare](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)la ricerca nei log di controllo di Office 365 . Per altre informazioni, vedere Informazioni di [riferimento sull'API dell'attività](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)di gestione di Office 365.

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant.
- Il tenant deve avere il controllo unificato abilitato. Per i tenant con licenze di Office 365 E3 o E5 il controllo unificato è abilitato per impostazione predefinita. <br>Se il tenant non dispone di una di queste licenze, è necessario abilitare il controllo unificato nel tenant utilizzando uno di questi metodi:
    - [Utilizzando il cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e abilitare il parametro "UnifiedAuditLogIngestionEnabled").
    - [Tramite l'interfaccia utente del Centro conformità per la protezione &](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Connettersi a Office 365

1. In Azure Sentinel selezionare **Connettori dati** e quindi fare clic sul riquadro Office 365.In Azure Sentinel, select Data connectors and then click the **Office 365** tile.

2. Se non è già stato abilitato, è possibile farlo accedendo al pannello **Connettori dati** e selezionando Connettore **Di Office 365.** Qui è possibile fare clic su **Apri pagina connettore** e nella sezione di configurazione **Configurazione** selezionare tutti i log attività di Office 365 che si desidera connettere ad Azure Sentinel. 
   > [!NOTE]
   > Se sono già stati connessi più tenant in una versione supportata in precedenza del connettore di Office 365 in Azure Sentinel, sarà possibile visualizzare e modificare i log raccolti da ogni tenant. Non sarà possibile aggiungere altri tenant, ma è possibile rimuovere i tenant aggiunti in precedenza.
3. Per utilizzare lo schema pertinente in Log Analytics per i registri di Office 365, cercare **OfficeActivity**.


## <a name="next-steps"></a>Passaggi successivi
In this document, you learned how to connect Office 365 to Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

