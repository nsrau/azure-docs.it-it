<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Informazioni su Puppet e macchine virtuali di Azure

Puppet Enterprise è un software di automazione per compilare, distribuire e gestire l'infrastruttura. Può essere usata per gestire il ciclo di vita dell'infrastruttura IT e, ad esempio, per il rilevamento, il provisioning, la gestione della configurazione di sistema operativo e applicazioni, l'orchestrazione e la creazione di report.

Puppet è un sistema client-server. Puppet Master e Puppet Enterprise Agent sono entrambi disponibili per l'installazione tramite Azure:

-   Puppet Master è disponibile come immagine preconfigurata, installata in un server Ubuntu. È anche possibile installare Puppet Enterprise in un server esistente, ma l'immagine rappresenta il modo più semplice per iniziare. Per configurare l'agente, sono necessarie le informazioni sul server.
-   Puppet Enterprise Agent è disponibile come estensione della macchina virtuale che può essere installata durante la creazione di una macchina virtuale oppure in una macchina virtuale esistente.

Per le istruzioni, scaricare la Guida introduttiva disponibile nella pagina [Microsoft Windows e Azure][Microsoft Windows e Azure].

## Risorse aggiuntive

[Nuove integrazioni con Microsoft Azure e Visual Studio][Nuove integrazioni con Microsoft Azure e Visual Studio]

[Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server]

[Come accedere a una macchina virtuale che esegue Linux][Come accedere a una macchina virtuale che esegue Linux]

[Gestire le estensioni][Gestire le estensioni]

<!--Link references-->

  [Microsoft Windows e Azure]: http://puppetlabs.com/solutions/microsoft
  [Nuove integrazioni con Microsoft Azure e Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/
  [Come accedere a una macchina virtuale che esegue Linux]: ../virtual-machines-linux-how-to-log-on
  [Gestire le estensioni]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
