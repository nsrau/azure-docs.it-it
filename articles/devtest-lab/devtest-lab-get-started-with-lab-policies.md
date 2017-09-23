---
title: Gestire i criteri di base di un lab in Azure DevTest Labs| Microsoft Docs
description: Come configurare alcuni dei criteri (impostazioni) di base di un lab in DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: ed35d081b191ec41ed9e5970515057a4715c0d59
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---

# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Gestire i criteri di base di un lab in Azure DevTest Labs

Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. Questo articolo è una guida introduttiva ai criteri per configurare due dei più importanti criteri: la limitazione del numero di macchine virtuali che un singolo utente può creare o attestare e la configurazione di arresto automatico. Per vedere come impostare ogni criterio del lab, fare riferimento all'articolo [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Accesso ai criteri di lab in Azure DevTest Labs
I passaggi seguenti consentono di impostare i criteri per un lab in Azure DevTest Labs:

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.   

1. Selezionare **Configuration and policies** (Configurazione e criteri).

    ![Pannello Impostazioni criteri](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Il pannello **Configuration and policies** (Configurazione e criteri) contiene un menu di impostazioni che è possibile specificare. In questo articolo vengono illustrate solo le impostazioni per **Virtual machines per user** (Macchine virtuali per utente) e **Arresto automatico**. Per maggiori informazioni sulle altre impostazioni, vedere [Gestire tutti i criteri per un lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Impostazione delle macchine virtuali per utente
I criteri per **Macchine virtuali per utente** consentono di specificare il numero massimo di VM che possono essere create da un singolo utente. Se un utente prova a creare o attestare una macchina virtuale quando è stato raggiunto il limite per utente, un messaggio di errore indica che non è possibile creare o attestare la macchina virtuale. 

1. Dal menu **Configuration and policies** (Configurazione e criteri) del lab selezionare **Virtual machines per user** (Macchine virtuali per utente).
   
    ![Macchine virtuali per utente](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selezionare **Sì** per limitare il numero di macchine virtuali per utente. Se non si desidera limitare il numero di macchine virtuali per utente, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero massimo di macchine virtuali che un utente può creare o attestare. 

1. Selezionare **Sì** per limitare il numero di macchine virtuali che possono usare unità SSD. Se non si desidera limitare il numero di macchine virtuali che possono usare unità SSD, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero massimo di macchine virtuali che possono essere create usando unità SSD. 

1. Selezionare **Salva**.

## <a name="set-auto-shutdown"></a>Impostazione dell'arresto automatico
I criteri di arresto automatico consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora dell'arresto delle macchine virtuali del lab.

1. Nel pannello **Configuration and policies** (Configurazione e criteri) del lab selezionare **Arresto automatico**.
   
    ![Arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

1. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.

1. Specificare **Sì** o **No** per questa opzione per inviare una notifica 15 minuti prima del momento specificato per l'arresto automatico. Se si specifica **Sì**, immettere un endpoint dell'URL webhook per ricevere la notifica. Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selezionare **Salva**.

    Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una VM specifica, aprire il pannello della VM e modificare l'impostazione **Arresto automatico** . 

## <a name="set-auto-start"></a>Impostazione dell'avvio automatico
I criteri di avvio automatico consentono di specificare l'ora in cui devono essere avviate le macchine virtuali nel lab corrente.  

1. Dal pannello **Configuration and policies** (Configurazione e criteri) del lab selezionare **Avvio automatico**.
   
    ![Avvio automatico](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

3. Se si abilita questo criterio, specificare l'ora di avvio pianificata, il fuso orario e i giorni della settimana a cui è applicabile questo orario. 

4. Selezionare **Salva**.

    Dopo l'abilitazione, questi criteri non vengono applicati automaticamente alle VM del lab corrente. Per applicare questa impostazione a una VM specifica, aprire il pannello della VM e modificare l'impostazione **Avvio automatico** . 

## <a name="next-steps"></a>Passaggi successivi

- [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md) - Informazioni su come modificare altri criteri di lab 

