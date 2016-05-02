<properties
	pageTitle="Definire i criteri del lab | Microsoft Azure"
	description="Informazioni su come definire i criteri del lab, ad esempio per le dimensioni delle macchine virtuali, il numero massimo di macchine virtuali per ogni utente e l'arresto automatico."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2016"
	ms.author="tarcher"/>

# Definire i criteri del lab

> [AZURE.NOTE] Fare clic sul collegamento seguente per visualizzare il video associato a questo articolo, relativo alla [procedura di creazione di elementi personalizzati](/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab)

## Panoramica

DevTest Lab consente di specificare i criteri principali che determinano come vengono utilizzati il lab e le relative macchine virtuali. Ad esempio, è possibile configurare le regole per le dimensioni delle VM consentite per la creazione di macchine virtuali, definire la soglia per il numero di VM che è possibile creare e pianificare processi per avviare/arrestare automaticamente le VM del lab.

## Accesso ai criteri del lab

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Toccare **Impostazioni**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Il pannello **Impostazioni** include un gruppo di impostazioni denominato **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/policies.png)

	Per maggiori informazioni su ciascun criterio, toccare il criterio desiderato nell'elenco seguente:

	- [Allowed VM Sizes](#set-allowed-vm-sizes) - Selezionare l'elenco delle dimensioni consentite per le macchine virtuali nel lab. Un utente può creare macchine virtuali solo da questo elenco.

	- [Maximum VMs per user](#set-maximum-vms-per-user) - Specificare il numero massimo di VM che è possibile creare per un lab, oltre al numero massimo di macchine virtuali che possono essere create da un utente.

	- [Total VMs allowed](#set-total-vms-allowed) - Specificare il numero massimo di VM che è possibile creare per un lab, oltre al numero massimo di macchine virtuali che possono essere create da un utente.

	- [Auto shutdown](#set-auto-shutdown) - Specificare a che ora le macchine virtuali del lab corrente devono arrestarsi.

	- [Auto start](#set-auto-start) - Specificare l'ora in cui le macchine virtuali del lab corrente devono arrestarsi.

## Impostare le dimensioni consentite per le macchine virtuali

I criteri per l'impostazione delle dimensioni consentite per le macchine virtuali permettono di ridurre al minimo gli sprechi specificando le dimensioni consentite per le macchine virtuali nel lab. Se questi criteri sono attivati, per la creazione di macchine virtuali è possibile utilizzare solo le dimensioni selezionate da questo elenco.

1. Nel pannello **Impostazioni** del lab toccare **Allowed VM Sizes** in **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
 
1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se si abilitano questi criteri, toccare una o più dimensioni per definire quali possono essere usate per creare macchine virtuali nel lab.

1. Toccare **Salva**.

## Impostare il numero massimo di macchine virtuali per utente

I criteri per **Maximum VMs per user** consentono di specificare il numero massimo di VM che possono essere create da un singolo utente. Se un utente prova a creare una nuova VM quando è stato raggiunto il limite per utente, un messaggio di errore indicherà che non è possibile creare la macchina virtuale.

1. Nel pannello **Impostazioni** del lab toccare **Maximum VMs per user** in **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se si abilitano questi criteri, nella casella di testo **Maximum VMs allowed per User** immettere un valore numerico per indicare il numero massimo di macchine virtuali che un utente può creare. Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.

1. Toccare **Salva**.

## Impostare il numero totale di macchine virtuali consentite

I criteri per **Total VMs allowed** consentono di specificare il numero massimo di macchine virtuali che è possibile creare per il lab corrente. Se un utente prova a creare una nuova VM quando è stato raggiunto il limite per il lab, un messaggio di errore indicherà che non è possibile creare la macchina virtuale.

1. Nel pannello **Impostazioni** del lab toccare **Total VMs allowed** in **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)

1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se si abilitano questi criteri, nella casella di testo **Total VMs allowed in this lab** immettere un valore numerico per indicare il numero massimo di macchine virtuali che è possibile creare per il lab corrente. Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.

1. Toccare **Salva**.

## Impostare l'arresto automatico

I criteri di arresto automatico consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora dell'arresto delle macchine virtuali del lab.

1. Nel pannello **Impostazioni** del lab toccare **Auto shutdown** in **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se si abilitano questi criteri, specificare l'ora locale per l'arresto di tutte le macchine virtuali nel lab corrente.

1. Toccare **Salva**.

1. Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una VM specifica, aprire il pannello della macchina virtuale e modificare la rispettiva impostazione **Auto Shutdown**.

## Configurare l'avvio automatico

I criteri di avvio automatico consentono di specificare l'ora in cui devono essere avviate le macchine virtuali nel lab corrente.

1. Nel pannello **Impostazioni** del lab toccare **Auto start** in **VM Policies**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/auto-start.png)

1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se si abilitano questi criteri, specificare l'ora di inizio locale pianificata e i giorni della settimana a cui è applicabile questo orario.

1. Toccare **Salva**.

1. Dopo l'abilitazione, questi criteri non vengono applicati automaticamente alle VM del lab corrente. Per applicare questa impostazione a una macchina virtuale specifica, aprire il pannello della VM e modificare la rispettiva impostazione **Auto Start**.

## Passaggi successivi

Dopo avere definito e applicato i diversi criteri per le VM per il lab, è possibile eseguire le operazioni seguenti:

- [Creare un'immagine personalizzata](./devtest-lab-create-template.md) - Quando si crea una macchina virtuale, si specifica una base, che può essere un'immagine personalizzata o un'immagine del Marketplace. Questo articolo illustra come creare un'immagine personalizzata da un file VHD.
- [Configurare immagini del Marketplace](./devtest-lab-configure-marketplace-images.md) - Lab di sviluppo/test supporta la creazione di nuove macchine virtuali basate su immagini di Azure Marketplace. Questo articolo illustra come specificare eventuali immagini di Azure Marketplace da usare durante la creazione di nuove macchine virtuali in un lab.
- [Creare una VM in un lab](./devtest-lab-add-vm-with-artifacts.md) - Questo articolo illustra come creare una nuova macchina virtuale da un'immagine di base, personalizzata o del Marketplace, e come usare gli elementi nella VM.

<!---HONumber=AcomDC_0420_2016-->