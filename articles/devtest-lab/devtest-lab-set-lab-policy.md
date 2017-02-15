---
title: Definire i criteri del lab in Azure DevTest Labs| Documentazione Microsoft
description: Informazioni su come definire i criteri del lab, ad esempio per le dimensioni delle macchine virtuali, il numero massimo di macchine virtuali per ogni utente e l&quot;arresto automatico.
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
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4222de44e426c449a42822994b1c15b44c3fec54


---
# <a name="define-lab-policies-in-azure-devtest-labs"></a>Definizione dei criteri di lab in Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs consente di specificare i criteri principali che consentono di controllare i costi e ridurre al minimo gli sprechi nei lab. Questi criteri di lab includono il numero massimo di VM create per utente e per lab e varie opzioni di avvio automatico e chiusura automatica. 

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Accesso ai criteri di lab in Azure DevTest Labs
I passaggi seguenti consentono di impostare i criteri per un lab in Azure DevTest Labs:

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selezionare **Altri servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Selezionare **Impostazioni criteri**.
5. Il pannello **Impostazioni criteri** contiene un menu di impostazioni che è possibile specificare: 
   
    ![Pannello Impostazioni criteri](./media/devtest-lab-set-lab-policy/policies.png)
   
    Per ulteriori informazioni sull'impostazione di un criterio, selezionarlo dall'elenco seguente:
   
   * [Dimensioni consentite per le macchine virtuali](#set-allowed-virtual-machine-sizes) - Selezionare l'elenco delle dimensioni delle VM consentite nel lab. Un utente può creare macchine virtuali solo da questo elenco.
   * [Macchine virtuali per utente](#set-virtual-machines-per-user) - Specificare il numero massimo di VM che possono essere create da un utente. 
   * [Macchine virtuali per lab](#set-virtual-machines-per-lab) - Specificare il numero massimo di VM che possono essere create per un lab. 
   * [Arresto automatico](#set-auto-shutdown) - Specificare il momento in cui le VM del lab corrente si arrestano automaticamente.
   * [Avvio automatico](#set-auto-start) - Specificare il momento in cui le VM del lab corrente si avviano automaticamente.

## <a name="set-allowed-virtual-machine-sizes"></a>Impostazione delle dimensioni consentite per le macchine virtuali
I criteri per l'impostazione delle dimensioni consentite per le macchine virtuali permettono di ridurre al minimo gli sprechi specificando le dimensioni consentite per le macchine virtuali nel lab. Se questi criteri sono attivati, per la creazione di macchine virtuali è possibile utilizzare solo le dimensioni selezionate da questo elenco.

1. Nel pannello **Impostazioni criteri** del lab selezionare **Dimensioni consentite per le macchine virtuali**.
   
    ![Dimensioni consentite per le macchine virtuali](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
3. Se si abilitano questi criteri, selezionare una o più dimensioni per definire quali possono essere usate per creare macchine virtuali nel lab.
4. Selezionare **Salva**.

## <a name="set-virtual-machines-per-user"></a>Impostazione delle macchine virtuali per utente
I criteri per **Macchine virtuali per utente** consentono di specificare il numero massimo di VM che possono essere create da un singolo utente. Se un utente prova a creare una VM quando è stato raggiunto il limite per utente, un messaggio di errore indica che non è possibile creare la VM. 

1. Nel pannello **Impostazioni criteri** del lab selezionare **Macchine virtuali per utente**.
   
    ![Macchine virtuali per utente](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)
2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
3. Se si abilitano questi criteri, immettere un valore numerico per indicare il numero massimo di VM che un utente può creare. 
   Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.
4. Selezionare **Salva**.

## <a name="set-virtual-machines-per-lab"></a>Impostazione delle macchine virtuali per lab
I criteri per **Macchine virtuali per lab** consentono di specificare il numero massimo di VM che è possibile creare per il lab corrente. Se un utente prova a creare una VM quando è stato raggiunto il limite per lab, un messaggio di errore indica che non è possibile creare la VM. 

1. Nel pannello **Impostazioni criteri** del lab selezionare **Macchine virtuali per lab**.
   
    ![Macchine virtuali per lab](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)
2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
3. Se si abilitano questi criteri, immettere un valore numerico per indicare il numero massimo di VM che è possibile creare per il lab corrente. 
   Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.
4. Selezionare **Salva**.

## <a name="set-auto-shutdown"></a>Impostazione dell'arresto automatico
I criteri di arresto automatico consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora dell'arresto delle macchine virtuali del lab.

1. Nel pannello **Impostazioni criteri** del lab selezionare **Arresto automatico**.
   
    ![Arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
3. Se si abilitano questi criteri, specificare l'ora locale per l'arresto di tutte le macchine virtuali nel lab corrente.
4. Selezionare **Salva**.
5. Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una VM specifica, aprire il pannello della VM e modificare l'impostazione **Arresto automatico** . 

## <a name="set-auto-start"></a>Impostazione dell'avvio automatico
I criteri di avvio automatico consentono di specificare l'ora in cui devono essere avviate le macchine virtuali nel lab corrente.  

1. Nel pannello **Impostazioni criteri** del lab selezionare **Avvio automatico**.
   
    ![Avvio automatico](./media/devtest-lab-set-lab-policy/auto-start.png)
2. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
3. Se si abilitano questi criteri, specificare l'ora di inizio locale pianificata e i giorni della settimana a cui è applicabile questo orario. 
4. Selezionare **Salva**.
5. Dopo l'abilitazione, questi criteri non vengono applicati automaticamente alle VM del lab corrente. Per applicare questa impostazione a una VM specifica, aprire il pannello della VM e modificare l'impostazione **Avvio automatico** . 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere definito e applicato i diversi criteri per le VM per il lab, è possibile eseguire le operazioni seguenti:

* [Configurare la gestione dei costi](devtest-lab-configure-cost-management.md): viene illustrato come usare il grafico **Tendenza dei costi mensili stimati**  
  per visualizzare il costo stimato per il mese in corso fino alla data odierna e il costo stimato per la fine del mese.
* [Creare un'immagine personalizzata](devtest-lab-create-template.md): quando si crea una VM, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
* [Configurare immagini del Marketplace](devtest-lab-configure-marketplace-images.md): Azure DevTest Labs supporta la creazione di VM basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di VM in un lab.
* [Creare una VM in un lab](devtest-lab-add-vm-with-artifacts.md): questo articolo illustra come creare una VM da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.




<!--HONumber=Nov16_HO3-->


