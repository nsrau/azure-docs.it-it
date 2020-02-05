---
title: 'Azure VMware Solutions (AVS): configurare AVS private cloud come sito di ripristino di emergenza usando VMware Site Recovery Manager'
description: Viene descritto come configurare il cloud privato AVS come sito di ripristino di emergenza per i carichi di lavoro VMware locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bbd8a12820432ce2f131dda29af6740a2f04e18
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024722"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurare AVS private cloud come destinazione per il ripristino di emergenza con VMware Site Recovery Manager

È possibile usare il cloud privato AVS come sito per il ripristino di emergenza per i carichi di lavoro VMware locali.

La soluzione di ripristino di emergenza è basata sulla replica vSphere e su VMware Site Recovery Manager (SRM). È possibile seguire un approccio simile per abilitare il cloud privato AVS come sito primario protetto dal sito di ripristino locale.

La soluzione AVS:

* Elimina la necessità di configurare un Data Center in modo specifico per il ripristino di emergenza.
* Consente di sfruttare le località di Azure in cui è distribuito AVS per la resilienza geografica mondiale.
* Offre un'opzione per ridurre i costi di distribuzione e il costo totale di proprietà per stabilire il ripristino di emergenza.

Per la soluzione AVS è necessario eseguire le operazioni seguenti:

* Installare, configurare e gestire la replica vSphere e SRM nel cloud privato AVS.
* Fornire le proprie licenze per SRM quando il cloud privato AVS è il sito protetto. Non sono necessarie licenze SRM aggiuntive per il sito AVS quando viene usato come sito di ripristino.

Con questa soluzione si ha il controllo completo sulla replica vSphere e SRM. Le interfacce dell'interfaccia della riga di comando e dell'interfaccia utente, API e CLI, consentono di usare gli script e gli strumenti esistenti.

![Distribuzione di Site Recovery Manager](media/srm-deployment.png)

È possibile usare tutte le versioni di LRV e SRM compatibili con il cloud privato AVS e gli ambienti locali. Gli esempi in questa guida usano LRV 6,5 e SRM 6,5. Queste versioni sono compatibili con vSphere 6,5, supportato da AVS.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Le sezioni seguenti descrivono come distribuire una soluzione di ripristino di emergenza usando SRM nel cloud privato AVS.

