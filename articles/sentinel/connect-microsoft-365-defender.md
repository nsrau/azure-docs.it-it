---
title: Connettere i dati non elaborati di Microsoft 365 Defender ad Azure Sentinel | Microsoft Docs
description: Informazioni su come inserire dati di eventi non elaborati da Microsoft 365 Defender in Sentinel di Azure.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655580"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Connettere i dati da Microsoft 365 Defender ad Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** era noto in precedenza come **Microsoft Threat Protection** o **MTP**.
>
> **Microsoft Defender for endpoint** era noto in precedenza come **Microsoft Defender Advanced Threat Protection** o **MDATP**.
>
> È possibile visualizzare i nomi precedenti ancora in uso per un certo periodo di tempo.

## <a name="background"></a>Sfondo

Il nuovo [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) Connector consente di trasmettere i log di **caccia avanzati** , ovvero un tipo di dati di eventi non elaborati, da Microsoft 365 Defender ad Azure Sentinel. 

Con l'integrazione di [Microsoft Defender for endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) nel Microsoft 365 Defender Security Umbrella, è ora possibile raccogliere gli eventi di [caccia avanzati](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) di Microsoft Defender per l'endpoint usando il connettore Microsoft 365 Defender e trasmetterli direttamente nelle nuove tabelle create per lo scopo nell'area di lavoro di Sentinel di Azure. Queste tabelle sono basate sullo stesso schema usato nel portale di Microsoft 365 Defender, offrendo l'accesso completo al set completo di log di caccia avanzati e consentendo di eseguire le operazioni seguenti:

- Copia con facilità le query di caccia avanzate di Microsoft Defender ATP in Azure Sentinel.

- Usare i registri eventi non elaborati per fornire informazioni aggiuntive per gli avvisi, la ricerca e l'analisi e correlare gli eventi con i dati provenienti da origini dati aggiuntive in Sentinel di Azure.

- Archiviare i log con una maggiore conservazione, oltre a Microsoft Defender per endpoint o la conservazione predefinita di Microsoft 365 Defender di 30 giorni. È possibile eseguire questa operazione configurando la conservazione dell'area di lavoro o configurando la conservazione per tabella in Log Analytics.

> [!IMPORTANT]
>
> Il connettore Microsoft 365 Defender è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft Defender per endpoint, come descritto in [configurare Microsoft Defender per la distribuzione degli endpoint](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- All'utente deve essere assegnato il ruolo di amministratore globale nel tenant (in Azure Active Directory).

## <a name="connect-to-microsoft-365-defender"></a>Connettersi a Microsoft 365 Defender

Se Microsoft Defender for endpoint viene distribuito e l'inserimento dei dati, i registri eventi possono essere facilmente trasmessi in Sentinel di Azure.

1. In Sentinel di Azure selezionare **connettori dati**, selezionare **Microsoft 365 Defender (anteprima)** dalla raccolta e selezionare **Apri connettore pagina**.

1. I tipi di eventi seguenti possono essere raccolti dalle corrispondenti tabelle di caccia avanzate. Contrassegnare le caselle di controllo dei tipi di evento che si desidera raccogliere:

    | Tipo di eventi | Nome tabella |
    |-|-|
    | Informazioni sul computer (incluse le informazioni sul sistema operativo) | DeviceInfo |
    | Proprietà di rete dei computer | DeviceNetworkInfo |
    | Creazione di processi ed eventi correlati | DeviceProcessEvents |
    | Connessione di rete ed eventi correlati | DeviceNetworkEvents |
    | Creazione di file, modifiche e altri eventi file system | DeviceFileEvents |
    | Creazione e modifica delle voci del registro di sistema | DeviceRegistryEvents |
    | Accessi e altri eventi di autenticazione | DeviceLogonEvents |
    | Eventi di caricamento DLL | DeviceImageLoadEvents |
    | Tipi di eventi aggiuntivi | DeviceEvents |
    |

1. Fare clic su **Applica modifiche**. 

1. Per eseguire una query sulle tabelle di caccia avanzate in Log Analytics, immettere il nome della tabella dall'elenco precedente nella finestra query.

## <a name="verify-data-ingestion"></a>Verificare l'inserimento dei dati

Il grafico dei dati nella pagina del connettore indica che i dati vengono inseriti. Si noterà che viene visualizzata una singola riga che aggrega il volume di eventi in tutte le tabelle abilitate. Dopo aver abilitato il connettore, è possibile usare la query KQL seguente per generare un grafico simile del volume dell'evento per una singola tabella (modificare la tabella *DeviceEvents* nella tabella richiesta a scelta):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Nella scheda **passaggi successivi** sono disponibili alcune query di esempio che sono state incluse. È possibile eseguirli in loco o modificarli e salvarli.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come ottenere dati di eventi non elaborati da Microsoft Defender for endpoint in Azure Sentinel, usando il connettore Microsoft 365 Defender. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).