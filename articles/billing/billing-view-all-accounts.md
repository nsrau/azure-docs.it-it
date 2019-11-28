---
title: Visualizzare gli account di fatturazione nel portale di Azure | Microsoft Docs
description: Informazioni su come visualizzare gli account di fatturazione nel portale di Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ec3236ffba409c390a12e46dd29263b18857c647
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222722"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Visualizzare gli account di fatturazione nel portale di Azure  

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. Si potrebbe anche avere accesso tramite il contratto Enterprise Agreement o il Contratto del cliente Microsoft dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto.

Il portale di Azure attualmente supporta i tipi di account di fatturazione seguenti:

- **Programma dei Microsoft Online Services**: l'account di fatturazione per un programma dei Microsoft Online Services viene creato quando ci si iscrive ad Azure tramite il sito Web di Azure, ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contratto Enterprise**: quando l'organizzazione firma un contratto [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) per l'uso di Azure, viene creato un apposito account di fatturazione.

- **Contratto del cliente Microsoft**: quando l'organizzazione collabora con un rappresentante Microsoft per firmare un Contratto del cliente Microsoft, viene creato un apposito account di fatturazione. Anche alcuni clienti in specifiche aree che si iscrivono tramite il sito Web di Azure per ricevere un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) oppure [aggiornano](https://azure.microsoft.com/offers/ms-azr-0044p/) il loro account Azure gratuito possono avere un account di fatturazione per un Contratto del cliente Microsoft. Per altre informazioni, vedere [Introduzione all'account di fatturazione per il Contratto del cliente Microsoft](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Ambiti degli account di fatturazione
Un ambito è un nodo all'interno di un account di fatturazione che gli utenti usano per visualizzare e gestire la fatturazione. Consente di gestire i dati di fatturazione, i pagamenti, le fatture e l'account in generale. 

### <a name="microsoft-online-services-program"></a>Programma dei Microsoft Online Services

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione     | Rappresenta un singolo proprietario (amministratore account) per una o più sottoscrizioni di Azure. Un amministratore account è autorizzato a eseguire varie attività di fatturazione, ad esempio creare sottoscrizioni, visualizzare fatture o cambiare la fatturazione per le sottoscrizioni.  |
|Subscription     |  Rappresenta un raggruppamento di risorse di Azure. La fattura viene generata in questo ambito. Prevede metodi specifici di pagamento della fattura corrispondente.|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione    | Rappresenta una registrazione del contratto Enterprise Agreement. La fattura viene generata in questo ambito. È strutturato tramite reparti e account di registrazione.  |
|department     |  Raggruppamento facoltativo di account di registrazione.      |
|Account di registrazione     |  Rappresenta un singolo proprietario dell'account. Le sottoscrizioni di Azure vengono create in questo ambito.  |


### <a name="microsoft-customer-agreement"></a>Contratto del cliente Microsoft

|Scope  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un contratto del cliente per più prodotti e servizi Microsoft. È strutturato tramite profili di fatturazione e sezioni della fattura.   |
|Profilo di fatturazione     |  Rappresenta una fattura e i relativi metodi di pagamento. La fattura viene generata in questo ambito. Può includere più sezioni della fattura.      |
|Sezione della fattura     |   Rappresenta un gruppo di costi in una fattura. Le sottoscrizioni e altri acquisti sono associati a questo ambito.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Cambiare ambito di fatturazione nel portale di Azure


1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selezionare **Tutti gli ambiti di fatturazione** sul lato sinistro.

   ![Screenshot che mostra tutti gli ambiti di fatturazione](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Se si ha accesso a un solo ambito, l'opzione **Tutti gli ambiti di fatturazione** non verrà visualizzata.

4. Selezionare un ambito per visualizzare i dettagli.



## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come iniziare ad [analizzare i costi](../cost-management/quick-acm-cost-analysis.md).