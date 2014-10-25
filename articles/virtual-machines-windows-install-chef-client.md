<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Informazioni su Chef e macchine virtuali di Azure

Chef fornisce un sistema di automazione per compilare, distribuire e gestire l'infrastruttura. Le risorse vengono gestite tramite soluzioni, ovvero definizioni riutilizzabili che forniscono istruzioni per attività quali la configurazione di un server Web.

Chef è un sistema client-server. Per conoscere le opzioni per l'uso di un server Chef, vedere la pagina che descrive come [scegliere l'installazione][scegliere l'installazione]. Per configurare il client, sono necessarie le informazioni sul server Chef. Per installare il client Chef in una macchina virtuale di Azure, sono disponibili le opzioni seguenti:

-   Usare il portale di gestione per installare il client Chef quando si crea una macchina virtuale che esegue Windows Server 2012 o Windows Server 2012 R2. Per le istruzioni, vedere [Portale di Microsoft Azure][Portale di Microsoft Azure].
-   Usare Windows PowerShell per installare il client Chef in una macchina virtuale esistente. Uno [script][script] di esempio è disponibile in Github.
-   Usare un plug-in di Chef, [knife-azure][knife-azure], per creare un'istanza della macchina virtuale e installare il client Chef.

## Risorse aggiuntive

[Chef e Microsoft Azure][Chef e Microsoft Azure]

[Come accedere a una macchina virtuale che esegue Windows Server][Come accedere a una macchina virtuale che esegue Windows Server]

[Come accedere a una macchina virtuale che esegue Linux][Come accedere a una macchina virtuale che esegue Linux]

[Gestire le estensioni][Gestire le estensioni]

<!--Link references-->

  [scegliere l'installazione]: http://www.getchef.com/chef/choose-your-version/
  [Portale di Microsoft Azure]: http://docs.opscode.com/azure_portal.html
  [script]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef e Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Come accedere a una macchina virtuale che esegue Windows Server]: ../virtual-machines-log-on-windows-server/
  [Come accedere a una macchina virtuale che esegue Linux]: ../virtual-machines-linux-how-to-log-on
  [Gestire le estensioni]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
