---
title: Connettere i dati di Azure Web Application Firewall (WAF) ad Azure Sentinel
description: Informazioni su come connettere i dati di Azure WAF ad Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655902"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Connettere i dati dal Web Application Firewall (WAF) di Azure

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il Web Application Firewall (WAF) di Azure fornisce una protezione centralizzata delle applicazioni Web da exploit e minacce comuni, ad esempio l'inserimento di codice e gli script tra siti. Azure WAF può essere distribuito nel servizio [Gateway applicazione Azure](../web-application-firewall/ag/ag-overview.md) , nel servizio [front](../web-application-firewall/afds/afds-overview.md) -end di Azure e tramite un criterio WAF rete per la [distribuzione di contenuti (CDN) di Azure](../web-application-firewall/cdn/cdn-overview.md) (il secondo attualmente disponibile in anteprima pubblica).
È possibile connettere i log di Azure WAF ad Azure Sentinel, consentendo di visualizzare i dati di log nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

## <a name="connect-to-azure-waf"></a>Connettersi ad Azure WAF

### <a name="instructions-tab"></a>Scheda istruzioni

1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Selezionare **Azure Web Application Firewall (WAF)** dalla raccolta di connettori dati, quindi selezionare **Apri pagina connettore** nel riquadro di anteprima.

1. Selezionare il collegamento per il tipo di risorsa WAF i cui log si vuole connettere: **aprire la risorsa del gateway applicazione >**, **aprire la risorsa porta anteriore >** o aprire la rete per la **distribuzione di contenuti (CDN) WAF Policy >** e una volta nella schermata dell'elenco di risorse, scegliere una risorsa WAF dall'elenco.

    1. Dal menu di spostamento della risorsa WAF selezionare **impostazioni di diagnostica**.

    1. Selezionare **+ Aggiungi impostazioni di diagnostica** nella parte inferiore dell'elenco.

    1. Nella schermata **impostazioni di diagnostica** Digitare un nome nel campo **nome impostazioni di diagnostica** .

    1. Fare clic sulla casella **di controllo Invia a log Analytics** . Sotto di essa verranno visualizzati due nuovi campi. Scegliere la **sottoscrizione** pertinente e l' **area di lavoro log Analytics** (in cui risiede Azure Sentinel).

    1. Fare clic sulle caselle di controllo dei tipi di regola per i quali si desidera inserire i log. Suggerimenti per ogni tipo di risorsa:

        | Risorsa | Log da selezionare per l'inserimento |
        |----------|------------------------------|
        | Gateway applicazione | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Frontdoor          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Criteri WAF della rete CDN      | WebApplicationFirewallLogs |
        |

    1. Selezionare **Salva**.

### <a name="next-steps-tab"></a>Scheda passaggi successivi

- Per informazioni dettagliate sui dati di log di Azure WAF, vedere le cartelle di lavoro, gli esempi di query e i modelli di regole di analisi consigliati combinati con **azure Web Application Firewall** Data Connector.

- Per eseguire query sui dati di Azure WAF nei **log**, digitare **AzureDiagnostics** nella finestra query.

> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (uno stripe di colori nella raccolta di connettori dati e nelle icone di connessione accanto ai nomi dei tipi di dati) vengono visualizzati come connessi (verde) solo se i dati sono stati *inseriti* in un determinato momento nelle ultime due settimane. Una volta passate due settimane senza inserimento dati, il connettore verrà visualizzato come disconnesso. Il momento in cui arrivano più dati, lo stato *connesso* restituirà.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere i log di Azure WAF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).