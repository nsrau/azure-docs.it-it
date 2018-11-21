---
title: Gestire i criteri di lab in Azure DevTest Labs| Microsoft Docs
description: Informazioni su come definire i criteri del lab, ad esempio per le dimensioni delle macchine virtuali, il numero massimo di macchine virtuali per ogni utente e l'arresto automatico.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: aa0ffbd69e73ddbef72e0eabf79f2736079c3d23
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622024"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gestire tutti i criteri per un lab in Azure DevTest Labs

Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. In questo articolo viene illustrato in modo dettagliato come impostare ogni criterio.  

## <a name="set-allowed-virtual-machine-sizes"></a>Impostazione delle dimensioni consentite per le macchine virtuali
I criteri per l'impostazione delle dimensioni consentite per le macchine virtuali permettono di ridurre al minimo gli sprechi specificando le dimensioni consentite per le macchine virtuali nel lab. Se questi criteri sono attivati, per la creazione di macchine virtuali è possibile utilizzare solo le dimensioni selezionate da questo elenco.

1. Nel [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), selezionare un lab e quindi **Configurazione e criteri**.

    ![Accedere alla configurazione e ai criteri del lab](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Nel riquadro **Configurazione e criteri** del lab selezionare **Allowed virtual machines sizes** (Dimensioni consentite per le macchine virtuali).
   
    ![Dimensioni consentite per le macchine virtuali](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

1. Se si abilitano questi criteri, selezionare una o più dimensioni per definire quali possono essere usate per creare macchine virtuali nel lab.

1. Selezionare **Salva**.

## <a name="set-virtual-machines-per-user"></a>Impostazione delle macchine virtuali per utente
I criteri per **Macchine virtuali per utente** consentono di specificare il numero di macchine virtuali che possono essere create da un singolo utente. Se un utente prova a creare o attestare una macchina virtuale quando è stato raggiunto il limite per utente, un messaggio di errore indica che non è possibile creare o attestare la macchina virtuale. 

1. Dal riquadro **Configurazione e criteri** del lab selezionare **Macchine virtuali per utente**.
   
    ![Macchine virtuali per utente](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selezionare **Sì** per limitare il numero di macchine virtuali per utente. Se non si desidera limitare il numero di macchine virtuali per utente, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero di macchine virtuali che un utente può creare o attestare. 

1. Selezionare **Sì** per limitare il numero di macchine virtuali che possono usare unità SSD. Se non si desidera limitare il numero di macchine virtuali che possono usare unità SSD, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero di macchine virtuali che possono essere create usando unità SSD. 

1. Selezionare **Salva**.

## <a name="set-virtual-machines-per-lab"></a>Impostazione delle macchine virtuali per lab
I criteri per **Macchine virtuali per lab** consentono di specificare il numero di macchine virtuali che è possibile creare per il lab corrente. Se un utente prova a creare una macchina virtuale quando è stato raggiunto il limite per lab, un messaggio di errore indica che non è possibile creare la macchina virtuale. 

1. Dal riquadro **Configurazione e criteri** del lab selezionare **Macchine virtuali per lab**.
   
    ![Macchine virtuali per lab](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selezionare **Sì** per limitare il numero di macchine virtuali per lab. Se non si desidera limitare il numero di macchine virtuali per lab, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero di macchine virtuali che un utente può creare o attestare. 

1. Selezionare **Sì** per limitare il numero di macchine virtuali che possono usare unità SSD. Se non si desidera limitare il numero di macchine virtuali che possono usare unità SSD, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero di macchine virtuali che possono essere create usando unità SSD. 

1. Selezionare **Salva**.

## <a name="set-auto-shutdown"></a>Impostare l'arresto automatico
I criteri di arresto automatico consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora dell'arresto delle macchine virtuali del lab.

1. Nel riquadro **Configurazione e criteri** del lab selezionare **Arresto automatico**.
   
    ![Arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

1. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.

1. Specificare **Sì** o **No** per questa opzione per inviare una notifica 15 minuti prima del momento specificato per l'arresto automatico. Se si sceglie **Sì**, immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di posporre l'arresto.

   Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Selezionare **Salva**.

Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una macchina virtuale specifica, aprire il riquadro di gestione della macchina virtuale e modificare l'impostazione **Arresto automatico**.

## <a name="set-auto-shutdown-policy"></a>Impostare i criteri di arresto automatico
I proprietari del lab possono configurare una pianificazione di arresto per tutte le macchine virtuali nel lab. In questo modo, è possibile ridurre i costi dovuti all'esecuzione di macchine virtuali che non vengono usate (inattive). È possibile applicare un criterio di arresto a tutte le macchine virtuali del lab a livello centrale, ma anche evitare agli utenti di dover configurare una pianificazione per i propri computer. Questa funzionalità consente di impostare il criterio per la pianificazione del lab, offrendo agli utenti del lab il livello di controllo desiderato. I proprietari del lab possono configurare questo criterio seguendo questa procedura:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **Criteri di arresto automatico** nella sezione **Pianificazioni** del menu a sinistra.
3. Selezionare una delle opzioni disponibili. Le sezioni seguenti forniscono altri dettagli su queste opzioni: il criterio impostato si applica solo alle nuove macchine virtuali create nel lab e non alle macchine virtuali già esistenti. 

    ![Opzioni dei criteri di arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utente imposta una pianificazione e può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione concede agli utenti del lab il controllo completo sulla pianificazione dell'arresto automatico delle macchine virtuali. Gli utenti del lab non notano alcuna modifica nella pagina della pianificazione dell'arresto automatico della macchina virtuale.

![Opzioni dei criteri di arresto automatico - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utente imposta una pianificazione e non può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override della pianificazione del lab. Non possono tuttavia rifiutare esplicitamente il criterio di arresto automatico. Questa opzione assicura che ogni computer del lab sia soggetto a una pianificazione di arresto automatico. Gli utenti del lab possono aggiornare la pianificazione dell'arresto automatico delle macchine virtuali e configurare le notifiche di arresto.

![Opzioni dei criteri di arresto automatico - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utente non ha alcun controllo sulla pianificazione impostata dall'amministratore del lab
Se si imposta il lab su questo criterio, gli utenti del lab non possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione offre all'amministratore del lab il controllo completo sulla pianificazione per ogni computer del lab. Gli utenti del lab possono solo configurare le notifiche di arresto automatico per le macchine virtuali.

![Opzioni dei criteri di arresto automatico - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Impostare l'avvio automatico
I criteri di avvio automatico consentono di specificare l'ora in cui devono essere avviate le macchine virtuali nel lab corrente.  

1. Dal riquadro **Configurazione e criteri** del lab selezionare **Avvio automatico**.
   
    ![Avvio automatico](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

3. Se si abilita questo criterio, specificare l'ora di avvio pianificata, il fuso orario e i giorni della settimana a cui è applicabile questo orario. 

4. Selezionare **Salva**.

Dopo l'abilitazione, questi criteri non vengono applicati automaticamente alle VM del lab corrente. Per applicare questa impostazione a una macchina virtuale specifica, aprire il riquadro della macchina virtuale e modificare l'impostazione **Avvio automatico**.

## <a name="set-expiration-date"></a>Impostare la data di scadenza
È possibile impostare una data di scadenza quando si [crea la macchina virtuale](devtest-lab-add-vm.md). In **Impostazioni avanzate** scegliere l'icona del calendario per specificare una data in cui la macchina virtuale verrà eliminata automaticamente. Per impostazione predefinita, la macchina virtuale non scade mai.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere definito e applicato i diversi criteri per le VM per il lab, è possibile eseguire le operazioni seguenti:

* [Understand shared IP addresses ](devtest-lab-shared-ip.md) (Comprendere gli indirizzi IP condivisi) - Illustra come vengono usati gli indirizzi IP condivisi in DevTest Labs per ridurre al minimo il numero di indirizzi IP pubblici necessari per connettersi a macchine virtuali nel lab.
* [Configurare la gestione dei costi](devtest-lab-configure-cost-management.md): viene illustrato come usare il grafico **Tendenza dei costi mensili stimati**  
  per visualizzare il costo stimato per il mese in corso fino alla data odierna e il costo stimato per la fine del mese.
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare immagini del Marketplace](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs supporta la creazione di VM basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

