---
title: Analizzare gli avvisi con Azure Sentinel Documenti Microsoft
description: Usare questa esercitazione per informazioni su come analizzare gli avvisi con Azure Sentinel.Use this tutorial to learn how to investigate alerts with Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585204"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Esercitazione: Rilevare le minacce out-of-the-box


> [!IMPORTANT]
> Il rilevamento delle minacce out-of-the-box è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dopo aver [connesso le origini](quickstart-onboard.md) dati ad Azure Sentinel, si vuole ricevere una notifica quando si verifica qualcosa di sospetto. Per consentire questa operazione, Azure Sentinel fornisce modelli predefiniti. Questi modelli sono stati progettati dal team di esperti di sicurezza e analisti di Microsoft sulla base di minacce note, vettori di attacco comuni e catene di escalation di attività sospette. Dopo aver abilitato questi modelli, verranno cercati automaticamente qualsiasi attività che sembra sospetta in tutto l'ambiente. Molti dei modelli possono essere personalizzati per cercare o filtrare le attività, in base alle proprie esigenze. Gli avvisi generati da questi modelli creeranno eventi imprevisti che è possibile assegnare e analizzare nell'ambiente.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel:This tutorial helps you detect threats with Azure Sentinel:

> [!div class="checklist"]
> * Utilizzare rilevamenti out-of-the-box
> * Automatizzare le risposte alle minacce

## <a name="about-out-of-the-box-detections"></a>Informazioni sui rilevamenti predefiniti

Per visualizzare tutti i rilevamenti predefiniti, passare ad **Analisi** e quindi scegliere **Rule templates** (Modelli di regole). Questa scheda contiene tutte le regole predefinite di Azure Sentinel.

   ![Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Sono disponibili i seguenti tipi di modello:

- **Sicurezza Microsoft:** i modelli di sicurezza Microsoft creano automaticamente gli eventi imprevisti di Azure Sentinel dagli avvisi generati in altre soluzioni di sicurezza Microsoft, in tempo reale. È possibile usare le regole di sicurezza Microsoft come modello per creare nuove regole con logica simile. Per ulteriori informazioni sulle regole di sicurezza, vedere [Creare automaticamente eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).
- **Fusion-** Basato sulla tecnologia Fusion, il rilevamento avanzato degli attacchi multifase in Azure Sentinel utilizza algoritmi di apprendimento automatico scalabili in grado di correlare molti avvisi ed eventi a bassa fedeltà tra più prodotti in incidenti ad alta fedeltà e utilizzabili. Fusion è abilitata per impostazione predefinita. Poiché la logica è nascosta, non è possibile utilizzarla come modello per creare più di una regola.
- **Analisi comportamentale dell'apprendimento automatico:** questi modelli sono basati su algoritmi di apprendimento automatico Microsoft proprietari, pertanto non è possibile visualizzare la logica interna del funzionamento e dell'esecuzione. Poiché la logica è nascosta, non è possibile utilizzarla come modello per creare più di una regola.
-   **Pianificato:** le regole analitiche pianificate sono query pianificate scritte da esperti di sicurezza Microsoft.Scheduled – Scheduled analytic rules are scheduled queries written by Microsoft security experts. È possibile visualizzare la logica di query e apportarvi modifiche. È possibile utilizzare le regole pianificate come modello per creare nuove regole con logica simile.

## <a name="use-out-of-the-box-detections"></a>Utilizzare rilevamenti out-of-the-box

1. Per utilizzare un modello incorporato, fare clic su **Crea regola** per creare una nuova regola attiva basata su tale modello. Ogni voce contiene un elenco di origini dati necessarie che vengono controllate automaticamente e ciò può comportare la disabilitazione della **regola di creazione.**
  
   ![Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Verrà visualizzata la procedura guidata per la creazione della regola, in base al modello selezionato. Tutti i dettagli vengono compilati automaticamente e per **Regole pianificate** o Regole di **sicurezza Microsoft**è possibile personalizzare la logica in base all'organizzazione o creare regole aggiuntive basate sul modello predefinito. Dopo aver eseguito i passaggi della procedura guidata per la creazione di regole e aver creato una regola basata sul modello, la nuova regola viene visualizzata nella scheda **Regole attive.**

Per ulteriori informazioni sui campi della procedura guidata, vedere [Esercitazione: Creare regole analitiche personalizzate per rilevare le minacce sospette.](tutorial-detect-threats-custom.md)



## <a name="next-steps"></a>Passaggi successivi
In this tutorial, you learned how to get started detecting threats using Azure Sentinel. 

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte alle minacce automatizzate in Azure Sentinel.](tutorial-respond-threats-playbook.md)

