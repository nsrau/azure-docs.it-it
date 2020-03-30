---
title: Pianificare e gestire i costi per Archiviazione di AzurePlan and manage costs for Azure Storage
description: Informazioni su come pianificare e gestire i costi per Archiviazione di Azure usando l'analisi dei costi nel portale di Azure.Learn how to plan for and manage costs for Azure Storage by using cost analysis in Azure portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304524"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Pianificare e gestire i costi per Archiviazione di AzurePlan and manage costs for Azure Storage

Questo articolo descrive come pianificare e gestire i costi per Archiviazione di Azure.This article describes how you plan and manage costs for Azure Storage. In primo luogo, si usa il calcolatore dei prezzi di Azure per pianificare i costi di archiviazione prima di aggiungere risorse. Dopo aver iniziato a usare le risorse di Archiviazione di Azure, usare le funzionalità di gestione dei costi per impostare i budget e monitorare i costi. È inoltre possibile esaminare i costi previsti e monitorare le tendenze di spesa per identificare le aree in cui è possibile agire.

Tenere presente che i costi per Archiviazione di Azure sono solo una parte dei costi mensili nella fattura di Azure.Keep in mind that costs for Azure Storage are only a portion of the monthly costs in your Azure bill. Anche se questo articolo spiega come pianificare e gestire i costi per Archiviazione di Azure, vengono fatturati tutti i servizi e le risorse di Azure usati per la sottoscrizione di Azure, inclusi i servizi di terze parti. Dopo aver acquisito familiarità con la gestione dei costi per Archiviazione di Azure, è possibile applicare metodi simili per gestire i costi per tutti i servizi di Azure usati nella sottoscrizione.

## <a name="prerequisites"></a>Prerequisiti

L'analisi dei costi supporta diversi tipi di account di Azure. Per visualizzare l'elenco completo dei tipi di account supportati, vedere [Understand Cost Management data](../../cost-management-billing/costs/understand-cost-mgt-data.md) (Informazioni sui dati di Gestione costi). Per visualizzare i dati relativi ai costi, è necessario effettuare almeno l'accesso in lettura per l'account Azure. Per informazioni sull'assegnazione dell'accesso ai dati di Gestione costi di Azure, vedere [Assegnare l'accesso ai dati](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Stimare i costi prima di creare un account di archiviazione di AzureEstimate costs before creating an Azure Storage account

Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi prima di creare e iniziare a trasferire i dati a un account di Archiviazione di Azure.Use the Azure pricing calculator to estimate costs before you create and begin transferring data to an Azure Storage account.

1. Nella pagina [Calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure scegliere il riquadro **Account di archiviazione.**

2. Scorrere la pagina verso il basso e individuare la sezione **Account di archiviazione** della stima.

3. Scegliere le opzioni dagli elenchi a discesa. 

   Quando si modifica il valore di questi elenchi a discesa, la stima dei costi cambia. Tale stima appare nell'angolo superiore e nella parte inferiore della stima. 
    
   ![Monitorare i costi con il riquadro Analisi costi](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Quando si modifica il valore dell'elenco a discesa **Tipo,** cambiano anche le altre opzioni visualizzate in questo foglio di lavoro. Utilizzare i collegamenti nella sezione **Ulteriori informazioni** per ulteriori informazioni sul significato di ogni opzione e su come queste opzioni influiscono sul prezzo delle operazioni correlate allo storage. 

4. Modificare le opzioni rimanenti per visualizzarne l'effetto sulla stima.

## <a name="use-budgets-and-cost-alerts"></a>Usare budget e avvisi relativi ai costi

È possibile creare [budget](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) per gestire i costi e creare avvisi per informare automaticamente gli stakeholder in caso di anomalie di spesa e rischi di costi eccessivi. Gli avvisi si basano sul confronto tra la spesa e le soglie definite budget e costi. I budget e gli avvisi vengono creati per le sottoscrizioni e i gruppi di risorse di Azure, pertanto sono utili come parte di una strategia di monitoraggio generale dei costi. Tuttavia, potrebbero avere funzionalità limitate per gestire i singoli costi del servizio di Azure, ad esempio il costo di Archiviazione di Azure, perché sono progettati per tenere traccia dei costi a un livello superiore.

## <a name="monitor-costs"></a>Monitorare i costi

Quando si usano le risorse di Azure con Archiviazione di Azure, si incorre in costi. I costi unitari di utilizzo delle risorse variano in base agli intervalli di tempo (secondi, minuti, ore e giorni) o in base all'utilizzo dell'unità (byte, megabyte e così via). I costi vengono sostenuti non appena inizia l'utilizzo di Archiviazione di Azure.Costs are subedd as soon usage of Azure Storage starts. È possibile visualizzare i costi nel riquadro di [analisi dei costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md) nel portale di Azure.You can see the costs in the cost analysis pane in the Azure portal.

Quando si usa l'analisi dei costi, è possibile visualizzare i costi di Archiviazione di Azure in grafici e tabelle per intervalli di tempo diversi. Alcuni esempi sono per giorno, mese corrente e precedente e anno. È inoltre possibile visualizzare i costi rispetto ai budget e ai costi previsti. Il passaggio a visualizzazioni più lunghe nel tempo consente di identificare le tendenze di spesa e di vedere dove potrebbe essersi verificata una spesa eccessiva. Se hai creato budget, puoi anche vedere facilmente dove hanno superato.

Per visualizzare i costi di Archiviazione di Azure nell'analisi dei costi:To view Azure Storage costs in cost analysis:

1. Accedere al portale di [Azure](https://portal.azure.com).

2. Aprire la finestra **Gestione costi - Fatturazione,** selezionare **Gestione costi** dal menu, quindi selezionare Analisi **dei costi.** È quindi possibile modificare l'ambito per una sottoscrizione specifica dall'elenco a discesa **Ambito.You** can then change the scope for a specific subscription from the Scope dropdown.

   ![Monitorare i costi con il riquadro Analisi costi](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Per visualizzare solo i costi per Archiviazione di Azure, selezionare **Aggiungi filtro** e quindi **Nome servizio**. Quindi, scegliere **archiviazione** dall'elenco. 

   Ecco un esempio che mostra i costi solo per Archiviazione di Azure:Here's an example showing costs for just Azure Storage:

   ![Monitorare i costi di archiviazione con il riquadro Analisi costi](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Nell'esempio precedente viene visualizzato il costo corrente per il servizio. Vengono visualizzati anche i costi per aree di Azure (località) e per gruppo di risorse.  

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla gestione dei costi con [l'analisi dei costi](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Per altre informazioni su come funziona la determinazione dei prezzi con Archiviazione di Azure:See the following articles to learn more on how pricing works with Azure Storage:

- [Prezzi di Azure Storage Overview](https://azure.microsoft.com/pricing/details/storage/)
- [Ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../blobs/storage-blob-reserved-capacity.md)
