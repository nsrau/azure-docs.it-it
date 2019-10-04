---
title: Creare una sottoscrizione di Azure aggiuntiva per l'account di fatturazione
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
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709580"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Creare una sottoscrizione di Azure aggiuntiva per il Contratto del cliente Microsoft

Creare sottoscrizioni aggiuntive per l'account di fatturazione per configurare ambienti distinti per le attività di sviluppo e test e la sicurezza oppure per isolare i dati per motivi di conformità.

Questo articolo di applica a un account di fatturazione per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access). Se si vogliono creare sottoscrizioni per altri tipi di account di fatturazione, vedere [Creare una sottoscrizione aggiuntiva nel portale di Azure](billing-create-subscription.md).

Per creare una sottoscrizione, è necessario essere un **proprietario della sezione della fattura **, un** collaboratore della sezione della fattura** o un **autore di sottoscrizioni di Azure**. Per altre informazioni, vedere [Ruoli e attività di fatturazione della sottoscrizione](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Per concedere ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure per l'account di fatturazione, vedere [Concedere ad altri utenti l'autorizzazione per creare sottoscrizioni di Azure](#give-others-permission).

## <a name="create-a-subscription"></a>Creare una sottoscrizione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Sottoscrizioni**.

   ![Screenshot che mostra la ricerca di sottoscrizioni nel portale](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Selezionare **Aggiungi**

4. Se si ha accesso a più account di fatturazione, selezionare quello relativo al Contratto del cliente Microsoft.

   ![Screenshot che mostra la pagina per creare sottoscrizioni](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Selezionare un profilo di fatturazione. Gli addebiti per la sottoscrizione verranno fatturati al profilo di fatturazione selezionato. Se si ha accesso a un solo profilo di fatturazione, la selezione sarà disattivata.

6. Selezionare una sezione della fattura. Gli addebiti per la sottoscrizione verranno fatturati a questa sezione della fattura del profilo di fatturazione. Se si ha accesso a una sola sezione della fattura, la selezione sarà disattivata.

7. Selezionare un piano per la sottoscrizione. Selezionare **Piano di Microsoft Azure per DevTest** se si intende usare questa sottoscrizione per i carichi di lavoro di sviluppo o test, altrimenti scegliere **Piano di Microsoft Azure**. Se si ha accesso a un solo piano, la selezione sarà disattivata.

8. Immettere un nome per la sottoscrizione. Il nome consente di identificare facilmente la sottoscrizione nel portale di Azure.

9. Selezionare **Create** (Crea).

## <a name="give-others-permission"></a>Concedere ad altri utenti l'autorizzazione

Aggiungere utenti come autori di sottoscrizioni di Azure in una sezione della fattura per concedere loro l'autorizzazione per creare sottoscrizioni di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che mostra la ricerca di sottoscrizioni nel portale](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Passare alla sezione Fattura. A seconda dell'accesso, può essere necessario selezionare un account di fatturazione o un profilo di fatturazione. Dall'account o dal profilo di fatturazione selezionare **Sezioni della fattura** e quindi una sezione della fattura nell'elenco. Tutte le sottoscrizioni che verranno create dagli utenti verranno fatturate a questa sezione della fattura.
   
   ![Screenshot che mostra la selezione di sezioni della fattura](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Selezionare **Gestione delle identità e degli accessi (IAM)** nell'angolo in alto a sinistra.

5. Nella parte superiore della pagina selezionare **Aggiungi**.

6. Selezionare **Autore di sottoscrizioni di Azure** come ruolo.

7. Immettere l'indirizzo di posta elettronica dell'utente a cui si vuole concedere l'accesso.

8. Selezionare **Salva**.

## <a name="check-access"></a>Verificare l'accesso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Concedere ad altri utenti l'autorizzazione per la creazione di risorse di Azure con ruoli predefiniti](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Creare una macchina virtuale Windows](../virtual-machines/windows/quick-create-portal.md)
- [Creare una macchina virtuale Linux](../virtual-machines/linux/quick-create-portal.md)
- [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
