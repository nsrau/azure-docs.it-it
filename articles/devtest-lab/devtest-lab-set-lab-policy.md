---
title: Gestire i criteri di lab in Azure DevTest Labs| Microsoft Docs
description: Informazioni su come definire i criteri del lab, ad esempio per le dimensioni delle macchine virtuali, il numero massimo di macchine virtuali per ogni utente e l'arresto automatico.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 328a4d893637d7150807855e118b485a2c3bbfc5
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Gestire tutti i criteri per un lab in Azure DevTest Labs

Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. In questo articolo viene illustrato in modo dettagliato come impostare ogni criterio.  

## <a name="set-allowed-virtual-machine-sizes"></a>Impostazione delle dimensioni consentite per le macchine virtuali
I criteri per l'impostazione delle dimensioni consentite per le macchine virtuali permettono di ridurre al minimo gli sprechi specificando le dimensioni consentite per le macchine virtuali nel lab. Se questi criteri sono attivati, per la creazione di macchine virtuali è possibile utilizzare solo le dimensioni selezionate da questo elenco.

1. Nel pannello **Configuration and policies** (Configurazione e criteri) del lab selezionare **Allowed virtual machines sizes** (Dimensioni consentite per le macchine virtuali).
   
    ![Dimensioni consentite per le macchine virtuali](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.

1. Se si abilitano questi criteri, selezionare una o più dimensioni per definire quali possono essere usate per creare macchine virtuali nel lab.

1. Selezionare **Salva**.

## <a name="set-virtual-machines-per-user"></a>Impostazione delle macchine virtuali per utente
I criteri per **Macchine virtuali per utente** consentono di specificare il numero massimo di VM che possono essere create da un singolo utente. Se un utente prova a creare o attestare una macchina virtuale quando è stato raggiunto il limite per utente, un messaggio di errore indica che non è possibile creare o attestare la macchina virtuale. 

1. Dal menu **Configuration and policies** (Configurazione e criteri) del lab selezionare **Virtual machines per user** (Macchine virtuali per utente).
   
    ![Macchine virtuali per utente](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Selezionare **Sì** per limitare il numero di macchine virtuali per utente. Se non si desidera limitare il numero di macchine virtuali per utente, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero massimo di macchine virtuali che un utente può creare o attestare. 

1. Selezionare **Sì** per limitare il numero di macchine virtuali che possono usare unità SSD. Se non si desidera limitare il numero di macchine virtuali che possono usare unità SSD, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero massimo di macchine virtuali che possono essere create usando unità SSD. 

1. Selezionare **Salva**.

## <a name="set-virtual-machines-per-lab"></a>Impostazione delle macchine virtuali per lab
I criteri per **Macchine virtuali per lab** consentono di specificare il numero massimo di VM che è possibile creare per il lab corrente. Se un utente prova a creare una VM quando è stato raggiunto il limite per lab, un messaggio di errore indica che non è possibile creare la VM. 

1. Dal menu **Configuration and policies** del lab selezionare **Virtual machines per lab** (Macchine virtuali per lab).
   
    ![Macchine virtuali per lab](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Selezionare **Sì** per limitare il numero di macchine virtuali per lab. Se non si desidera limitare il numero di macchine virtuali per lab, selezionare **No**. Se si seleziona **Sì**, immettere un valore numerico per indicare il numero massimo di macchine virtuali che un utente può creare o attestare. 

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

## <a name="set-expiration-date"></a>Impostare la data di scadenza
È possibile impostare una data di scadenza quando si [crea la macchina virtuale](devtest-lab-add-vm.md). In **Impostazioni avanzate** scegliere l'icona del calendario per specificare una data in cui la macchina virtuale verrà eliminata automaticamente.  Per impostazione predefinita, la macchina virtuale non scadrà.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere definito e applicato i diversi criteri per le VM per il lab, è possibile eseguire le operazioni seguenti:

* [Understand shared IP addresses ](devtest-lab-shared-ip.md) (Comprendere gli indirizzi IP condivisi) - Illustra come vengono usati gli indirizzi IP condivisi in DevTest Labs per ridurre al minimo il numero di indirizzi IP pubblici necessari per connettersi a macchine virtuali nel lab.
* [Configurare la gestione dei costi](devtest-lab-configure-cost-management.md): viene illustrato come usare il grafico **Tendenza dei costi mensili stimati**  
  per visualizzare il costo stimato per il mese in corso fino alla data odierna e il costo stimato per la fine del mese.
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare immagini del Marketplace](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs supporta la creazione di VM basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm-with-artifacts.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.


