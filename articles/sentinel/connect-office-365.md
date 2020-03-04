---
title: Connettere i dati di Office 365 ad Azure Sentinel | Microsoft Docs
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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252506"
---
# <a name="connect-data-from-office-365-logs"></a>Connettere i dati dai log di Office 365



È possibile trasmettere i log di controllo da [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) ad Azure Sentinel con un solo clic. È possibile trasmettere i log di controllo da Office 365 nell'area di lavoro di Azure Sentinel nello stesso tenant. Il connettore log attività di Office 365 fornisce informazioni approfondite sulle attività dell'utente in corso. Si otterranno informazioni su varie azioni utente, amministratore, sistema e criteri ed eventi di Office 365. Connettendo i log di Office 365 ad Azure Sentinel è possibile usare questi dati per visualizzare i dashboard, creare avvisi personalizzati e migliorare il processo di analisi.

> [!IMPORTANT]
> Se si dispone di una licenza E3, prima di poter accedere ai dati tramite l'API di gestione delle attività di Office 365, è necessario abilitare la registrazione di controllo unificata per l'organizzazione di Office 365. A tale scopo, accendere il registro di controllo di Office 365. Per istruzioni, vedere [attivare o disattivare la ricerca dei log di controllo di Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Per ulteriori informazioni, vedere [riferimento all'API di gestione delle attività di Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant.
- Per il tenant deve essere abilitato il controllo unificato. Per i tenant con licenze Office 365 E3 o E5 il controllo unificato è abilitato per impostazione predefinita. <br>Se il tenant non ha una di queste licenze, è necessario abilitare il controllo unificato nel tenant usando uno dei metodi seguenti:
    - [Usare il cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e abilitare il parametro "UnifiedAuditLogIngestionEnabled").
    - [Uso dell'interfaccia utente di Centro sicurezza e conformità](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Connettersi a Office 365

1. In Sentinel di Azure selezionare **connettori dati** , quindi fare clic sul riquadro **Office 365** .

2. Se non è già stato fatto, è possibile farlo accedendo al pannello **connettori dati** e selezionando connettore **Office 365** . Qui è possibile fare clic sulla **pagina Apri connettore** e nella sezione configurazione con etichetta **configurazione** Selezionare tutti i log attività di Office 365 che si vuole connettere a Sentinel di Azure. 
   > [!NOTE]
   > Se sono già stati connessi più tenant in una versione supportata in precedenza del connettore Office 365 in Azure Sentinel, sarà possibile visualizzare e modificare i log raccolti da ogni tenant. Non sarà possibile aggiungere tenant aggiuntivi, ma è possibile rimuovere i tenant aggiunti in precedenza.
3. Per usare lo schema pertinente in Log Analytics per i log di Office 365, cercare **OfficeActivity**.


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Office 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

