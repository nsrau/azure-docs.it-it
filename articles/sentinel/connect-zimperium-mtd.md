---
title: Connettere Zimperium Mobile Threat Defense ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere Zimperium Mobile Threat Defense a Sentinel di Azure.
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: a11d4602882973a24e09c62c12a9dc1dcdc8246d
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501251"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Connettere Zimperium Mobile Threat Defense ad Azure Sentinel


> [!IMPORTANT]
> Il connettore dati Zimperium Mobile Threat Defense in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Il connettore Zimperium Mobile Threat Defense offre la possibilità di connettere il registro delle minacce di Zimperium con Azure Sentinel per visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sul panorama delle minacce per dispositivi mobili dell'organizzazione e migliorare le funzionalità di sicurezza.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Configurare e connettere Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense può integrare ed esportare i log direttamente in **Sentinel di Azure**.

1. Nel portale di Azure Sentinel fare clic su connettori dati e selezionare **Zimperium Mobile Threat Defense**.
2. Selezionare **Apri connettore pagina**.
3. Seguire le istruzioni nella pagina del connettore **Zimperium Mobile Threat Defense** , riepilogando come indicato di seguito.
 1. In zConsole fare clic su **Gestisci** nella barra di spostamento.
 2. Fare clic sulla scheda **Integrations** (Integrazioni).
 3. Fare clic sul pulsante **segnalazione minacce** , quindi sul pulsante **Aggiungi integrazioni** .
 4. Creare l'integrazione selezionando **Microsoft Azure Sentinel** tra le integrazioni disponibili e immettere l'ID e la chiave primaria dell'area di lavoro per la connessione a Sentinel di Azure.
 5. È disponibile anche l'opzione per selezionare un livello di filtro per i dati sulle minacce da inserire in Sentinel di Azure. 

4. Per ulteriori informazioni, vedere il portale di [supporto clienti di Zimperium](https://support.zimperium.com).


## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati in Log Analytics in CustomLogs ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.

Per usare lo schema pertinente in Log Analytics per Zimperium Mobile Threat Defense, cercare ZimperiumThreatLog_CL e ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Zimperium Mobile Threat Defense a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).

- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Utilizzare le cartelle di lavoro](tutorial-monitor-your-data.md) di per monitorare i dati.

Per ulteriori informazioni su Zimperium, vedere gli argomenti seguenti:

- [Zimperium](https://zimperium.com)

- [Blog sulla sicurezza di Zimperium mobile](https://blog.zimperium.com)

- [Portale di supporto clienti di Zimperium](https://support.zimperium.com)

