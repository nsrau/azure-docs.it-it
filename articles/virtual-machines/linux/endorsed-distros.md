---
title: Distribuzioni di Linux approvate in Azure
description: Informazioni sulle distribuzioni di Linux approvate in Azure, che includono le linee guida per Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: fd21170c4edc1ed0587ea4d4e067e61590530623
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283317"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuzioni di Linux approvate in Azure

In Azure Marketplace sono disponibili immagini Linux fornite da partner. Microsoft collabora con diverse community Linux per aggiungere altre versioni all'elenco di distribuzioni approvate. Per le distribuzioni che non sono disponibili dal Marketplace, è possibile portare il proprio Linux seguendo le linee guida disponibili in [creare e caricare un disco rigido virtuale che contiene il sistema operativo Linux](./create-upload-generic.md).

## <a name="supported-distributions-and-versions"></a>Distribuzioni e versioni supportate

Nella tabella seguente sono elencate le distribuzioni e versioni di Linux supportate in Azure. Per informazioni più dettagliate sul supporto per Linux e la tecnologia open source in Azure, vedere [supporto per immagini Linux in Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .

I driver di Linux Integration Services (LIS) per Hyper-V e Azure sono moduli kernel forniti da Microsoft direttamente nel kernel Linux upstream. Per impostazione predefinita, alcuni driver LIS sono integrati nel kernel della distribuzione. Le distribuzioni precedenti basate su Red Hat Enterprise (RHEL)/CentOS sono disponibili come download separato in [Linux Integration Services versione 4.2 per Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106). Per altre informazioni sui driver LIS, vedere [Requisiti del kernel Linux](create-upload-generic.md#linux-kernel-requirements).

L'agente Linux di Azure è già preinstallato nelle immagini di Azure Marketplace ed è in genere disponibile dal repository del pacchetto della distribuzione. Il codice sorgente è disponibile su [GitHub](https://github.com/azure/walinuxagent).

| Distribuzione | Versione | Driver | Agente |
| --- | --- | --- | --- |
| CentOS di software Wave Rogue |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [download LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: nel kernel |Pacchetto: in [repository](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) in "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS è ora la [fine del ciclo di vita](https://coreos.com/os/eol/) a partire dal 26 maggio 2020. |Non più disponibile | | |
| Debian da Credativ |8.x, 9.x |Nel kernel |Pacchetto: in repo sotto "waagent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Pianale container Linux di Kinvolk| Stable, Edge| | |
| Oracle Linux da Oracle |6.x, 7.x, 8.x |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux da Red Hat |6.x, 7.x, 8.x |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise di SUSE |SLES/SLES per SAP 11. x, 12. x, 15. x <br/> [Ciclo di vita delle immagini del cloud pubblico SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |Nel kernel |Pacchetto:<p> per 11 in [Cloud: strumenti](https://build.opensuse.org/project/show/Cloud:Tools) archivio<br>per 12 inclusi nel modulo "Cloud pubblico" in "python-azure-agent"<br/>Codice sorgente: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE di SUSE |openSUSE Leap 15.x |Nel kernel |Pacchetto: in repository [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) sotto "python-azure-agent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu by Canonical |Ubuntu Server e Pro. 16. x, 18. x, 20. x<p>Per informazioni sul supporto esteso per Ubuntu 12,04 e 14,04, vedere la pagina relativa alla [manutenzione estesa della sicurezza di Ubuntu](https://www.ubuntu.com/esm). |Nel kernel |Pacchetto: in repo sotto "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Cadenza aggiornamento immagine

Azure richiede che gli autori delle distribuzioni di Linux approvate aggiornino regolarmente le proprie immagini in Azure Marketplace con le patch e le correzioni di sicurezza più recenti, con una cadenza trimestrale o più rapida. Le immagini aggiornate in Azure Marketplace sono disponibili automaticamente ai clienti come nuove versioni di uno SKU di immagine. Altre informazioni su come trovare immagini Linux: [trovare immagini di VM Linux in Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Kernel ottimizzati per Azure

Azure collabora a stretto contatto con varie distribuzioni di Linux approvate per ottimizzare le immagini pubblicate in Azure Marketplace. Un aspetto di questa collaborazione è lo sviluppo di kernel Linux "ottimizzati" ottimizzati per la piattaforma Azure e distribuiti come componenti completamente supportati della distribuzione Linux. I kernel ottimizzati per Azure incorporano nuove funzionalità e miglioramenti delle prestazioni e a una cadenza più rapida (in genere trimestrale) rispetto ai kernel predefiniti o generici disponibili nella distribuzione.

Nella maggior parte dei casi, questi kernel sono preinstallati nelle immagini predefinite in Azure Marketplace, in modo che i clienti possano ottenere immediatamente il vantaggio di questi kernel ottimizzati. Altre informazioni su questi kernel ottimizzati per Azure sono disponibili nei collegamenti seguenti:

- [Kernel di CentOS ottimizzato per Azure-disponibile tramite la virtualizzazione CentOS SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Kernel del cloud Debian-disponibile con l'immagine "backports" di Debian 10 e Debian 9 in Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [Kernel ottimizzato per SLES Azure](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Kernel di Ubuntu ottimizzato per Azure](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)

## <a name="partners"></a>Partner

### <a name="coreos"></a>CoreOS

Il 26 maggio 2020, coreos è stato pianificato per [la scadenza](https://coreos.com/os/eol/) .
Microsoft dispone di due (2) canali di migrazione per gli utenti di CoreOS.

- Pianale by Kinvolk (vedere la voce "pianale container Linux by Kinvolk").
- [Sistema operativo Fedora Core](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (i clienti devono caricare la propria immagine. Ecco la documentazione per la [migrazione](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/).

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ è una società indipendente di servizi e consulenza, specializzata nello sviluppo e nell'implementazione di soluzioni professionali tramite l'utilizzo di software gratuito. Come azienda leader nell'open source, Credativ ha ottenuto un riconoscimento internazionale, con molti reparti IT che ricorrono ai suoi servizi di assistenza. In collaborazione con Microsoft, Credativ sta attualmente preparando immagini Debian corrispondenti per Debian 8 (Jessie) e Debian precedente a 7 (Wheezy). Entrambe le immagini sono appositamente progettate per l'esecuzione in Azure e possono essere facilmente gestite tramite la piattaforma. Credativ supporta anche la manutenzione a lungo termine e l'aggiornamento delle immagini Debian per Azure tramite i centri di supporto open source.

### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La strategia di Oracle è offrire un'ampia gamma di soluzioni per cloud pubblici e privati. Ciò offre ai clienti scelta e flessibilità nella modalità distribuzione del software Oracle in cloud Oracle e in altri cloud. La partnership di Oracle con Microsoft consente ai clienti di distribuire il software Oracle in cloud pubblici e privati Microsoft con la certezza della certificazione e del supporto di Oracle.  L'impegno e l'investimento di Oracle nelle soluzioni di cloud pubblico e privato Oracle sono invariati.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Il fornitore leader del mondo di soluzioni open source, Red Hat aiuta più del 90% delle società Fortune 500 a risolvere le esigenze aziendali, a allineare le strategie IT e aziendali e a prepararsi per il futuro della tecnologia. Per raggiungere questi obiettivi, Red Hat fornisce soluzioni sicure tramite un modello di business aperto e uno schema di sottoscrizioni conveniente e prevedibile.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server su Azure è una piattaforma collaudata che offre un'affidabilità e una sicurezza superiori per il cloud computing. La piattaforma Linux versatile di SUSE consente l'integrazione lineare con i servizi cloud di Azure per offrire un ambiente cloud facilmente gestibile. Grazie alle oltre 9.200 applicazioni certificate create da oltre 1.800 fornitori indipendenti di software per SUSE Linux Enterprise Server, SUSE è in grado di assicurare che i carichi di lavoro in esecuzione supportati nel data center possano essere distribuiti con sicurezza su Azure.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Le competenze di Canonical in ambito di engineering e le regole di governance della community aperta sono alla base del successo di Ubuntu nelle tecnologie per client, server e cloud computing, inclusi servizi cloud personali per i consumatori. L'obiettivo di Canonical di una piattaforma unificata e gratuita in Ubuntu, dal telefono al cloud, offre una famiglia di interfacce coerenti per telefono, tablet, TV e desktop. Ciò fa di Ubuntu la scelta ideale per istituzioni di vari tipo, dai provider di cloud pubblico ai produttori di elettronica di consumo e la soluzione preferita tra singoli esperti di IT.

Grazie a centri per lo sviluppo e l'engineering in tutto il mondo, Canonical si trova in una posizione privilegiata ed è pertanto in grado di collaborare con produttori di hardware, provider di contenuti e sviluppatori di software, in modo da portare le soluzioni Ubuntu sul mercato, da PC a server e dispositivi palmari.
