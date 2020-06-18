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
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758572"
---
# <a name="connect-data-from-office-365-logs"></a>Connettere i dati dai log di Office 365



È possibile trasmettere i log di controllo da [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) ad Azure Sentinel con un solo clic. È possibile trasmettere i log di controllo da Office 365 nell'area di lavoro di Azure Sentinel nello stesso tenant. Il connettore log attività di Office 365 fornisce dati analitici sulle attività in corso dell'utente. Si otterranno informazioni sui vari utenti, amministratore, sistema, azioni dei criteri ed eventi da Office 365. Connettendo i log di Office 365 ad Azure Sentinel è possibile usare questi dati per visualizzare i dashboard, creare avvisi personalizzati e migliorare il processo di indagine.

> [!IMPORTANT]
> Se si dispone di una licenza E3, prima di poter accedere ai dati tramite l'API di attività di gestione di Office 365, è necessario abilitare i log di controllo unificati per l'organizzazione di Office 365. A tale scopo, attivare il log di controllo di Office 365. Per istruzioni, vedere [Abilitare o disabilitare la ricerca dei log di controllo](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Per altre informazioni, vedere la [documentazione relativa all'API di attività di gestione di Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Prerequisiti

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant.
- Per il tenant deve essere abilitato il controllo unificato. Per i tenant con licenze Office 365 E3 o E5 il controllo unificato è abilitato per impostazione predefinita. <br>Se il tenant non dispone di una di queste licenze, è necessario abilitare il controllo unificato nel tenant usando uno di questi metodi:
    - [Usando il cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e abilitando il parametro "UnifiedAuditLogIngestionEnabled".
    - [Usando l'interfaccia utente del Centro sicurezza e conformità](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > Attualmente il connettere dati O365 acquisisce automaticamente solo attività Exchange e SharePoint come indicato nella pagina dei connettori nella sezione relativa ai tipi di dati. Si consiglia di controllare [questo articolo nel caso in cui si richiedano i dati di controllo dei team e si proteggano i team mediante Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761). 

## <a name="connect-to-office-365"></a>Connettersi a Office 365

1. In Azure Sentinel selezionare **Connettori dati**, quindi fare clic sul riquadro **Office 365**.

2. Se non è già stato abilitato, è possibile farlo selezionando il pannello **Connettori dati** e selezionando il connettore **Office 365**. Qui è possibile fare clic sulla **pagina Apri connettore** e nella sezione di configurazione con l'etichetta **Configurazione** selezionare tutti i log attività di Office 365 che si desidera connettere ad Azure Sentinel. 
   > [!NOTE]
   > Se sono già stati connessi più tenant in una versione supportata in precedenza del connettore Office 365 in Azure Sentinel, sarà possibile visualizzare e modificare i log raccolti da ogni tenant. Non sarà possibile aggiungere ulteriori tenant, ma è possibile rimuovere i tenant aggiunti in precedenza.
3. Per usare lo schema pertinente in analisi dei log per i log di Office 365, cercare **OfficeActivity**.


## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Office 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

