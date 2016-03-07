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
	ms.date="02/18/2016"
	ms.author="tarcher"/>

# Definire i criteri del lab

> [AZURE.NOTE] Fare clic sul collegamento seguente per visualizzare il video associato a questo articolo, relativo alla [procedura di creazione di elementi personalizzati](/documentation/videos/how-to-set-vm-policies-in-a-devtest-lab)

## Panoramica

DevTest Lab consente di specificare i criteri principali che determinano come vengono utilizzati il lab e le relative macchine virtuali. Questi criteri riguardano le soglie dei costi, le dimensioni delle macchine virtuali, il numero massimo di macchine virtuali per ogni utente e una funzione di arresto automatico delle macchine virtuali nel lab.

## Accesso ai criteri del lab

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Nell'elenco dei lab toccare il lab desiderato.

1. Toccare **Impostazioni**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. Nel pannello **Impostazioni** è presente un gruppo di impostazioni denominato **Criteri**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/policies.png)

	Per maggiori informazioni su ciascun criterio, toccare il criterio desiderato nell'elenco seguente:

	- Cost Thresholds - Questo criterio non è attualmente supportato.

	- [Allowed VM Sizes](#set-allowed-vm-sizes) - Selezionare l'elenco delle dimensioni consentite per le macchine virtuali nel lab. Un utente può creare macchine virtuali solo da questo elenco.

	- [Maximum VMs](#set-maximum-vms) - Specificare il numero massimo di macchine virtuali che è possibile creare per un lab e il numero massimo di macchine virtuali che un utente può creare.

	- [Auto shutdown](#set-auto-shutdown) - Specificare a che ora le macchine virtuali del lab corrente devono arrestarsi.

## Impostare le dimensioni consentite per le macchine virtuali

I criteri per l'impostazione delle dimensioni consentite per le macchine virtuali permettono di ridurre al minimo gli sprechi specificando le dimensioni consentite per le macchine virtuali nel lab. Se questi criteri sono attivati, per la creazione di macchine virtuali è possibile utilizzare solo le dimensioni selezionate da questo elenco.

1. Nel pannello **Impostazioni** del lab, in **Criteri**, toccare **Allowed VM Sizes**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se questi criteri sono abilitati, toccare una o più dimensioni per definire quali possono essere utilizzate per creare macchine virtuali nel lab.

1. Toccare **Salva**.

## Impostare il numero massimo di macchine virtuali

I criteri relativi al numero massimo di macchine virtuali consentono di specificare quante macchine virtuali è possibile creare per il lab corrente e quante macchine virtuali un utente può creare. Se un utente tenta di creare una nuova macchina virtuale quando è stato raggiunto il limite dell'utente stesso o del lab, un messaggio di errore indica che non è possibile creare la macchina virtuale.

1. Nel pannello **Impostazioni** del lab, in **Criteri**, toccare **Maximum VMs**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. Nella sezione **Policy per User**:
 
	1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.
	
	1. Se questi criteri sono abilitati, nella casella di testo **Maximum VMs allowed per User** immettere un valore numerico per indicare il numero massimo di macchine virtuali che un utente può creare. Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.

1. Nella sezione **Policy per Lab**:
 
	1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.
	
	1. Se questi criteri sono abilitati, nella casella di testo **Maximum VMs allowed in this lab** immettere un valore numerico per indicare il numero massimo di macchine virtuali che è possibile creare per il lab corrente. Se si immette un numero non valido, l'interfaccia utente visualizza il numero massimo consentito per questo campo.

1. Toccare **Salva**.

## Impostare l'arresto automatico

I criteri di arresto automatico consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora dell'arresto delle macchine virtuali del lab.

1. Nel pannello **Impostazioni** del lab, in **Criteri**, toccare **Auto Shutdown**.

	![Impostazioni](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. Toccare **On** per abilitare i criteri e **Off** per disabilitarli.

1. Se questi criteri sono abilitati, specificare l'ora dell'arresto di tutte le macchine virtuali nel lab corrente.

1. Toccare **Salva**.

<!---HONumber=AcomDC_0224_2016-->