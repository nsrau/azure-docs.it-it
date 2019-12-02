---
title: Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure
description: Questo articolo descrive come scaricare o visualizzare il file dei dati quotidiani di utilizzo e addebiti di Azure.
keywords: utilizzo fatturazione, addebiti utilizzo, download utilizzo, visualizzazione utilizzo, fattura di azure, utilizzo di azure
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dcf4e3b9d85909c8f1d149c9d1940a6755b431a1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224019"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Visualizzare e scaricare il file dei dati di utilizzo e addebiti di Azure

È possibile scaricare una scomposizione giornaliera per l'utilizzo e gli addebiti di Azure nel portale di Azure. Solo determinati ruoli sono autorizzati a ottenere informazioni sull'utilizzo di Azure, ad esempio l'amministratore dell'account o l'amministratore dell'organizzazione. Per altre informazioni sull'accesso alle informazioni di fatturazione, vedere [Manage access to Azure billing using roles](billing-manage-access.md) (Gestire l'accesso alla fatturazione di Azure usando i ruoli).

Per visualizzare i dati di utilizzo e addebiti di Azure, un cliente con Contratto del cliente Microsoft deve avere il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione.  Per i clienti con Contratto Microsoft Partner, solo il ruolo di amministratore globale e agente amministratore nell'organizzazione partner Microsoft può visualizzare e scaricare i dati di utilizzo e addebiti di Azure. [Controllare il tipo di account di fatturazione nel portale di Azure](#check-your-billing-account-type).

## <a name="download-usage-from-the-azure-portal-csv"></a>Scaricare il file relativo all'utilizzo dal portale di Azure (CSV)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. A seconda dell'accesso, potrebbe essere necessario selezionare un account di fatturazione o un profilo di fatturazione.
1. Nel menu a sinistra selezionare **Fatture** in **Fatturazione**.
1. Nella griglia della fattura trovare la riga del periodo di fatturazione corrispondente ai dati di utilizzo da scaricare.
1. Fare clic sull'icona del download o sui puntini di sospensione (`...`) a destra.
1. Selezionare **Scarica il file relativo a utilizzo e addebiti di Azure** nel menu di download.

## <a name="download-usage-for-ea-customers"></a>Scaricare informazioni sull'utilizzo per i clienti EA

Per visualizzare e scaricare i dati di utilizzo per i clienti con contratto EA, è necessario essere un amministratore dell'organizzazione, un proprietario dell'account o un amministratore del reparto con i criteri per la visualizzazione degli addebiti abilitati.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare *Gestione dei costi e fatturazione*.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. Selezionare **Utilizzo e addebiti**.
1. Per il mese da scaricare, selezionare **Scarica**.

## <a name="download-usage-for-pending-charges"></a>Scaricare i dati di utilizzo per gli addebiti in sospeso

Se si dispone di un Contratto del cliente Microsoft, è possibile scaricare i dati di utilizzo da inizio mese per il periodo di fatturazione corrente. Questi addebiti per l'utilizzo non sono stati ancora fatturati.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare *Gestione dei costi e fatturazione*.
3. Selezionare un profilo di fatturazione. A seconda dell'accesso, potrebbe essere necessario selezionare prima un account di fatturazione.
4. Nell'area **Panoramica** trovare i collegamenti di download sotto agli addebiti da inizio mese.
5. Selezionare **Utilizzo e addebiti di Azure**.

    ![Screenshot che mostra il download da Panoramica](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>Controllare il tipo di account di fatturazione
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla fattura e sugli addebiti per l'utilizzo, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliato di Microsoft Azure](billing-understand-your-usage.md)
- [Comprendere la fattura per Microsoft Azure](billing-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)

Se si ha un Contratto del cliente Microsoft, vedere:

- [Comprendere i termini sui dati di utilizzo dettagliati di Azure del Contratto del cliente Microsoft](billing-mca-understand-your-usage.md)
- [Comprendere gli addebiti nella fattura del Contratto del cliente Microsoft](billing-mca-understand-your-bill.md)
- [Visualizzare e scaricare la fattura di Microsoft Azure](billing-download-azure-invoice.md)
- [Visualizzare e scaricare i documenti fiscali per il Contratto del cliente Microsoft](billing-mca-download-tax-document.md)
- [Visualizzare e scaricare i prezzi di Azure dell'organizzazione](billing-ea-pricing.md)
