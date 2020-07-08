---
title: Connettere i log di Office 365 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di log di Office 365 per fornire informazioni sulle attività di amministrazione e utente in corso in Exchange e SharePoint, incluso OneDrive.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559349"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Connettere i log di Office 365 ad Azure Sentinel

Il connettore di log di [Office 365](https://docs.microsoft.com/office/) riporta le informazioni di Azure Sentinel sulle attività degli utenti e degli amministratori in corso in **Exchange** e **SharePoint** (incluso **OneDrive**). Queste informazioni includono i dettagli delle azioni, ad esempio i download di file, le richieste di accesso inviate, le modifiche agli eventi del gruppo e le operazioni delle cassette postali, nonché i dettagli dell'utente che ha eseguito le azioni. La connessione dei log di Office 365 ad Azure Sentinel consente di visualizzare e analizzare questi dati nelle cartelle di lavoro, eseguire query su di essi per creare avvisi personalizzati e incorporarli per migliorare il processo di analisi, offrendo informazioni più dettagliate sulla sicurezza di Office 365.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant.

- La distribuzione di Office 365 deve trovarsi nello stesso tenant dell'area di lavoro di Azure Sentinel.

> [!IMPORTANT]
> - Per consentire al connettore di accedere ai dati tramite l'API di gestione delle attività di Office 365, è necessario abilitare la **registrazione di controllo unificata** nella distribuzione di Office 365. A seconda del tipo di licenza di Office 365/Microsoft 365 disponibile, è possibile che non sia abilitata per impostazione predefinita. Consultare il [Centro sicurezza e conformità di Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) per verificare lo stato della registrazione di controllo unificata in base al tipo di licenza.
> - È anche possibile abilitare, disabilitare e controllare lo stato corrente della registrazione di controllo unificata di Office 365. Per istruzioni, vedere [Abilitare o disabilitare la ricerca dei log di controllo](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Per ulteriori informazioni, vedere le informazioni di [riferimento sull'API di gestione di Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Come indicato in precedenza, e come si vedrà nella pagina Connector in **tipi di dati**, il connettore di Office 365 per Azure Sentinel supporta attualmente l'inserimento di log di controllo solo da Microsoft Exchange e SharePoint (incluso OneDrive). Tuttavia, esistono alcune soluzioni esterne se si è interessati a importare [dati da Team](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761) o da [altri dati di Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) in Sentinel di Azure. 

## <a name="enable-the-office-365-log-connector"></a>Abilitare il connettore di log di Office 365

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dall'elenco **connettori dati** fare clic su **Office 365**e quindi sul pulsante **Apri pagina connettore** in basso a destra.

1. Nella sezione **configurazione**con etichetta selezionare le caselle di controllo dei log attività di Office 365 che si vuole connettere a Sentinel di Azure e fare clic su **Applica modifiche**. 

   > [!NOTE]
   > Se in precedenza sono stati connessi più tenant ad Azure Sentinel, usando una versione precedente del connettore Office 365 che lo supporta, sarà possibile visualizzare e modificare i log raccolti da ogni tenant. Non sarà possibile aggiungere ulteriori tenant, ma è possibile rimuovere i tenant aggiunti in precedenza.

1. Per eseguire query sui dati di log di Office 365 in Log Analytics, digitare `OfficeActivity` nella prima riga della finestra della query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Office 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .

