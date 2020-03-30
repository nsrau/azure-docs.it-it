---
title: Digest di raccomandazione per Azure AdvisorRecommendation digest for Azure Advisor
description: Ottenere un riepilogo periodico per i consigli attiviGet periodic summary for your active recommendations
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502464"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configurare il riepilogo periodico per i suggerimenti

I digest dei suggerimenti di Advisor forniscono un modo semplice e proattivo per rimanere **aggiornati** sui consigli attivi, in diverse categorie. La funzionalità consente di configurare notifiche periodiche per il riepilogo di tutti i consigli attivi, in diverse categorie. È possibile scegliere il canale desiderato per le notifiche come e-mail, sms o altri, utilizzando gruppi di azione. In questo articolo viene illustrato come impostare un digest di raccomandazione per i suggerimenti di Advisor.This article shows you how to set-up a **recommendation digests** for your Advisor recommendations.


## <a name="setting-up-your-recommendation-digest"></a>Impostazione del digest della raccomandazione 

L'esperienza di creazione del **digest di raccomandazione** consente di configurare il riepilogo. È possibile selezionare i seguenti parametri per le configurazioni:
1. Categoria: Abbiamo categorie di raccomandazione come costo, alta disponibilità, prestazioni ed eccellenza operativa. La funzionalità non è ancora disponibile per le raccomandazioni sulla sicurezza.
2. Frequenza del digest: la frequenza delle notifiche di riepilogo può essere settimanale, bisettimanale e mensile.
3. Gruppo di azioni: è possibile selezionare un gruppo di azioni esistente o creare un nuovo gruppo di azioni. Per ulteriori informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)di azioni .
4. Lingua per il digest
5. Nome digest di consiglio: è possibile utilizzare una stringa di facile utilizzo per tenere traccia e monitorare meglio i digest.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Passaggi per creare il digest delle raccomandazioni nel portale di AzureSteps to create recommendation digest in Azure portal

Ecco i passaggi per creare il **digest della raccomandazione:Here** are the steps to create recommendation digest:
* **Fase 1:** Nel portale di Azure passare a **Advisor** e nella sezione **Monitoraggio** selezionare **Digest consigli** 

   ![Punto di ingresso del digest di raccomandazioneRecommendation digest entry-point](./media/digest-0.png)

* **Fase 2:** Seleziona **Nuovo digest di raccomandazione** dalla barra superiore come indicato di seguito:Select New recommendation digest from the top bar as below:

   ![Creare un digest di raccomandazioneCreate recommendation digest](./media/digest-5.png)

* **Fase 3:** Nella sezione **ambito** selezionare la **sottoscrizione** per il digest

   ![Fornire input digest di raccomandazioneProvide recommendation digest inputs](./media/digest-1.png)

* **Fase 4:** Nella sezione **della condizione,** selezionare le configurazioni come **categoria,** **frequenza** e **lingua**

   ![Fornire le condizioni di input del digest di raccomandazioneProvide recommendation digest input conditions](./media/digest-2.png)

* **Fase 5:** Nella sezione Del **gruppo** di azioni selezionare il **gruppo di azioni** per il digest. Per saperne di più qui - [Creare e gestire gruppi](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) di azioni

   ![Fornire un gruppo di azioni di input del digest di raccomandazioneProvide recommendation digest input action group](./media/digest-3.png)

* **Fase 6:** In questa sezione finale per **i dettagli del digest**è possibile assegnare nome e stato al digest dei suggerimenti. Premere **crea digest di raccomandazione** per completare la configurazione.
   ![Creazione completa del digest di raccomandazione](./media/digest-4.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione a Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Consigli per l'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
