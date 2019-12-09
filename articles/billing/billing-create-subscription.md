---
title: Creare una sottoscrizione di Azure aggiuntiva | Microsoft Docs
description: Informazioni su come creare una nuova sottoscrizione di Azure nel portale di Azure.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: bd561f616c9e120a38f1c6b125e349684f82942e
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665611"
---
# <a name="create-an-additional-azure-subscription"></a>Creare una sottoscrizione di Azure aggiuntiva

È possibile creare una sottoscrizione aggiuntiva per l'account di fatturazione del [contratto Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/), [Contratto del cliente Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) o [Contratto Microsoft Partner](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) nel portale di Azure. Una sottoscrizione aggiuntiva può essere utile per evitare di raggiungere i limiti della sottoscrizione, per creare ambienti distinti per finalità di sicurezza o per isolare i dati per motivi di conformità.

Se si dispone di un account di fatturazione del Programma dei Microsoft Online Services, è possibile creare sottoscrizioni aggiuntive nel [portale di iscrizione di Azure](https://account.azure.com/signup?offer=ms-azr-0003p).

Per altre informazioni sugli account di fatturazione e su come identificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Autorizzazioni necessarie per la creazione di sottoscrizioni di Azure

Per creare sottoscrizioni è necessario disporre delle autorizzazioni seguenti:

|Account di fatturazione  |Autorizzazione  |
|---------|---------|
|Contratto Enterprise Agreement (EA) |  Ruolo di proprietario dell'account nella registrazione del Contratto Enterprise. Per altre informazioni, vedere [Informazioni sui ruoli amministrativi per il Contratto Enterprise di Azure](billing-understand-ea-roles.md).    |
|Contratto del cliente Microsoft |  Ruolo proprietario o collaboratore nella sezione fattura, nel profilo di fatturazione o nell'account di fatturazione. In alternativa, il ruolo di autore di sottoscrizioni di Azure nella sezione fattura.  Per altre informazioni, vedere [Ruoli e attività di fatturazione della sottoscrizione](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Contratto Microsoft Partner |   Ruolo amministratore globale e agente amministratore nell'organizzazione partner CSP. Per altre informazioni, vedere [Centro per i partner - Assegnare autorizzazioni e ruoli utente](https://docs.microsoft.com/partner-center/permissions-overview).  L'utente deve eseguire l'accesso al tenant del partner per creare sottoscrizioni di Azure.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Per creare una sottoscrizione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Sottoscrizioni**.

   ![Screenshot che mostra la ricerca di Sottoscrizioni nel portale](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Selezionare **Aggiungi**.

   ![Screenshot che mostra il pulsante Aggiungi nella visualizzazione Sottoscrizioni](./media/billing-create-subscription/subscription-add.png)

1. Se si ha accesso a più account di fatturazione, selezionare quello per cui si vuole creare la sottoscrizione.

1. Compilare il modulo e fare clic su **Crea**. Le tabelle seguenti elencano i campi del modulo per ogni tipo di account di fatturazione.

**Enterprise Agreement**

|Campo  |Definizione  |
|---------|---------|
|NOME     | Nome visualizzato che consente di identificare facilmente la sottoscrizione nel portale di Azure.  |
|Offerta     | Selezionare Sviluppo/test con contratto Enterprise se si intende usare questa sottoscrizione per i carichi di lavoro di sviluppo o test, altrimenti scegliere Microsoft Azure Enterprise. Per creare sottoscrizioni di sviluppo/test con contratto Enterprise, è necessario abilitare l'offerta Sviluppo/test per l'account di registrazione.|

**Contratto del cliente Microsoft**

|Campo  |Definizione  |
|---------|---------|
|Profilo di fatturazione     | Gli addebiti per la sottoscrizione verranno fatturati al profilo di fatturazione selezionato. Se si ha accesso a un solo profilo di fatturazione, la selezione sarà disattivata.     |
|Sezione della fattura     | Gli addebiti per la sottoscrizione verranno visualizzati in questa sezione della fattura del profilo di fatturazione. Se si ha accesso a una sola sezione della fattura, la selezione sarà disattivata.  |
|Pianificazione     | Selezionare Piano di Microsoft Azure per Sviluppo/test se si intende usare questa sottoscrizione per i carichi di lavoro di sviluppo o test, altrimenti scegliere Piano di Microsoft Azure. Se è abilitato solo un piano per il profilo di fatturazione, la selezione sarà disattivata.  |
|NOME     | Nome visualizzato che consente di identificare facilmente la sottoscrizione nel portale di Azure.  |

**Contratto Microsoft Partner**

|Campo  |Definizione  |
|---------|---------|
|Customer    | Viene creata la sottoscrizione per il cliente selezionato. Se è presente solo un cliente, la selezione sarà disattivata.  |
|Rivenditore    | Rivenditore che fornirà i servizi al cliente. Si tratta di un campo facoltativo, applicabile solo ai provider indiretti nel modello a due livelli CSP. |
|NOME     | Nome visualizzato che consente di identificare facilmente la sottoscrizione nel portale di Azure.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Creare una sottoscrizione di Azure aggiuntiva a livello di codice

È anche possibile creare sottoscrizioni aggiuntive a livello di codice. Per altre informazioni, vedere [Creare sottoscrizioni di Azure a livello di codice](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](billing-add-change-azure-subscription-administrator.md)
- [Move resources to new resource group or subscription](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json) (Spostare le risorse a un nuovo gruppo di risorse o a una nuova sottoscrizione)
- [Creare gruppi di gestione per la gestione e l'organizzazione delle risorse](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Annullare la sottoscrizione di Azure](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
