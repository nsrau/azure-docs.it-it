---
title: Digest della raccomandazione per Azure Advisor
description: Ottenere un riepilogo periodico per le raccomandazioni attive
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: e446fca11e029e28e44ada884efd071f3142514c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518080"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Configura riepilogo periodico per le raccomandazioni

I **digest delle raccomandazioni** di Advisor forniscono un modo semplice e proattivo per rimanere sempre aggiornati sulle raccomandazioni attive, in categorie diverse. La funzionalità offre la possibilità di configurare notifiche periodiche per il riepilogo di tutte le raccomandazioni attive, in categorie diverse. È possibile scegliere il canale desiderato per le notifiche, ad esempio posta elettronica, SMS o altri, usando i gruppi di azioni. Questo articolo illustra come configurare i digest di una **raccomandazione** per le raccomandazioni di Advisor.


## <a name="setting-up-your-recommendation-digest"></a>Impostazione del digest delle raccomandazioni 

L'esperienza di creazione del **digest di raccomandazione** consente di configurare il riepilogo. È possibile selezionare i parametri seguenti per le configurazioni:
1. Categoria: abbiamo categorie di raccomandazioni come costo, disponibilità elevata, prestazioni e eccellenza operativa. La funzionalità non è ancora disponibile per le raccomandazioni sulla sicurezza.
2. Frequenza del digest: la frequenza per le notifiche di riepilogo può essere settimanale, quindicinale e mensile.
3. Gruppo di azione: è possibile selezionare un gruppo di azioni esistente o creare un nuovo gruppo di azioni. Per altre informazioni sui gruppi di azioni, vedere [creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).
4. Lingua per il digest
5. Nome del digest di Raccomandazione: è possibile usare una stringa intuitiva per tenere traccia e monitorare meglio i digest.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Procedura per creare il digest delle raccomandazioni in portale di Azure

Ecco i passaggi per creare il **digest delle raccomandazioni:**
* **Passaggio 1:** Nel portale di Azure passare a **Advisor** e in **monitoraggio** sezione selezionare **digest di raccomandazione** . 

   ![Punto di ingresso del digest di raccomandazione](./media/digest-0.png)

* **Passaggio 2:** Selezionare **nuovo digest raccomandazione** dalla barra superiore come riportato di seguito:

   ![Crea digest delle raccomandazioni](./media/digest-5.png)

* **Passaggio 3:** Nella sezione **ambito** selezionare la **sottoscrizione** per il digest

   ![Fornire input del digest di raccomandazione](./media/digest-1.png)

* **Passaggio 4:** Nella sezione **condizione** selezionare le configurazioni come **Category**, **Frequency** e **Language** .

   ![Fornire le condizioni di input del digest di raccomandazione](./media/digest-2.png)

* **Passaggio 5:** Nella sezione **gruppo di azione** selezionare il **gruppo di azioni** per il digest. Per altre informazioni, vedere [creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md)

   ![Fornire il gruppo di azioni di input digest di raccomandazione](./media/digest-3.png)

* **Passaggio 6:** In questa sezione finale per **i dettagli del digest**è possibile assegnare il nome e lo stato al digest di raccomandazione. Premere **Crea il digest delle raccomandazioni** per completare la configurazione.
   ![Creazione del digest di raccomandazione completa](./media/digest-4.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Azure Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Advisor Cost recommendations](advisor-cost-recommendations.md) (Consigli di Advisor sui costi)
* [Consigli di Advisor sulle prestazioni](advisor-performance-recommendations.md)
* [Advisor Security recommendations](advisor-security-recommendations.md) (Consigli di Advisor sulla sicurezza)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)
* [API REST Advisor](/rest/api/advisor/)
