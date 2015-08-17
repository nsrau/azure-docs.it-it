<properties
	pageTitle="Informazioni su Puppet e sulle macchine virtuali di Azure"
	description="Viene descritta l'installazione e la configurazione di Puppet su una macchina virtuale in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="kathydav"/>

#Informazioni su Puppet e sulle macchine virtuali di Azure


<p>Puppet Enterprise è un software di automazione per compilare, distribuire e gestire l'infrastruttura. Può essere usato per gestire il ciclo di vita dell'infrastruttura IT, tra cui il rilevamento, il provisioning, la gestione della configurazione del sistema operativo e delle applicazioni, l'orchestrazione e la creazione di report.

Puppet è un sistema client-server. Puppet Master e Puppet Enterprise Agent sono entrambi disponibili per l'installazione tramite Microsoft Azure:

- Puppet Master è disponibile come immagine preconfigurata, installata su un server Ubuntu. È anche possibile installare Puppet Enterprise in un server esistente, ma l'immagine rappresenta il modo più semplice per iniziare. Per configurare l'agente, sono necessarie le informazioni sul server.

- Puppet Enterprise Agent è disponibile come estensione della macchina virtuale che può essere installata durante la creazione di una macchina virtuale oppure in una macchina virtuale esistente.

Per istruzioni, scaricare la "Guida introduttiva" disponibile nella pagina [Microsoft Windows e Azure](http://puppetlabs.com/solutions/microsoft).


##Risorse aggiuntive
[Nuove integrazioni con Microsoft Azure e Visual Studio]

[Come accedere a una macchina virtuale che esegue Windows Server]

[Come accedere a una macchina virtuale che esegue Linux]

[Gestione delle estensioni]

<!--Link references-->
[Nuove integrazioni con Microsoft Azure e Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
[Come accedere a una macchina virtuale che esegue Windows Server]: virtual-machines-log-on-windows-server.md
[Come accedere a una macchina virtuale che esegue Linux]: virtual-machines-linux-how-to-log-on.md
[Azure VM extensions and features]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=August15_HO6-->