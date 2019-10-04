---
title: Configurare le impostazioni di arresto automatico per una macchina virtuale in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare le impostazioni di arresto automatico per una macchina virtuale (VM) in modo che la VM venga arrestata automaticamente quando non è in uso.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: spelluru
ms.openlocfilehash: 934e8fd71c901c89f328c777103a8cb39bf21ac4
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361570"
---
# <a name="configure-autoshutdown-settings-for-a-vm-in-azure-devtest-labs"></a>Configurare le impostazioni di arresto automatico per una macchina virtuale in Azure DevTest Labs
Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. Questo articolo illustra come configurare i criteri di arresto automatico per un account Lab e configurare le impostazioni di arresto automatico per un Lab nell'account Lab. Per vedere come impostare ogni criterio del lab, vedere [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="autoshutdown-policy-for-a-lab"></a>Criteri di arresto automatico per un Lab
I proprietari del lab possono configurare una pianificazione di arresto per tutte le macchine virtuali nel lab. In questo modo, è possibile ridurre i costi dovuti all'esecuzione di macchine virtuali che non vengono usate (inattive). È possibile applicare un criterio di arresto a tutte le macchine virtuali del lab a livello centrale, ma anche evitare agli utenti di dover configurare una pianificazione per i propri computer. Questa funzionalità consente di impostare il criterio per la pianificazione del lab, offrendo agli utenti del lab il livello di controllo desiderato. I proprietari del lab possono configurare questo criterio seguendo questa procedura:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **Criteri di arresto automatico** nella sezione **Pianificazioni** del menu a sinistra.
3. Selezionare una delle opzioni disponibili. Le sezioni seguenti forniscono ulteriori dettagli su queste opzioni: I criteri impostati si applicano solo alle nuove macchine virtuali create nel Lab e non alle macchine virtuali già esistenti. 

    ![Opzioni dei criteri di arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-autoshutdown-settings-for-a-lab"></a>Configurare le impostazioni di arresto automatico per un Lab
I criteri di arresto automatico consentono di ridurre al minimo gli sprechi del Lab consentendo di specificare l'ora di arresto delle macchine virtuali del Lab.

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Selezionare **Configuration and policies** (Configurazione e criteri).

    ![Riquadro Impostazioni criteri](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Nel riquadro **configurazione e criteri** del Lab selezionare **arresto automatico** in pianificazioni.
   
    ![Arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
7. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.
8. Specificare **Sì** o **No** per l'opzione per inviare una notifica 15 minuti prima dell'ora di arresto automatico specificato. Se si sceglie **Sì**, immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per ulteriori informazioni, vedere la sezione [notifiche](#notifications) . 
9. Selezionare **Salva**.

    Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una macchina virtuale specifica, aprire il riquadro di gestione della macchina virtuale e modificare le impostazioni di **arresto** automatico.

## <a name="configure-autoshutdown-settings-for-a-vm"></a>Configurare le impostazioni di arresto automatico per una macchina virtuale

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utente imposta una pianificazione e può rifiutare esplicitamente
Se questa opzione di criteri è impostata per il Lab, gli utenti possono ignorare o rifiutare esplicitamente la pianificazione del Lab. Questa opzione concede agli utenti del lab il controllo completo sulla pianificazione dell'arresto automatico delle macchine virtuali. Gli utenti del lab non notano alcuna modifica nella pagina della pianificazione dell'arresto automatico della macchina virtuale.

![Opzione per l'arresto automatico dei criteri-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utente imposta una pianificazione e non può rifiutare esplicitamente
Se questa opzione di criteri è impostata per il Lab, gli utenti possono eseguire l'override della pianificazione del Lab. Non possono tuttavia rifiutare esplicitamente il criterio di arresto automatico. Questa opzione assicura che ogni computer del lab sia soggetto a una pianificazione di arresto automatico. Gli utenti del lab possono aggiornare la pianificazione dell'arresto automatico delle macchine virtuali e configurare le notifiche di arresto.

![Opzione per l'arresto automatico del criterio-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utente non ha alcun controllo sulla pianificazione impostata dall'amministratore del lab
Se questa opzione di criteri è impostata per il Lab, gli utenti non possono eseguire l'override o rifiutare esplicitamente la pianificazione del Lab. Questa opzione offre all'amministratore del lab il controllo completo sulla pianificazione per ogni computer del lab. Gli utenti del lab possono solo configurare le notifiche di arresto automatico per le macchine virtuali.

![Opzione per l'arresto automatico dei criteri-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notifiche
Al termine dell'arresto automatico da parte del proprietario del Lab, le notifiche verranno inviate agli utenti del Lab 15 minuti prima che venga attivato l'arresto automatico in caso di influire su una delle macchine virtuali. Questa opzione offre agli utenti del Lab la possibilità di salvare il proprio lavoro prima dell'arresto. La notifica fornisce inoltre i collegamenti per ogni macchina virtuale per le azioni seguenti:

- Ignora l'arresto automatico per questa ora
- Arrestare l'AutoShutdown per un'ora o 2 ore, in modo che possano rimanere in funzione della macchina virtuale.

La notifica viene inviata tramite l'endpoint dell'hook Web configurato o un indirizzo di posta elettronica specificato dai proprietari del Lab nelle impostazioni di arresto automatico. I webhook consentono di compilare o configurare integrazioni che sottoscrivono eventi specifici. Quando viene attivato uno di questi eventi, DevTest Labs invierà un payload HTTP POST all'URL configurato del webhook. Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

Si consiglia di usare i webhook perché sono ampiamente supportati da varie app (ad esempio, Slack, app per la logica di Azure e così via) e consente di implementare un metodo personalizzato per l'invio di notifiche. Per un esempio di ricezione della notifica di arresto automatico da messaggi di posta elettronica tramite app per la logica di Azure, vedere[creare un'app per la logica che riceve notifiche tramite posta elettronica](devtest-lab-auto-shutdown.md#create-a-logic-app-that-receives-email-notifications). 


## <a name="next-steps"></a>Passaggi successivi
Vedere [gestire i criteri di arresto automatico per un Lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md)
