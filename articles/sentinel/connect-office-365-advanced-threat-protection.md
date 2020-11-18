---
title: Connettere i dati di Microsoft Defender per Office 365 (in precedenza Office 365 ATP) ad Azure Sentinel | Microsoft Docs
description: Inserire i dati di Microsoft Defender per Office 365 in Sentinel di Azure per ottenere visibilità e creare scenari di risposta automatici.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655495"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Connettere avvisi da Microsoft Defender per Office 365 

> [!IMPORTANT]
>
> - **Microsoft Defender per office 365** era noto in precedenza come **Office 365 Advanced Threat Protection (ATP)**.
>
>     Per un certo periodo di tempo, è possibile che venga visualizzato il vecchio nome ancora in uso nel prodotto (incluso il relativo connettore dati in Sentinel di Azure).
>
> - L'inserimento di avvisi di Microsoft Defender per Office 365 è attualmente disponibile in anteprima pubblica. Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
[Microsoft Defender per Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) protegge la tua organizzazione da minacce zero e altre minacce avanzate poste da malware sconosciuti nei messaggi di posta elettronica, nei collegamenti di URL dannosi e negli strumenti di collaborazione. Inserendo gli avvisi di Microsoft Defender per Office 365 in Azure Sentinel, sarà possibile usare le informazioni relative alla posta elettronica, alla condivisione di file e alle minacce basate su URL nelle operazioni di sicurezza. Puoi quindi analizzare più dettagliatamente gli eventi di sicurezza nell'intera organizzazione e creare PlayBook per una risposta efficace e immediata.

Il connettore importa gli avvisi seguenti:

- È stato rilevato un clic su URL potenzialmente dannoso 

- Messaggi di posta elettronica contenenti malware rimosso dopo il recapito

- Messaggi di posta elettronica contenenti URL attività rimossi dopo il recapito 

- Messaggio di posta elettronica segnalato dall'utente come malware o attività 

- Modelli di invio di posta elettronica sospetti rilevati 

- L'utente ha limitato l'invio di posta elettronica 

Questi avvisi possono essere visualizzati dai clienti di Office nel **Centro sicurezza e conformità di Office**.

## <a name="prerequisites"></a>Prerequisiti

- Quando si Abilita il connettore, è necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario essere un amministratore globale o un amministratore della sicurezza nel tenant dell'area di lavoro di Azure Sentinel.

- È necessario disporre di una licenza valida per [office 365 ATP piano 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (inclusa con le licenze Office 365 E5, Office 365 A5 e Microsoft 365 E5 e disponibile per l'acquisto separatamente). 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Connettersi a Microsoft Defender per Office 365

Se Microsoft Defender per Office 365 è distribuito e se i criteri sono stati configurati, è possibile inserire facilmente gli avvisi in Sentinel di Azure.

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Selezionare **Microsoft Defender per office 365** (potrebbe essere ancora chiamato *Office 365 Advanced Threat Protection*) nella raccolta di connettori e selezionare **Apri pagina connettore**.

1. Nella sezione **configurazione** fare clic su **Connetti**. 

1. Nella sezione **crea eventi imprevisti** fare clic su **Abilita**.

1. Per usare lo schema pertinente per eseguire query sugli avvisi di Office 365 ATP, cercare **SecurityAlert** e specificare il **nome del provider** come **OATP**.

1. Selezionare la scheda **passaggi successivi** per visualizzare e usare i modelli di query di esempio e di regole di analisi in bundle con il connettore Microsoft Defender per Office 365.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Microsoft Defender per Office 365 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).