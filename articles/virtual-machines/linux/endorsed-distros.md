---
title: Distribuzioni approvate di Linux | Microsoft Docs
description: Informazioni sulle distribuzioni di Linux approvate in Azure, che includono le linee guida per Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 39cb2464eb593a29c4436afb5c14419b704ebff4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux in distribuzioni approvate da Azure
In Azure Marketplace sono disponibili immagini Linux fornite da partner. Microsoft collabora con diverse community Linux per aggiungere altre versioni all'elenco delle distribuzioni approvate. Nel frattempo, per le distribuzioni non disponibili in Marketplace, è possibile portare il proprio sistema Linux attenendosi alle linee guida in [Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="supported-distributions-and-versions"></a>Distribuzioni e versioni supportate
Nella tabella seguente sono elencate le distribuzioni e versioni di Linux supportate in Azure. Per altre informazioni dettagliate, fare riferimento a [Supporto delle immagini Linux in Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892).

I driver di Linux Integration Services (LIS) per Hyper-V e Azure sono moduli kernel forniti da Microsoft direttamente nel kernel Linux upstream.  Per impostazione predefinita, alcuni driver LIS sono integrati nel kernel della distribuzione. Le distribuzioni precedenti basate su Red Hat Enterprise (RHEL)/CentOS sono disponibili come download separato in [Linux Integration Services versione 4.1 per Hyper-V](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Per altre informazioni sui driver LIS, vedere [Requisiti del kernel Linux](create-upload-generic.md#linux-kernel-requirements).

L'agente Linux di Azure è già preinstallato nelle immagini di Azure Marketplace ed è in genere disponibile dal repository del pacchetto della distribuzione. Il codice sorgente è disponibile su [GitHub](https://github.com/azure/walinuxagent).

| Distribuzione | Versione | Driver | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+ |CentOS 6.3: [download LIS](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: nel kernel |Pacchetto: in [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Nel kernel |Codice sorgente: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9 +, 8.2 + |Nel kernel |Pacchetto: in repo sotto "waagent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux. |RHEL 6.7+, 7.1+ |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES per SAP<br>11 SP4<br>12 SP1+|Nel kernel |Pacchetto:<p> per 11 in [Cloud: strumenti](https://build.opensuse.org/project/show/Cloud:Tools) archivio<br>per 12 inclusi nel modulo "Cloud pubblico" in "python-azure-agent"<br/>Codice sorgente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.1+ |Nel kernel |Pacchetto: in repository [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) sotto "python-azure-agent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04, 14.04, 16.04, 16.10 |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Dal sito Web CoreOS:

*CoreOS è progettato per la protezione, l’uniformità e l’affidabilità. Anziché eseguire l'installazione dei pacchetti tramite yum o apt, CoreOS utilizza i contenitori di Linux per gestire i servizi a un livello superiore di astrazione. Il codice di un servizio singolo e tutte le dipendenze vengono inserite in un contenitore che può essere eseguito su uno o più macchine CoreOS.*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ è una società indipendente di servizi e consulenza, specializzata nello sviluppo e nell'implementazione di soluzioni professionali tramite l'utilizzo di software gratuito. Come azienda leader nell'open source, Credativ ha ottenuto un riconoscimento internazionale, con molti reparti IT che ricorrono ai suoi servizi di assistenza. In collaborazione con Microsoft, Credativ sta attualmente preparando immagini Debian corrispondenti per Debian 8 (Jessie) e Debian precedente a 7 (Wheezy). Entrambe le immagini sono appositamente progettate per l'esecuzione in Azure e possono essere facilmente gestite tramite la piattaforma. Credativ supporta anche la manutenzione a lungo termine e l'aggiornamento delle immagini Debian per Azure tramite i centri di supporto open source.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La strategia di Oracle è quella di offrire un ampio portafoglio di soluzioni per cloud pubblici e privati. Ciò offre ai clienti scelta e flessibilità nella modalità distribuzione del software Oracle in cloud Oracle e in altri cloud. La collaborazione di Oracle con Microsoft permette ai clienti di distribuire il software Oracle nei cloud Microsoft pubblici e privati, con la sicurezza offerta dalla certificazione e dal supporto Oracle.  L'impegno e gli investimenti di Oracle in ambito di soluzioni per cloud Oracle pubblico e privato rimangono invariati.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Fornitore mondiale di soluzioni open source, Red Hat aiuta oltre il 90% delle società Fortune 500 a risolvere sfide aziendali, allineare le strategie IT a quelle aziendali e prepararsi per la tecnologia del futuro. Per raggiungere questi obiettivi, Red Hat fornisce soluzioni sicure tramite un modello di business aperto e uno schema di sottoscrizioni conveniente e prevedibile.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server su Azure è una piattaforma collaudata che offre un'affidabilità e una sicurezza superiori per il cloud computing. La piattaforma Linux versatile di SUSE consente l'integrazione lineare con i servizi cloud di Azure per offrire un ambiente cloud facilmente gestibile. Grazie alle oltre 9.200 applicazioni certificate create da oltre 1.800 fornitori indipendenti di software per SUSE Linux Enterprise Server, SUSE è in grado di assicurare che i carichi di lavoro in esecuzione supportati nel data center possano essere distribuiti con sicurezza su Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Le competenze di Canonical in ambito di engineering e le regole di governance della community aperta sono alla base del successo di Ubuntu nelle tecnologie per client, server e cloud computing, inclusi servizi cloud personali per i consumatori. L'obiettivo di Canonical di una piattaforma unificata e gratuita in Ubuntu, dal telefono al cloud, offre una famiglia di interfacce coerenti per telefono, tablet, TV e desktop. Ciò fa di Ubuntu la scelta ideale per istituzioni di vari tipo, dai provider di cloud pubblico ai produttori di elettronica di consumo e la soluzione preferita tra singoli esperti di IT.

Grazie a centri per lo sviluppo e l'engineering in tutto il mondo, Canonical si trova in una posizione privilegiata ed è pertanto in grado di collaborare con produttori di hardware, provider di contenuti e sviluppatori di software, in modo da portare le soluzioni Ubuntu sul mercato, da PC a server e dispositivi palmari.
