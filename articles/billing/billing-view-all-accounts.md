---
title: Visualizzare gli account di fatturazione nel portale di Azure | Microsoft Docs
description: Informazioni su come visualizzare gli account di fatturazione nel portale di Azure.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490208"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Visualizzare gli account di fatturazione nel portale di Azure  

Quando si effettua l'iscrizione usare Azure, viene creato un account di fatturazione. Si usa l'account di fatturazione per gestire le fatture, pagamenti e tenere traccia dei costi. È possibile accedere a più account di fatturazione. Ad esempio, è possibile che hanno effettuato l'iscrizione per Azure per i progetti personali. Si potrebbe avere anche accesso tramite un contratto Enterprise o contratto di Microsoft dei clienti della propria organizzazione. Per ognuno di questi scenari, è necessario un account di fatturazione separato.

Portale di Azure supporta attualmente i seguenti tipi di account di fatturazione:

- **Programma dei Microsoft Online Services**: Quando si effettua l'iscrizione ad Azure tramite il sito Web di Azure, viene creato un account di fatturazione per un programma Microsoft Online Services. Ad esempio, quando iscrive un [Account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), [account con tariffe a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o come un [sottoscrittore di Visual studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contratto Enterprise**: Un account di fatturazione per un contratto Enterprise viene creato quando l'organizzazione esegue un' [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) per usare Azure.

- **Contratto di Microsoft Customer**: Quando l'organizzazione lavora con un rappresentante Microsoft per firmare un contratto di Microsoft dal cliente, viene creato un account di fatturazione per un contratto di clienti Microsoft. Alcuni clienti in aree selezionate, che effettuano l'iscrizione tramite il sito Web di Azure per un [account con tariffe a consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o l'aggiornamento loro [Account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) potrebbe avere un account di fatturazione per un Microsoft Customer Nonché accordo. Per altre informazioni, vedere [inizia con l'account di fatturazione per Microsoft Customer contratto](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Ambiti per l'account di fatturazione
Un ambito è un nodo all'interno di un account di fatturazione utilizzate dagli utenti per visualizzare e gestire la fatturazione. È in cui gli utenti gestire i dati di fatturazione dei pagamenti, fatture e condurre una gestione degli account generale. 

### <a name="microsoft-online-services-program"></a>Programma dei Microsoft Online Services

|`Scope`  |Definizione  |
|---------|---------|
|Account di fatturazione     | Rappresenta un solo proprietario (amministratore dell'Account) per uno o più sottoscrizioni di Azure. Un amministratore Account è autorizzato a eseguire diverse attività di fatturazione, ad esempio creare le sottoscrizioni, visualizzare le fatture o modificare la fatturazione per le sottoscrizioni.  |
|Sottoscrizione     |  Rappresenta un raggruppamento di risorse di Azure. La fattura viene generata in questo ambito. Ha i propri metodi di pagamento che consentono di pagare la fattura.|


### <a name="enterprise-agreement"></a>Enterprise Agreement

|`Scope`  |Definizione  |
|---------|---------|
|Account di fatturazione    | Rappresenta un'iscrizione con contratto Enterprise. La fattura viene generata in questo ambito. È stata strutturata con i reparti e gli account di registrazione.  |
|department     |  Raggruppamento facoltativo di account di registrazione.      |
|Account di registrazione     |  Rappresenta il proprietario di un singolo account. Le sottoscrizioni di Azure vengono create in questo ambito.  |


### <a name="microsoft-customer-agreement"></a>Contratto del cliente Microsoft

|`Scope`  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un contratto del cliente per più prodotti e servizi Microsoft. È strutturato usando i profili di fatturazione e nelle sezioni della fattura.   |
|Profilo di fatturazione     |  Rappresenta una fattura e i relativi metodi di pagamento. La fattura viene generata in questo ambito. Ciò può avere più sezioni della fattura.      |
|Sezione della fattura     |   Rappresenta un gruppo di costi in fattura. Le sottoscrizioni e gli altri acquisti vengono associati a questo ambito.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>L'ambito di fatturazione nel portale di Azure


1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **costi di gestione e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Selezionare **tutti gli ambiti fatturazione** dal lato sinistro.

   ![Screenshot che mostra tutti gli ambiti di fatturazione](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   \* * Non sarà visibile **tutti gli ambiti fatturazione** se si ha accesso solo a un ambito.

4. Selezionare un ambito per visualizzare i dettagli.



## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come avviare [l'analisi dei costi](../cost-management/quick-acm-cost-analysis.md).