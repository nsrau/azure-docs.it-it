---
title: Connettere i log di Office 365 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore di log di Office 365 per inserire informazioni sulle attività di utenti e amministratori in corso in Exchange, team e SharePoint, incluso OneDrive.
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 4d3e3615ede7406a3b581171ae759ec0ec53f13e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655371"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>Connettere i log di Office 365 ad Azure Sentinel

Il connettore di log di [Office 365](/office/) introduce le informazioni di Azure Sentinel sulle attività di utenti e amministratori in corso in **Exchange** e **SharePoint** (incluso **OneDrive**) e ora anche in **Team** . Queste informazioni includono i dettagli delle azioni, ad esempio i download di file, le richieste di accesso inviate, le modifiche agli eventi del gruppo, le operazioni sulle cassette postali, gli eventi dei team, ad esempio gli eventi chat, team, Member e Channel, nonché i dettagli dell'utente che ha eseguito le azioni. La connessione dei log di Office 365 ad Azure Sentinel consente di visualizzare e analizzare questi dati nelle cartelle di lavoro, eseguire query su di essi per creare avvisi personalizzati e incorporarli per migliorare il processo di analisi, offrendo informazioni più dettagliate sulla sicurezza di Office 365.

> [!IMPORTANT]
> L'estensione del connettore di log di Office 365 **per i log di Microsoft teams** è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant.

- La distribuzione di Office 365 deve trovarsi nello stesso tenant dell'area di lavoro di Azure Sentinel.

> [!IMPORTANT]
> - Per consentire al connettore di accedere ai dati tramite l'API di gestione delle attività di Office 365, è necessario abilitare la **registrazione di controllo unificata** nella distribuzione di Office 365. A seconda del tipo di licenza di Office 365/Microsoft 365 disponibile, è possibile che non sia abilitata per impostazione predefinita. Consultare il [Centro sicurezza e conformità di Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) per verificare lo stato della registrazione di controllo unificata in base al tipo di licenza.
> - È anche possibile abilitare, disabilitare e controllare lo stato corrente della registrazione di controllo unificata di Office 365. Per istruzioni, vedere [Abilitare o disabilitare la ricerca dei log di controllo](/office365/securitycompliance/turn-audit-log-search-on-or-off).
> - Per ulteriori informazioni, vedere le informazioni di [riferimento sull'API di gestione di Office 365](/office/office-365-management-api/office-365-management-activity-api-reference).


   > [!NOTE]
   > Come indicato in precedenza, e come si vedrà nella pagina Connector in **tipi di dati**, il connettore di Office 365 per Azure Sentinel supporta attualmente l'inserimento di log di controllo solo da Microsoft Exchange e SharePoint (incluso OneDrive) **e ora da Team**. Tuttavia, esistono alcune soluzioni esterne se si è interessati a trasferire [altri dati di Office](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) in Sentinel di Azure. 

## <a name="enable-the-office-365-log-connector"></a>Abilitare il connettore di log di Office 365

### <a name="instructions-tab"></a>Scheda istruzioni

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **Office 365**, quindi selezionare **Apri pagina connettore** nel riquadro di anteprima.

1. Nella sezione **configurazione** con etichetta selezionare le caselle di controllo dei log attività di Office 365 che si vuole connettere a Sentinel di Azure e fare clic su **Applica modifiche**. 

   > [!NOTE]
   > Se in precedenza sono stati connessi più tenant ad Azure Sentinel, usando una versione precedente del connettore Office 365 che lo supporta, sarà possibile visualizzare e modificare i log raccolti da ogni tenant. Non sarà possibile aggiungere ulteriori tenant, ma è possibile rimuovere i tenant aggiunti in precedenza.

### <a name="next-steps-tab"></a>Scheda passaggi successivi

- Vedere le cartelle di lavoro consigliate, gli esempi di query e i modelli di regola di analisi forniti con il connettore di log di **Office 365** , per ottenere informazioni dettagliate sui dati di log di SharePoint, OneDrive, Exchange e teams.

- Per eseguire manualmente query sui dati di log di Office 365 nei **log**, immettere `OfficeActivity` nella prima riga della finestra query.
   - Per filtrare la query per un tipo di log specifico, immettere `| where OfficeWorkload == "<logtype>"` nella seconda riga della query, dove *\<logtype\>* è,, `SharePoint` `OneDrive` `Exchange` o `MicrosoftTeams` .

## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere Office 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Inizia a rilevare le minacce con Azure Sentinel, usando regole [predefinite](tutorial-detect-threats-built-in.md) o [personalizzate](tutorial-detect-threats-custom.md) .