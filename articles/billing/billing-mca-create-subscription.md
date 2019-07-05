---
title: Creare un'altra sottoscrizione di Azure per l'account di fatturazione
description: Informazioni su come creare una nuova sottoscrizione di Azure nel portale di Azure.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490930"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Creare un'altra sottoscrizione di Azure per il contratto di clienti Microsoft

Creare altre sottoscrizioni per l'account di fatturazione per impostare ambienti distinti per sviluppo e test, sicurezza o per isolare i dati per motivi di conformità.

Questo articolo si applica a un account di fatturazione per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access). Se si desidera creare sottoscrizioni per altri tipi di account di fatturazione, vedere [creare una sottoscrizione aggiuntiva nel portale di Azure](billing-create-subscription.md).

Per creare una sottoscrizione, è necessario essere un **proprietario di sezione fatturazione**, **collaboratore alla sezione della fattura**, o **creatore di sottoscrizione di Azure**. Per altre informazioni, vedere [sottoscrizione fatturazione ruoli e attività](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Per offrire ad altri utenti autorizzati a creare sottoscrizioni di Azure per l'account di fatturazione, vedere [Concedi ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Creare una sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Sottoscrizioni**.

   ![Screenshot che mostra una ricerca nel portale per le sottoscrizioni](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Selezionare **Aggiungi**

4. Se si ha accesso a più account di fatturazione, selezionare l'account di fatturazione per il contratto di Microsoft dal cliente.

   ![Screenshot che mostra creare pagina di sottoscrizione](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selezionare un profilo di fatturazione. Gli addebiti per la sottoscrizione verranno fatturati al profilo di fatturazione selezionato. Se si ha accesso a un solo profilo di fatturazione, la selezione è disabilitata.

6. Selezionare una sezione della fattura. Gli addebiti per la sottoscrizione verranno fatturati a questa sezione della fattura del profilo di fatturazione. Se si ha accesso al solo una sezione della fattura, la selezione è disabilitata.

7. Selezionare un piano per la sottoscrizione. Selezionare **piano di Microsoft Azure per sviluppo/test**, se si prevede di usare questa sottoscrizione per lo sviluppo o test dei carichi di lavoro else usare **piano di Microsoft Azure**. Se si ha accesso a un solo piano, la selezione è disabilitata.

8. Immettere un nome per la sottoscrizione. Il nome aiuta a identificare facilmente la sottoscrizione nel portale di Azure.

9. Selezionare **Create**.

## <a name="give-others-permission"></a>Assegnare ad altri utenti l'autorizzazione

Aggiungere utenti come autori di sottoscrizione di Azure in una sezione della fattura per concedere le autorizzazioni per creare sottoscrizioni di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **costi di gestione e fatturazione**.

   ![Screenshot che mostra una ricerca nel portale per le sottoscrizioni](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Passare alla sezione Fattura. In base all'accesso, si potrebbe essere necessario selezionare un account di fatturazione o di un profilo di fatturazione. L'account di fatturazione o profilo, selezionare **sezioni della fattura** e quindi una sezione della fattura dall'elenco. Tutte le sottoscrizioni che verranno create dagli utenti verranno fatturate a questa sezione della fattura.
   
   ![Screenshot che mostra la selezione di sezioni della fattura](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Selezionare **gestione di accesso (IAM)** dal lato superiore sinistro.

5. Nella parte superiore della pagina selezionare **Aggiungi**.

6. Selezionare **creatore di sottoscrizione di Azure** per ruolo.

7. Immettere l'indirizzo di posta elettronica dell'utente a cui si desidera concedere l'accesso.

8. Selezionare **Salva**.

## <a name="check-access"></a>Verificare l'accesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si hanno domande o assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ad altri utenti autorizzati a creare risorse di Azure usando ruoli predefiniti](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Creare una macchina virtuale windows](../virtual-machines/windows/quick-create-portal.md)
- [Creare una macchina virtuale linux](../virtual-machines/linux/quick-create-portal.md)
- [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
