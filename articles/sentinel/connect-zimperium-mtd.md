---
title: Connettere la difesa delle minacce mobili di zimperium ad Azure Sentinel Documenti Microsoft
description: Informazioni su come connettere la difesa delle minacce mobili di zimperium ad Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587941"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Connettere la protezione dalle minacce mobili di zimperium ad Azure Sentinel


> [!IMPORTANT]
> Il connettore dati di Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Il connettore di difesa da minaccia per dispositivi mobili di tipo zimperium consente di connettere il log delle minacce di Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. Ciò offre maggiori informazioni sul panorama delle minacce mobili dell'organizzazione e migliora le funzionalità operative di sicurezza.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configurare e connettere la difesa delle minacce mobili di zimperium

È possibile integrare ed esportare i log direttamente in **Azure Sentinel**.

1. Nel portale di Azure Sentinel fare clic su Connettori dati e selezionare **Protezione da threat di piattaforma mobile .**
2. Selezionare **Apri pagina connettore**.
3. Seguire le istruzioni visualizzate nella pagina del connettore di Protezione da threat defense per dispositivi **mobili,** riepilogate come indicato di seguito.
 1. In zConsole, fare clic su **Gestisci** sulla barra di spostamento.
 2. Fare clic sulla scheda **Integrations** (Integrazioni).
 3. Fare clic sul pulsante **Segnalazione minacce** e quindi sul pulsante **Aggiungi integrazioni.**
 4. Creare l'integrazione selezionando Microsoft Azure Sentinel dalle integrazioni disponibili e immettere l'ID dell'area di lavoro e la chiave primaria per connettersi ad Azure Sentinel.Create the Integration by selecting **Microsoft Azure Sentinel** from the available integrations and enter workspace ID and primary key to connect to Azure Sentinel.
 5. È inoltre disponibile l'opzione per selezionare un livello di filtro per i dati sulle minacce da eseguire il push in Azure Sentinel.Option to select a filter level for the threat data to push to Azure Sentinel is also available. 

4. Per ulteriori informazioni, fare riferimento al portale di [assistenza clienti .](https://support.zimperium.com)


## <a name="find-your-data"></a>Trova i tuoi dati

Dopo aver stabilito una connessione corretta, i dati vengono visualizzati in Log Analytics in CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Per utilizzare lo schema pertinente in Log Analytics per la difesa delle minacce mobili di zimperium, cercare ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere voluti fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come connettere la difesa delle minacce mobili di zimperium ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).

- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.

Per ulteriori informazioni su , vedere quanto segue:

- [Zimperium](https://zimperium.com)

- [Blog sulla sicurezza mobile di zimperium](https://blog.zimperium.com)

- [Portale di assistenza clienti](https://support.zimperium.com)

