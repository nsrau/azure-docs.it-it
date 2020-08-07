---
title: Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure
description: Informazioni su come scaricare o visualizzare l'utilizzo e gli addebiti giornalieri di Azure e visualizzare altre risorse disponibili.
keywords: utilizzo fatturazione, addebiti utilizzo, download utilizzo, visualizzazione utilizzo, fattura di azure, utilizzo di azure
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: d09f792e784c89e704691975d44cb3cbfa753cf4
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460508"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure

È possibile scaricare una scomposizione giornaliera per l'utilizzo e gli addebiti di Azure nel portale di Azure. Solo determinati ruoli sono autorizzati a ottenere informazioni sull'utilizzo di Azure, ad esempio l'amministratore dell'account o l'amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](../manage/manage-billing-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Per visualizzare i dati di utilizzo e addebiti di Azure, un cliente con Contratto del cliente Microsoft deve avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione.  Per i clienti con Contratto Microsoft Partner, solo il ruolo di amministratore globale e agente amministratore nell'organizzazione partner Microsoft può visualizzare e scaricare i dati di utilizzo e addebiti di Azure. [Controllare il tipo di account di fatturazione nel portale di Azure](#check-your-billing-account-type).

In base al tipo di sottoscrizione usato, le opzioni per scaricare l'utilizzo e gli addebiti possono variare.

## <a name="download-usage-from-the-azure-portal-csv"></a>Scaricare il file relativo all'utilizzo dal portale di Azure (CSV)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.  
    ![Screenshot che mostra una ricerca nel portale di Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione.
1. Nel menu a sinistra selezionare **Fatture** in **Fatturazione**.
1. Nella griglia della fattura trovare la riga del periodo di fatturazione corrispondente ai dati di utilizzo da scaricare.
1. Selezionare l'**icona del download** o i puntini di sospensione (`...`) a destra.  
  ![Screenshot che mostra il percorso di download di utilizzo e addebiti](./media/download-azure-daily-usage/download-usage-others.png)  
1. Il riquadro Download si apre sulla destra. Selezionare **Download** nella sezione **Dettagli utilizzo**.  

## <a name="download-usage-for-ea-customers"></a>Scaricare informazioni sull'utilizzo per i clienti EA

Per visualizzare e scaricare i dati di utilizzo per i clienti con contratto EA, è necessario essere un amministratore dell'organizzazione, un proprietario dell'account o un amministratore del reparto con i criteri per la visualizzazione degli addebiti abilitati.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.  
    ![Screenshot che mostra una ricerca nel portale di Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.  
    ![Screenshot che mostra il percorso di download di utilizzo e addebiti](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Scaricare i dati di utilizzo per gli addebiti in sospeso

Se si dispone di un Contratto del cliente Microsoft, è possibile scaricare i dati di utilizzo da inizio mese per il periodo di fatturazione corrente. Questi addebiti per l'utilizzo non sono stati ancora fatturati.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare *Gestione dei costi e fatturazione*.
3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
4. Nell'area **Panoramica** trovare i collegamenti di download sotto agli addebiti recenti.
5. Selezionare **Scarica l'utilizzo e i prezzi**.  
    :::image type="content" source="./media/download-azure-daily-usage/open-usage01.png" alt-text="Screenshot che mostra il download da Panoramica" lightbox="./media/download-azure-daily-usage/open-usage01.png" :::

## <a name="check-your-billing-account-type"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti per l'utilizzo, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](understand-usage.md)
- [Comprendere la fattura per Microsoft Azure](review-individual-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](download-azure-invoice.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](../manage/ea-pricing.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliati di Azure del Contratto del cliente Microsoft](mca-understand-your-usage.md)
- [Comprendere gli addebiti nella fattura del Contratto del cliente Microsoft](review-customer-agreement-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](download-azure-invoice.md)
- [Visualizzare e scaricare i documenti fiscali per il Contratto del cliente Microsoft](mca-download-tax-document.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](../manage/ea-pricing.md)
