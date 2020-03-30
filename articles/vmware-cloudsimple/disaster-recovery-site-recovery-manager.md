---
title: Soluzione Azure VMware di CloudSimple - Configurare il cloud privato come sito di ripristino di emergenza tramite VMware Site Recovery Manager
description: Descrive come configurare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa8b2da683d68a337df38e13726f22c5af43540a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565928"
---
# <a name="set-up-private-cloud-as-a-disaster-recovery-target-with-vmware-site-recovery-manager"></a>Configurare il cloud privato come destinazione di ripristino di emergenza con VMware Site Recovery Manager

È possibile usare il cloud privato CloudSimple come sito di ripristino di emergenza per i carichi di lavoro VMware locali.

La soluzione di ripristino di emergenza è basata su vSphere Replication e VMware Site Recovery Manager (SRM). È possibile seguire un approccio simile per abilitare il cloud privato come sito primario protetto dal sito di ripristino locale.

La soluzione CloudSimple:

* Elimina la necessità di configurare un data center specifico per il ripristino di emergenza.
* Consente di sfruttare le posizioni di Azure in cui CloudSimple viene distribuito per la resilienza geografica in tutto il mondo.
* Offre un'opzione per ridurre i costi di distribuzione e il costo totale di proprietà per la creazione di ripristino di emergenza.

La soluzione CloudSimple richiede di eseguire le operazioni seguenti:The CloudSimple solution requires you to do the following:

* Installare, configurare e gestire vSphere Replication e SRM nel cloud privato.
* Fornire le proprie licenze per SRM quando il cloud privato è il sito protetto. Non sono necessarie licenze SRM aggiuntive per il sito CloudSimple quando viene utilizzato come sito di ripristino.

Con questa soluzione, si ha il controllo completo sulla replica vSphere e SRM. Le interfacce familiari dell'interfaccia utente, dell'API e dell'interfaccia della riga di comando consentono l'uso degli script e degli strumenti esistenti.

![Distribuzione di Site Recovery Manager](media/srm-deployment.png)

È possibile utilizzare tutte le versioni di vRA e SRM compatibili con il cloud privato e gli ambienti locali. Gli esempi in questa guida usano vRA 6.5 e SRM 6.5. Queste versioni sono compatibili con vSphere 6.5, supportato da CloudSimple.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Nelle sezioni seguenti viene descritto come distribuire una soluzione di ripristino di emergenza usando SRM nel cloud privato.