1. [Verificare che le versioni del prodotto VMware siano compatibili](#verify-that-vmware-product-versions-are-compatible)
2. [Stima delle dimensioni dell'ambiente di ripristino di emergenza](#estimate-the-size-of-your-dr-environment)
3. [Creare un cloud privato AVS per l'ambiente](#create-an-avs-private-cloud-for-your-environment)
4. [Configurare AVS private cloud networking per la soluzione SRM](#set-up-avs-private-cloud-networking-for-the-srm-solution)
5. [Configurare una connessione VPN da sito a sito tra la rete locale e il cloud privato AVS e aprire le porte necessarie](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports)
6. [Configurare i servizi di infrastruttura nel cloud privato AVS](#set-up-infrastructure-services-in-your-avs-private-cloud)
7. [Installare l'appliance di replica vSphere nell'ambiente locale](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installare vSphere Replication Appliance nell'ambiente di cloud privato AVS](#install-vsphere-replication-appliance-in-your-avs-private-cloud-environment)
9. [Installare il server SRM nell'ambiente locale](#install-srm-server-in-your-on-premises-environment)
10. [Installare il server SRM nel cloud privato AVS](#install-srm-server-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificare che le versioni del prodotto VMware siano compatibili

Le configurazioni in questa guida sono soggette ai seguenti requisiti di compatibilità:

* La stessa versione di SRM deve essere distribuita nel cloud privato AVS e nell'ambiente locale.
* La stessa versione della replica vSphere deve essere distribuita nel cloud privato AVS e nell'ambiente locale.
* Le versioni di Platform Services controller (PSC) nel cloud privato AVS e nell'ambiente locale devono essere compatibili.
* Le versioni di vCenter nel cloud privato AVS e nell'ambiente locale devono essere compatibili.
* Le versioni di SRM e di replica vSphere devono essere compatibili tra loro e con le versioni di PSC e vCenter.

Per i collegamenti alla documentazione di VMware e alle informazioni di compatibilità pertinenti, vedere la documentazione di [vmware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) .

Per informazioni sulle versioni di vCenter e PSC nel cloud privato AVS, aprire il portale AVS. Passare a **risorse**, selezionare il cloud privato AVS, quindi fare clic sulla scheda **rete di gestione vSphere** .

![versioni di vCenter & PSC in AVS private cloud](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Stima delle dimensioni dell'ambiente di ripristino di emergenza

1. Verificare che la configurazione locale identificata sia entro i limiti supportati. Per SRM 6,5, i limiti sono documentati nell'articolo della Knowledge Base VMware sui [limiti operativi per Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110).
2. Assicurarsi di disporre di larghezza di banda di rete sufficiente per soddisfare le dimensioni del carico di lavoro e i requisiti di RPO. L'articolo della Knowledge Base VMware relativo al [calcolo dei requisiti della larghezza di banda per la replica vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornisce indicazioni sui limiti della larghezza di banda
3. Usare lo strumento AVS Sizer per stimare le risorse necessarie nel sito di ripristino di emergenza per proteggere l'ambiente locale.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Creare un cloud privato AVS per l'ambiente

Per creare un cloud privato AVS dal portale AVS, seguire le istruzioni e le indicazioni sul dimensionamento in [creare un cloud privato AVS](create-private-cloud.md).

### <a name="set-up-avs-private-cloud-networking-for-the-srm-solution"></a>Configurare AVS private cloud networking per la soluzione SRM

Accedere al portale di AVS per configurare la rete AVS private cloud per la soluzione SRM.

Creare una VLAN per la rete della soluzione SRM e assegnarle un CIDR della subnet. Per istruzioni, vedere [creare e gestire VLAN/subnet](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-avs-private-cloud-and-open-required-ports"></a>Configurare una connessione VPN da sito a sito tra la rete locale e il cloud privato AVS e aprire le porte necessarie

Per configurare la connettività da sito a sito tra la rete locale e il cloud privato AVS. Per istruzioni, vedere [configurare una connessione VPN al cloud privato AVS](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-avs-private-cloud"></a>Configurare i servizi di infrastruttura nel cloud privato AVS

È possibile configurare i servizi di infrastruttura nel cloud privato AVS per semplificare la gestione dei carichi di lavoro e degli strumenti.

È possibile aggiungere un provider di identità esterno come descritto in [usare Azure ad come provider di identità per vCenter in AVS private cloud](azure-ad.md) se si vuole eseguire una delle operazioni seguenti:

* Identificare gli utenti dal Active Directory locale (AD) nel cloud privato AVS.
* Configurare un annuncio nel cloud privato AVS per tutti gli utenti.
* Usare Azure AD.

Per fornire servizi di ricerca di indirizzi IP, gestione indirizzi IP e risoluzione dei nomi per i carichi di lavoro nel cloud privato AVS, configurare un server DHCP e DNS come descritto in [configurare le applicazioni e i carichi di lavoro DNS e DHCP nel cloud privato AVS](dns-dhcp-setup.md).

Il dominio *. avs.io viene usato dalle macchine virtuali e dagli host di gestione nel cloud privato AVS. Per risolvere le richieste a questo dominio, configurare l'invio DNS nel server DNS come descritto in [creare un](on-premises-dns-setup.md#create-a-conditional-forwarder)server d'istruzione condizionale.

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installare l'appliance di replica vSphere nell'ambiente locale

Installare vSphere Replication Appliance (LRV) nell'ambiente locale seguendo la documentazione di VMware. L'installazione di è costituita da questa procedura di alto livello:

1. Preparare l'ambiente locale per l'installazione di LRV.

2. Distribuire LRV nell'ambiente locale usando OVF in VR ISO di vmware.com. Per LRV 6,5, [questo Blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contiene le informazioni rilevanti.

3. Registrare il LRV locale con vCenter Single Sign-on nel sito locale. Per istruzioni dettagliate su vSphere Replication 6,5, vedere l'articolo relativo all' [installazione e configurazione di VMware document VMware vSphere replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-avs-private-cloud-environment"></a>Installare vSphere Replication Appliance nell'ambiente di cloud privato AVS

Prima di iniziare, verificare che siano disponibili gli elementi seguenti:

* Raggiungibilità IP dalle subnet nell'ambiente locale alla subnet di gestione del cloud privato AVS
* Raggiungibilità IP dalla subnet di replica nell'ambiente vSphere locale alla subnet della soluzione SRM del cloud privato AVS

Per istruzioni, vedere [configurare una connessione VPN al cloud privato AVS](set-up-vpn.md). I passaggi sono simili a quelli per l'installazione locale.

AVS consiglia di usare i nomi di dominio completi invece degli indirizzi IP durante l'installazione di LRV e SRM. Per conoscere il nome di dominio completo di vCenter e PSC nel cloud privato AVS, aprire il portale AVS. Passare a **risorse**, selezionare il cloud privato AVS, quindi fare clic sulla scheda **rete di gestione vSphere** .

![Individuazione del nome di dominio completo di vCenter/PSC in AVS private cloud](media/srm-resources.png)

AVS richiede l'installazione di LRV e SRM con l'utente ' cloudowner ' predefinito, ma crea invece un nuovo utente. Questa operazione viene eseguita per garantire un tempo di ingrandimento e una disponibilità elevate per un ambiente vCenter cloud privato. Tuttavia, l'utente cloudowner predefinito in AVS private cloud vCenter non dispone di privilegi sufficienti per creare un nuovo utente con privilegi amministrativi.

Prima di installare LRV e SRM, è necessario eseguire l'escalation dei privilegi vCenter dell'utente cloudowner e quindi creare un utente con privilegi amministrativi nel dominio vCenter SSO. Per informazioni dettagliate sull'utente e sul modello di autorizzazione del cloud privato AVS, vedere [informazioni sul modello di autorizzazione AVS private cloud](learn-private-cloud-permissions.md).

L'installazione di è costituita da questa procedura di alto livello:

1. [Escalation dei privilegi](escalate-private-cloud-privileges.md).
2. Creare un utente nel cloud privato AVS per la replica vSphere e l'installazione di SRM. Come illustrato di seguito nell'interfaccia utente di [vCenter: creare un utente nel cloud privato AVS per lrv & l'installazione di SRM](#vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation).
3. Preparare l'ambiente del cloud privato AVS per l'installazione di LRV.
4. Distribuire LRV nel cloud privato AVS usando OVF in VR ISO di vmware.com. Per LRV 6,5, [questo Blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contiene informazioni rilevanti.
5. Configurare le regole del firewall per LRV. Come illustrato di seguito nel [portale AVS: configurare le regole del firewall per LRV](#avs-portal-configure-firewall-rules-for-vra).
6. Registrare AVS private cloud LRV con vCenter Single Sign-on nel sito del cloud privato AVS.
7. Configurare le connessioni di replica vSphere tra le due appliance. Verificare che le porte richieste siano aperte attraverso i firewall. Per un elenco dei numeri di porta che devono essere aperti per la replica vSphere 6,5, vedere [questo articolo della Knowledge base di VMware](https://kb.vmware.com/s/article/2087769) .

Per istruzioni dettagliate sull'installazione di vSphere Replication 6,5, vedere l'articolo relativo all' [installazione e configurazione di VMware document VMware vSphere replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-the-avs-private-cloud-for-vra-and-srm-installation"></a>INTERFACCIA utente di vCenter: creare un utente nel cloud privato AVS per l'installazione di LRV e SRM

Accedere a vCenter usando le credenziali utente di cloudowner dopo l'escalation dei privilegi dal portale AVS.

Creare un nuovo utente, `srm-soln-admin`, in vCenter e aggiungerlo al gruppo Administrators in vCenter.
Disconnettersi da vCenter come utente cloudowner e accedere come utente *SRM-soln-admin* .

#### <a name="avs-portal-configure-firewall-rules-for-vra"></a>Portale AVS: configurare le regole del firewall per LRV

Configurare le regole del firewall come descritto in [configurare le regole e le tabelle del firewall](firewall.md) per aprire le porte per abilitare la comunicazione tra:

* LRV nella rete della soluzione SRM e negli host vCenter e ESXi nella rete di gestione.
* Appliance LRV nei due siti.

Per un elenco dei numeri di porta che devono essere aperti per la replica vSphere 6,5, vedere questo [articolo della Knowledge base di VMware](https://kb.vmware.com/s/article/2087769) .

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installare il server SRM nell'ambiente locale

Prima di iniziare, verificare quanto segue:

* il dispositivo di replica vSphere viene installato negli ambienti locale e cloud privato AVS.
* Le appliance di replica vSphere in entrambi i siti sono connesse tra loro.
* Sono state esaminate le informazioni VMware sui prerequisiti e sulle procedure consigliate. Per SRM 6,5, è possibile fare riferimento ai prerequisiti del documento VMware [e alle procedure consigliate per srm 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Seguire la documentazione di VMware per eseguire l'installazione del server SRM nel modello di distribuzione "topologia a due siti con un'istanza vCenter per controller di servizi della piattaforma", come descritto in questo [documento VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Le istruzioni di installazione per SRM 6,5 sono disponibili nel documento VMware [installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-avs-private-cloud"></a>Installare il server SRM nel cloud privato AVS

Prima di iniziare, verificare quanto segue:

* il dispositivo di replica vSphere viene installato negli ambienti locale e cloud privato AVS.
* Le appliance di replica vSphere in entrambi i siti sono connesse tra loro.
* Sono state esaminate le informazioni VMware sui prerequisiti e sulle procedure consigliate. Per SRM 6,5, è possibile fare riferimento a [prerequisiti e procedure consigliate per l'installazione del Server Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

I passaggi seguenti descrivono l'installazione di AVS private cloud SRM.

1. [interfaccia utente di vCenter: installare SRM](#vcenter-ui-install-srm)
2. [Portale AVS: configurare le regole del firewall per SRM](#avs-portal-configure-firewall-rules-for-srm)
3. [interfaccia utente di vCenter: configurare SRM](#vcenter-ui-configure-srm)
4. [Portale di AVS: decalation dei privilegi](#avs-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>interfaccia utente di vCenter: installare SRM

Dopo aver eseguito la registrazione a vCenter usando le credenziali SRM-soln-admin, seguire la documentazione di VMware per eseguire l'installazione del server SRM nella topologia a due siti del modello di distribuzione con un'istanza di vCenter per controller di servizi della piattaforma, come descritto in questo [documento VMware](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Le istruzioni di installazione per SRM 6,5 sono disponibili nel documento VMware [installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="avs-portal-configure-firewall-rules-for-srm"></a>Portale AVS: configurare le regole del firewall per SRM

Configurare le regole del firewall come descritto in [configurare le regole e le tabelle del firewall](firewall.md) per consentire la comunicazione tra:

Il server SRM e vCenter/PSC nel cloud privato AVS.
Server SRM in entrambi i siti

Per un elenco dei numeri di porta che devono essere aperti per la replica vSphere 6,5, vedere [questo articolo della Knowledge base di VMware](https://kb.vmware.com/s/article/2087769) .

#### <a name="vcenter-ui-configure-srm"></a>interfaccia utente di vCenter: configurare SRM

Dopo aver installato SRM in AVS private cloud, eseguire le operazioni seguenti, come descritto nelle sezioni della Guida all'installazione e alla configurazione di VMware Site Recovery Manager. Per SRM 6,5, le istruzioni sono disponibili nel documento VMware [installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Connettere le istanze del server Site Recovery Manager nei siti protetti e di ripristino.
2. Stabilire una connessione client all'istanza remota del server di Site Recovery Manager.
3. Installare il codice di licenza di Site Recovery Manager.

#### <a name="avs-portal-de-escalate-privileges"></a>Portale di AVS: decalation dei privilegi

Per annullare l'escalation dei privilegi, vedere [decrescenza dei privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Gestione continuativa della soluzione SRM

Si ha il controllo completo sulla replica vSphere e sul software SRM nell'ambiente di cloud privato AVS e si prevede di eseguire la necessaria gestione del ciclo di vita del software. Verificare che tutte le nuove versioni del software siano compatibili con AVS private cloud vCenter e PSC prima di aggiornare o aggiornare vSphere Replication o SRM.

> [!NOTE]
> AVS sta attualmente esplorando le opzioni per l'offerta di un servizio di ripristino di emergenza gestito. 

## <a name="multiple-replication-configuration"></a>Configurazione della replica multipla

 [Sia la replica basata su array sia le tecnologie di replica vSphere possono essere usate contemporaneamente con SRM](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) . Tuttavia, devono essere applicati a un set separato di macchine virtuali (una determinata macchina virtuale può essere protetta tramite la replica basata su array o la replica vSphere, ma non entrambi). Inoltre, il sito AVS può essere configurato come sito di ripristino per più siti protetti. Per informazioni sulle configurazioni multisito, vedere [Opzioni multisito di SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/) .

## <a name="references"></a>Riferimenti

* [Documentazione di VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limiti operativi per Site Recovery Manager 6,5](https://kb.vmware.com/s/article/2147110)
* [Calcolo dei requisiti di larghezza di banda per la replica vSphere](https://kb.vmware.com/s/article/2037268)
* [Opzioni OVF quando si distribuisce vSphere Replication 6,5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [Installazione e configurazione di VMware vSphere Replication 6,5](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Prerequisiti e procedure consigliate per SRM 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Gestione Site Recovery in una topologia con due siti con un'istanza di server vCenter per ogni controller dei servizi della piattaforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Guida all'installazione e alla configurazione di VMware Site Recovery Manager 6,5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog VMware su SRM con replica basata su array e replica vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog VMware sulle opzioni multisito di SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Numeri di porta che devono essere aperti per la replica vSphere 5.8. x, 6. x e 8](https://kb.vmware.com/s/article/2147112)
