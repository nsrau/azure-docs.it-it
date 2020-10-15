---
title: Esaminare gli avvisi con Azure Sentinel | Microsoft Docs
description: Informazioni su come usare i modelli predefiniti di rilevamento delle minacce di Azure che inviano notifiche quando si verifica qualcosa di sospetto.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605403"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Esercitazione: Rilevamento predefinito delle minacce


> [!IMPORTANT]
> Il rilevamento delle minacce predefinito è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo aver [connesso le origini dati](quickstart-onboard.md)   ad Azure Sentinel, sarà necessario ricevere una notifica quando si verifica un evento sospetto. Questo è il motivo per cui Azure Sentinel fornisce modelli predefiniti predefiniti che consentono di creare regole di rilevamento delle minacce. Questi modelli sono stati progettati dal team di esperti di sicurezza e analisti di Microsoft in base a minacce note, vettori di attacco comuni e catene di escalation delle attività sospette. Le regole create da questi modelli verranno ricercate automaticamente nell'ambiente in uso per qualsiasi attività che abbia un aspetto sospetto. Molti dei modelli possono essere personalizzati per cercare attività o filtrarle in base alle esigenze. Gli avvisi generati da queste regole creeranno eventi imprevisti che è possibile assegnare ed esaminare nell'ambiente in uso.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel:

> [!div class="checklist"]
> * Usare i rilevamenti di minacce predefiniti
> * Automatizzare le risposte alle minacce

## <a name="about-out-of-the-box-detections"></a>Informazioni sui rilevamenti predefiniti

Per visualizzare tutti i rilevamenti predefiniti, passare ad **Analisi** e quindi scegliere **Rule templates** (Modelli di regole). Questa scheda contiene tutte le regole predefinite di Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel":::

Sono disponibili i tipi di modello seguenti:

- **Sicurezza Microsoft**
   
   I modelli di sicurezza Microsoft creano automaticamente gli eventi imprevisti di Sentinel di Azure dagli avvisi generati in altre soluzioni di sicurezza Microsoft, in tempo reale. È possibile utilizzare le regole di sicurezza Microsoft come modello per creare nuove regole con logica simile. Per ulteriori informazioni sulle regole di sicurezza, vedere [creazione automatica degli eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).

- **Fusione** 

    Basato sulla tecnologia Fusion, il rilevamento degli attacchi multifase avanzati in Sentinel di Azure usa algoritmi di Machine Learning scalabili che possono correlare molti avvisi ed eventi a bassa fedeltà tra più prodotti in eventi imprevisti ad alta fedeltà e interattivi. Fusion è abilitato per impostazione predefinita. Poiché la logica è nascosta e pertanto non è personalizzabile, è possibile creare solo una regola con questo modello.

- **Analisi del comportamento di Machine Learning**

    Questi modelli sono basati su algoritmi di Microsoft Machine Learning proprietari, pertanto non è possibile vedere la logica interna del modo in cui funzionano e quando vengono eseguiti. Poiché la logica è nascosta e pertanto non è personalizzabile, è possibile creare solo una regola con ogni modello di questo tipo.

- **Pianificate**

    Le regole di analisi pianificate sono basate su query predefinite scritte da esperti di sicurezza Microsoft. È possibile visualizzare la logica di query e apportare modifiche. È possibile utilizzare il modello regole pianificate e personalizzare la logica di query e le impostazioni di pianificazione per creare nuove regole.

## <a name="use-out-of-the-box-detections"></a>Usare i rilevamenti predefiniti

1. Per usare un modello predefinito, fare clic sul nome del modello e quindi fare clic sul pulsante **Crea regola** nel riquadro dettagli per creare una nuova regola attiva basata su tale modello. Ogni modello include un elenco di origini dati obbligatorie. Quando si apre il modello, le origini dati vengono verificate automaticamente per la disponibilità. Se si verifica un problema di disponibilità, è possibile che il pulsante **Crea regola** sia disabilitato o che venga visualizzato un avviso relativo a tale effetto.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel":::
 
1. Facendo clic sul pulsante **Crea regola** si apre la creazione guidata regola in base al modello selezionato. Tutti i dettagli sono riempiti automaticamente e, con i modelli di **sicurezza** **pianificati** o Microsoft, è possibile personalizzare la logica e altre impostazioni delle regole in base alle esigenze specifiche. È possibile ripetere questo processo per creare regole aggiuntive basate sul modello predefinito. Al termine della procedura guidata per la creazione della regola, sarà stata completata la creazione di una regola basata sul modello. Le nuove regole verranno visualizzate nella scheda **regole attive** .

    Per altre informazioni su come personalizzare le regole nella creazione guidata regola, vedere [esercitazione: creare regole di analisi personalizzate per rilevare le minacce](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel. 

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).