1. [Verificare che le versioni del prodotto VMware siano compatibili](#verify-that-vmware-product-versions-are-compatible)
2. [Stimare le dimensioni dell'ambiente di ripristino di emergenza](#estimate-the-size-of-your-dr-environment)
3. [Creare un cloud privato per l'ambienteCreate a Private Cloud for your environment](#create-a-private-cloud-for-your-environment)
4. [Configurare la rete cloud privata per la soluzione SRM](#set-up-private-cloud-networking-for-the-srm-solution)
5. [Configurare una connessione VPN da sito a sito tra la rete locale e il cloud privato e aprire le porte necessarie](#set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports)
6. [Configurare i servizi di infrastruttura nel cloud privato](#set-up-infrastructure-services-in-your-private-cloud)
7. [Installare l'appliance di replica vSphere nell'ambiente localeInstall vSphere Replication appliance in your on-premises environment](#install-vsphere-replication-appliance-in-your-on-premises-environment)
8. [Installare l'appliance vSphere Replication nell'ambiente Private CloudInstall vSphere Replication appliance in your Private Cloud environment](#install-vsphere-replication-appliance-in-your-private-cloud-environment)
9. [Installare il server SRM nell'ambiente locale](#install-srm-server-in-your-on-premises-environment)
10. [Installare il server SRM nel cloud privato](#install-srm-server-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificare che le versioni del prodotto VMware siano compatibili

Le configurazioni in questa guida sono soggette ai seguenti requisiti di compatibilità:

* La stessa versione di SRM deve essere distribuita nel cloud privato e nell'ambiente locale.
* La stessa versione di vSphere Replication deve essere distribuita nel cloud privato e nell'ambiente locale.
* Le versioni di Platform Services Controller (PSC) nel cloud privato e nell'ambiente locale devono essere compatibili.
* Le versioni di vCenter nel cloud privato e nell'ambiente locale devono essere compatibili.
* Le versioni della replica SRM e vSphere devono essere compatibili tra loro e con le versioni di PSC e vCenter.

Per collegamenti alla documentazione di VMware e alle informazioni sulla compatibilità, visitare la documentazione di [VMware Site Recovery Manager.](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)

Per scoprire le versioni di vCenter e PSC nel tuo Cloud privato, apri il portale CloudSimple. Vai a **Risorse**, seleziona il tuo cloud privato e fai clic sulla scheda **vSphere Management Network.**

![Versioni di vCenter & PSC nel cloud privato](media/srm-resources.png)

### <a name="estimate-the-size-of-your-dr-environment"></a>Stimare le dimensioni dell'ambiente di ripristino di emergenza

1. Verificare che la configurazione locale identificata rientri nei limiti supportati. Per SRM 6.5, i limiti sono documentati nell'articolo della Knowledge Base VMware sui [limiti operativi per Site Recovery Manager 6.5.](https://kb.vmware.com/s/article/2147110)
2. Assicurarsi di disporre di larghezza di banda di rete sufficiente per soddisfare le dimensioni del carico di lavoro e i requisiti di RPO. L'articolo della Knowledge Base di VMware sul calcolo dei requisiti di [larghezza di banda per la replica vSphere](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html) fornisce indicazioni sui limiti di larghezza di banda.
3. Usare lo strumento di dimensione CloudSimple per stimare le risorse necessarie nel sito di ripristino di emergenza per proteggere l'ambiente locale.

### <a name="create-a-private-cloud-for-your-environment"></a>Creare un cloud privato per l'ambienteCreate a Private Cloud for your environment

Creare un cloud privato dal portale CloudSimple seguendo le istruzioni e le raccomandazioni di dimensionamento contenute in [Creare un cloud privato.](create-private-cloud.md)

### <a name="set-up-private-cloud-networking-for-the-srm-solution"></a>Configurare la rete cloud privata per la soluzione SRM

Accedi al portale CloudSimple per configurare la rete cloud privata per la soluzione SRM.

Creare una VLAN per la rete di soluzioni SRM e assegnarle una subnet CIDR. Per istruzioni, vedere [Creare e gestire VLAN/Subnets](create-vlan-subnet.md).

### <a name="set-up-a-site-to-site-vpn-connection-between-your-on-premises-network-and-the-private-cloud-and-open-required-ports"></a>Configurare una connessione VPN da sito a sito tra la rete locale e il cloud privato e aprire le porte necessarie

Per configurare la connettività da sito a sito tra la rete locale e il cloud privato. Per istruzioni, vedere [Configurare una connessione VPN al cloud privato CloudSimple](set-up-vpn.md).

### <a name="set-up-infrastructure-services-in-your-private-cloud"></a>Configurare i servizi di infrastruttura nel cloud privato

Configura i servizi di infrastruttura nel cloud privato per semplificare la gestione dei carichi di lavoro e degli strumenti.

È possibile aggiungere un provider di identità esterno come descritto in Usare Azure AD come provider di [identità per vCenter nel cloud CloudSimple Private Cloud](azure-ad.md) se si vuole eseguire una delle operazioni seguenti:You can add an external identity provider as described in Use Azure AD as an identity provider for vCenter on CloudSimple Private Cloud if you want to do uno dei seguenti elementi:

* Identificare gli utenti da Active Directory (AD) locale nel cloud privato.
* Configurare un AD nel cloud privato per tutti gli utenti.
* Usare Azure AD.

Per fornire servizi di ricerca degli indirizzi IP, gestione degli indirizzi IP e risoluzione dei nomi per i carichi di lavoro nel cloud privato, configurare un server DHCP e DNS come descritto in [Configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato CloudSimple](dns-dhcp-setup.md).

Il dominio cloudsimple.io viene usato dalle macchine virtuali di gestione e dagli host nel cloud privato. Per risolvere le richieste a questo dominio, configurare l'inoltro DNS sul server DNS come descritto in [Creare un server d'inoltro condizionale](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vsphere-replication-appliance-in-your-on-premises-environment"></a>Installare l'appliance di replica vSphere nell'ambiente localeInstall vSphere Replication Appliance in your on-premises environment

Installare vSphere Replication Appliance (vRA) nell'ambiente locale seguendo la documentazione di VMware. L'installazione è costituita da questi passaggi di alto livello:

1. Preparare l'ambiente locale per l'installazione di vRA.

2. Distribuire vRA nell'ambiente locale utilizzando l'OVF nell'ISO VR da vmware.com. Per vRA 6.5, [questo blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contiene le informazioni pertinenti.

3. Registrare la vRA locale con vCenter Single Sign-On nel sito locale. Per istruzioni dettagliate su vSphere Replication 6.5, vedere il documento [VMware VMware vSphere Replication 6.5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

## <a name="install-vsphere-replication-appliance-in-your-private-cloud-environment"></a>Installare l'appliance vSphere Replication nell'ambiente Private CloudInstall vSphere Replication appliance in your Private Cloud environment

Prima di iniziare, verificare di disporre di quanto segue:

* Raggiungibilità IP dalle subnet nell'ambiente locale alla subnet di gestione del cloud privato
* Raggiungibilità IP dalla subnet di replica nell'ambiente vSphere locale alla subnet della soluzione SRM del cloud privato

Per istruzioni, vedere [Configurare una connessione VPN al cloud privato CloudSimple](set-up-vpn.md). I passaggi sono simili a quelli per l'installazione locale.

CloudSimple consiglia di utilizzare FQDN anziché indirizzi IP durante l'installazione di vRA e SRM. Per scoprire il nome di dominio completo di vCenter e PSC nel cloud privato, aprire il portale CloudSimple. Vai a **Risorse**, seleziona il tuo cloud privato e fai clic sulla scheda **vSphere Management Network.**

![Ricerca di FQDN di vCenter/PSC nel cloud privato](media/srm-resources.png)

CloudSimple richiede che non si installi vRA e SRM utilizzando l'utente 'proprietario cloud' predefinito, ma si crei un nuovo utente. Questa operazione viene eseguita per garantire tempi di attività e disponibilità elevati per l'ambiente vCenter del cloud privato. Tuttavia, l'utente predefinito proprietario di cloud nel cloud privato vCenter non dispone di privilegi sufficienti per creare un nuovo utente con privilegi amministrativi.

Prima di installare vRA e SRM, è necessario riassegnare i privilegi vCenter dell'utente proprietario cloud e quindi creare un utente con privilegi amministrativi nel dominio SSO di vCenter. Per informazioni dettagliate sull'utente e sul modello di autorizzazione predefiniti di Private Cloud, consultate Informazioni sul modello di [autorizzazione Cloud privato.](learn-private-cloud-permissions.md)

L'installazione è costituita da questi passaggi di alto livello:

1. [Riassegnare i privilegi](escalate-private-cloud-privileges.md).
2. Creare un utente nel cloud privato per la replica vSphere e l'installazione SRM. Spiegazione di seguito nell'interfaccia utente di [vCenter: Creare un utente in Private Cloud per vRA & l'installazione SRM](#vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation).
3. Preparare l'ambiente Cloud privato per l'installazione di vRA.
4. Distribuisci vRA nel tuo Private Cloud usando l'OVF nell'ISO VR di vmware.com. Per vRA 6.5, [questo blog VMware](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices) contiene informazioni pertinenti.
5. Configurare le regole del firewall per vRA. Spiegato di seguito nel [portale CloudSimple: Configurare le regole del firewall per vRA](#cloudsimple-portal-configure-firewall-rules-for-vra).
6. Registra Private Cloud vRA con vCenter Single Sign-On sul sito Private Cloud.
7. Configurare le connessioni di replica vSphere tra le due appliance. Assicurarsi che le porte necessarie siano aperte attraverso i firewall. Vedere [questo articolo della Knowledge Base di VMware](https://kb.vmware.com/s/article/2087769) per un elenco dei numeri di porta che devono essere aperti per vSphere Replication 6.5.

Per istruzioni dettagliate sull'installazione di vSphere Replication 6.5, vedere il documento [VMware VMware vSphere Replication 6.5 Installation and Configuration](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-vra-and-srm-installation"></a>Interfaccia utente di vCenter: creare un utente nel cloud privato per l'installazione di vRA e SRM

Accedere a vCenter utilizzando le credenziali utente del proprietario cloud dopo l'escalation dei privilegi dal portale CloudSimple.

Creare un nuovo `srm-soln-admin`utente, , in vCenter e aggiungerlo al gruppo Administrators in vCenter.
Disconnettersi da vCenter come utente proprietario di cloud e accedere come utente *srm-soln-admin.*

#### <a name="cloudsimple-portal-configure-firewall-rules-for-vra"></a>Portale CloudSimple: configurare le regole del firewall per vRA

Configurare le regole del firewall come descritto in [Configurare le tabelle e le regole](firewall.md) del firewall per aprire le porte e consentire la comunicazione tra:

* vRA nella rete di soluzioni SRM e vCenter ed ESXi nella rete di gestione.
* apparecchi vRA nei due siti.

Vedere questo [articolo della Knowledge Base di VMware](https://kb.vmware.com/s/article/2087769) per un elenco dei numeri di porta che devono essere aperti per vSphere Replication 6.5.

### <a name="install-srm-server-in-your-on-premises-environment"></a>Installare il server SRM nell'ambiente locale

Prima di iniziare, verificare quanto segue:

* L'appliance di replica vSphere viene installata negli ambienti locale e Private Cloud.
* Le appliance di replica vSphere in entrambi i siti sono connesse tra loro.
* Sono state esaminate le informazioni di VMware sui prerequisiti e le procedure consigliate. Per SRM 6.5, è possibile fare riferimento al documento VMware [Prerequisiti e procedure consigliate per SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

Seguire la documentazione di VMware per eseguire l'installazione del server SRM nel modello di distribuzione 'Topologia a due siti con un'istanza vCenter per controller di servizi della piattaforma', come descritto in questo [documento VMWare.](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html) Le istruzioni di installazione per SRM 6.5 sono disponibili nel documento VMware [Installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

### <a name="install-srm-server-in-your-private-cloud"></a>Installare il server SRM nel cloud privato

Prima di iniziare, verificare quanto segue:

* L'appliance di replica vSphere viene installata negli ambienti locale e Private Cloud.
* Le appliance di replica vSphere in entrambi i siti sono connesse tra loro.
* Sono state esaminate le informazioni di VMware sui prerequisiti e le procedure consigliate. Per SRM 6.5, è possibile fare riferimento a [Prerequisiti e procedure consigliate per l'installazione del server di Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html).

I passaggi seguenti descrivono l'installazione di Private Cloud SRM.

1. [Interfaccia utente di vCenter: installare SRM](#vcenter-ui-install-srm)
2. [Portale CloudSimple: Configurare le regole del firewall per SRM](#cloudsimple-portal-configure-firewall-rules-for-srm)
3. [Interfaccia utente di vCenter: configurare SRM](#vcenter-ui-configure-srm)
4. [Portale CloudSimple: privilegi di de-escalation](#cloudsimple-portal-de-escalate-privileges)

#### <a name="vcenter-ui-install-srm"></a>Interfaccia utente di vCenter: installare SRM

Dopo la registrazione a vCenter utilizzando le credenziali srm-soln-admin, seguire la documentazione di VMware per eseguire l'installazione del server SRM nel modello di distribuzione 'Topologia a due siti con un'istanza vCenter per Controller di servizi della piattaforma', come descritto in questo [documento VMWare](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Le istruzioni di installazione per SRM 6.5 sono disponibili nel documento VMware [Installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

#### <a name="cloudsimple-portal-configure-firewall-rules-for-srm"></a>Portale CloudSimple: Configurare le regole del firewall per SRM

Configurare le regole del firewall come descritto in [Configurare tabelle e regole del firewall per](firewall.md) consentire la comunicazione tra:

Il server SRM e vCenter / PSC nel cloud privato.
I server SRM in entrambi i siti

Vedere [questo articolo della Knowledge Base di VMware](https://kb.vmware.com/s/article/2087769) per un elenco dei numeri di porta che devono essere aperti per vSphere Replication 6.5.

#### <a name="vcenter-ui-configure-srm"></a>Interfaccia utente di vCenter: configurare SRM

Dopo aver installato SRM nel cloud privato, eseguire le attività seguenti come descritto nelle sezioni della Guida all'installazione e alla configurazione di VMware Site Recovery Manager. Per SRM 6.5, le istruzioni sono disponibili nel documento VMware [Installazione di Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html).

1. Connettere le istanze del server di Site Recovery Manager nei siti protetti e di ripristino.
2. Stabilire una connessione client all'istanza del server Di Gestione di gestione siti remoti.
3. Installare la chiave di licenza di Site Recovery Manager.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portale CloudSimple: privilegi di de-escalation

Per decennare i privilegi, vedere [De-escalation dei privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="ongoing-management-of-your-srm-solution"></a>Gestione continua della soluzione SRM

Si dispone del controllo completo sulla replica vSphere e sul software SRM nell'ambiente Cloud privato e si prevede di eseguire la gestione del ciclo di vita del software necessaria. Assicurarsi che qualsiasi nuova versione del software sia compatibile con il Private Cloud vCenter e PSC prima di aggiornare o aggiornare vSphere Replication o SRM.

> [!NOTE]
> CloudSimple sta attualmente esplorando le opzioni per offrire un servizio di ripristino di emergenza gestito. 

## <a name="multiple-replication-configuration"></a>Configurazione di replica multipla

 [Entrambe le tecnologie di replica basata su array e vSphere possono essere utilizzate contemporaneamente con SRM.](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication) Tuttavia, devono essere applicati a set separato di macchine virtuali (una determinata macchina virtuale può essere protetta tramite la replica basata su array o la replica vSphere, ma non entrambe). Inoltre, il sito CloudSimple può essere configurato come sito di ripristino per più siti protetti. Per informazioni sulle configurazioni multisito, vedere [Opzioni multisito SRM.](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite/)

## <a name="references"></a>Riferimenti

* [Documentazione di VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
* [Limiti operativi per Site Recovery Manager 6.5](https://kb.vmware.com/s/article/2147110)
* [Calcolo dei requisiti di larghezza di banda per la replica vSphereCalculating bandwidth requirements for vSphere Replication](https://docs.vmware.com/en/vSphere-Replication/6.5/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
* [Scelte OVF durante la distribuzione di vSphere Replication 6.5](https://blogs.vmware.com/virtualblocks/2017/01/20/vr-65-ovf-choices/)
* [VMware vSphere Replica 6.5 Installazione e configurazione](https://docs.vmware.com/en/vSphere-Replication/6.5/vsphere-replication-65-install.pdf)
* [Prerequisiti e procedure consigliate per SRM 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
* [Gestione gestione siti in una topologia a due siti con un'istanza del server vCenter per ogni controller di servizi della piattaforma](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html)
* [Guida all'installazione e alla configurazione di VMware Site Recovery Manager 6.5](https://docs.vmware.com/en/Site-Recovery-Manager/6.5/com.vmware.srm.install_config.doc/GUID-437E1B65-A17B-4B4B-BA5B-C667C90FA418.html)
* [Blog di VMware su SRM con replica basata su array e replica vSphere](https://blogs.vmware.com/virtualblocks/2017/06/22/srm-array-based-replication-vs-vsphere-replication)
* [Blog di VMware sulle opzioni multisito SRM](https://blogs.vmware.com/virtualblocks/2016/07/28/srm-multisite)
* [Numeri di porta che devono essere aperti per la replica vSphere 5.8.x, 6.x e 8](https://kb.vmware.com/s/article/2147112)
