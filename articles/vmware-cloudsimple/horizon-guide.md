---
title: Soluzione Azure VMware di CloudSimple - Usare il sito Private Cloud per ospitare un'infrastruttura desktop virtuale con VMware Horizon
description: Descrive come utilizzare il sito CloudSimple Private Cloud per ospitare un'infrastruttura desktop virtuale tramite VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 723821a78ecae308443c93567402e3b232c036f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025249"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Utilizzare il sito CloudSimple Private Cloud per ospitare un'infrastruttura di desktop virtuale tramite VMware Horizon

È possibile utilizzare il sito CloudSimple Private Cloud per ospitare un'infrastruttura di desktop virtuale (VDI) utilizzando VMware Horizon 7.x. Nella figura seguente viene illustrata l'architettura della soluzione logica per l'unità VDI.

![Distribuzione di Horizon](media/horizon-deployment.png)

Con questa soluzione, hai il pieno controllo su Horizon View Manager e App Volume. Le interfacce familiari dell'interfaccia utente, dell'API e dell'interfaccia della riga di comando consentono l'uso degli script e degli strumenti esistenti.

La soluzione CloudSimple richiede di eseguire le operazioni seguenti:The CloudSimple solution requires you to do the following:

* Installare, configurare e gestire VMware Horizon 7.x nel cloud privato.
* Fornire le proprie licenze Horizon.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Le sezioni seguenti descrivono come distribuire una soluzione VDI usando Horizon nel cloud privato.

1. [Verificare che le versioni del prodotto VMware siano compatibili](#verify-that-vmware-product-versions-are-compatible)
2. [Stimare le dimensioni dell'ambiente desktop](#estimate-the-size-of-your-desktop-environment)
3. [Creare un cloud privato per l'ambienteCreate a Private Cloud for your environment](#create-a-private-cloud-for-your-environment)
4. [Installare VMware Horizon nel cloud privato](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificare che le versioni del prodotto VMware siano compatibili

* Verificare che le versioni correnti e pianificate di Horizon, Volumi app, Unified Access Gateway e User Environment Manager siano compatibili tra loro e con vCenter e PSC nel cloud privato. Per informazioni sulla compatibilità, vedere [VMware Compatibility Matrix per Horizon 7.5.](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=)
* Per scoprire le versioni correnti di vCenter e PSC nel cloud privato, vai a **Risorse** nel [portale CloudSimple](access-cloudsimple-portal.md), seleziona il tuo cloud privato e fai clic sulla scheda **vSphere Management Network.**

![Versioni vCenter e PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Stimare le dimensioni dell'ambiente desktop

* Verificare che la configurazione identificata rientri nei limiti operativi di VMware.
* Stimare le risorse necessarie per tutti i desktop e i componenti di gestione di Horizon.

### <a name="create-a-private-cloud-for-your-environment"></a>Creare un cloud privato per l'ambienteCreate a Private Cloud for your environment

1. Creare un cloud privato dal portale CloudSimple seguendo le istruzioni in [Configurare un ambiente Cloud privato.](quickstart-create-private-cloud.md)  CloudSimple crea un utente vCenter predefinito denominato 'cloudowner' in ogni cloud privato appena creato. Per informazioni dettagliate sull'utente e sul modello di autorizzazione predefiniti di Private Cloud, vedere [Learn the Private Cloud permissions model](learn-private-cloud-permissions.md).
2. Creare una VLAN nel cloud privato per il piano di gestione Horizon e assegnarle una subnet CIDR. Per istruzioni, vedere [Creare e gestire VLAN/Subnets](create-vlan-subnet.md). Si tratta della rete in cui verranno installati tutti i componenti della soluzione (unified Access Gateway, Connection Server, App Volume Server e server User Environment Manager).
3. Decidere se si desidera utilizzare un provider di identità esterno con il vCenter del cloud privato. In caso affermativo, scegliere una di queste opzioni:
    * Utilizzare Active Directory locale come provider di identità esterno. Per istruzioni, vedere [Origini identità di vCenter](set-vcenter-identity.md).
    * Configurare un server Active Directory nella VLAN del piano di gestione Private Cloud in Horizon da utilizzare come provider di identità esterno. Per istruzioni, vedere [Origini identità di vCenter](set-vcenter-identity.md).
    * Configurare un server DHCP e DNS nella VLAN del piano di gestione Horizon nel cloud privato. Per istruzioni, vedere [Configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato CloudSimple](dns-dhcp-setup.md).
4. Configurare l'inoltro DNS nel server DNS installato nel cloud privato. Per istruzioni, vedere Creazione di [un server d'inoltro condizionale](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Installare VMware Horizon nel cloud privato

Il diagramma di distribuzione seguente illustra una soluzione Horizon distribuita in un cloud privato. Unified Access Gateway, AD/DC, View e App Volume Server sono installati nella VLAN 234 creata dall'utente. Unified Access Gateway dispone di un indirizzo IP pubblico assegnato a internet. Le macchine virtuali del pool desktop Horizon vengono distribuite in VLAN 235 per fornire ulteriore isolamento e sicurezza.

![Distribuzione di Horizon nel cloud privato](media/horizon-private-cloud.png)

Nelle sezioni seguenti vengono descritte le istruzioni per configurare una distribuzione simile a quella illustrata nella figura. Prima di iniziare, verificare di disporre di quanto segue:

* Un cloud privato creato utilizzando il portale CloudSimple con capacità sufficiente per eseguire i pool di desktop.
* Larghezza di banda sufficiente tra l'ambiente locale e l'ambiente Cloud privato per supportare il traffico di rete per i desktop.
* Un tunnel VPN da sito a sito configurato tra il data center locale e il cloud privato.
* Raggiungibilità IP dalle subnet degli utenti finali nell'ambiente locale alle subnet CloudSimple Private Cloud.
* AD/DHCP/DNS installato per il cloud privato.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portale CloudSimple: creare una VLAN/subnet dedicata per i pool di desktop

Creare una VLAN per i pool di desktop Horizon e assegnarle una subnet CIDR. Per istruzioni, vedere [Creare e gestire VLAN/Subnets](create-vlan-subnet.md). Questa è la rete in cui verranno eseguite tutte le macchine virtuali desktop.

Segui le best practice di sicurezza standard per proteggere la distribuzione di Horizon:

* Consentire solo il traffico RDP desktop / traffico SSH alle macchine virtuali desktop.
* Consentire solo il traffico di gestione tra la VLAN del piano di gestione di Horizon e la VLAN del pool desktop.
* Consentire solo il traffico di gestione dalla rete locale.

È possibile applicare queste procedure consigliate configurando [le regole del firewall](firewall.md) dal portale CloudSimple.You can enforce these best practices by configuring firewall rules from the CloudSimple portal.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portale CloudSimple: configurare le regole del firewall per proteggere il piano di gestione di Horizon

Impostare le regole seguenti nel portale CloudSimple.Set up the following rules in the CloudSimple portal. Per istruzioni, consultate [Configurare tabelle e regole del firewall.](firewall.md)

1. Configurare le regole del firewall nel firewall CloudSimple N-S per consentire la comunicazione tra subnet locali e VLAN di gestione Horizon in modo che siano consentite solo le porte di rete elencate nell'elenco delle [porte Horizon](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) del documento VMware.

2. Creare regole firewall E-W tra la VLAN di gestione Horizon e la VLAN del pool di desktop nel cloud privato.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portale CloudSimple: creare un indirizzo IP pubblico per Unified Access Gateway

Creare un indirizzo IP pubblico per l'appliance Unified Access Gateway per abilitare le connessioni client desktop da Internet. Per istruzioni, consultate [Allocare indirizzi IP pubblici.](public-ips.md)

Al termine dell'installazione, l'indirizzo IP pubblico viene assegnato ed elencato nella pagina IP pubblico.

#### <a name="cloudsimple-portal-escalate-privileges"></a>Portale CloudSimple: escalation dei privilegi

L'utente predefinito 'cloudowner' non dispone di privilegi sufficienti nel vCenter del cloud privato per installare Horizon, pertanto i privilegi vCenter dell'utente devono essere riassegnati. Per ulteriori informazioni, consultate [Riassegnare i privilegi.](escalate-private-cloud-privileges.md)

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>Interfaccia utente di vCenter: creare un utente nell'installazione di Private Cloud for Horizon

1. Accedere a vCenter utilizzando le credenziali utente 'cloudowner'.
2. Creare un nuovo utente, 'horizon-soln-admin', in vCenter e aggiungerlo al gruppo Administrators in vCenter.
3. Esci da vCenter come utente "proprietario cloud" e accedi come utente 'horizon-soln-admin'.

#### <a name="vcenter-ui-install-vmware-horizon"></a>Interfaccia utente di vCenter: installare VMware Horizon

Come indicato nella sezione relativa all'architettura logica precedente, la soluzione Horizon presenta i componenti seguenti:As mentioned in the earlier logical architecture section, Horizon solution has the following components:

* VMware Horizon Vista
* Gateway di accesso unificato di VMware
* VMware App Volume Manager
* Gestione ambiente utente VMware

Installare i componenti come segue:

1. Installare e configurare Unified Access Gateway seguendo le istruzioni fornite nel documento di VMware Distribuzione e configurazione di [VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installare Horizon View nel cloud privato seguendo le istruzioni riportate nella [Visualizza guida all'installazione](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Installare App Volume Manager seguendo le istruzioni in Installare e configurare i volumi delle [app VMware.](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)

4. Installare e configurare User Environment Manager seguendo le istruzioni in [Informazioni sull'installazione e la configurazione di VMware User Environment Manager](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Invia una richiesta di supporto per caricare i volumi delle app preconfezionate VMware Horizon

Come parte del processo di installazione, App Volume Manager usa volumi preconfezionati per eseguire il provisioning degli stack di app e dei volumi scrivibili. Questi volumi fungono da modelli per gli stack di app e i volumi scrivibili.

Il caricamento dei volumi nell'archivio dati del cloud privato richiede la password root ESXi. Per assistenza, inviare una richiesta di [supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Collegare il pacchetto di installazione AppVolumes in modo che il personale di supporto CloudSimple possa caricare i modelli nell'ambiente Cloud privato.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>Portale CloudSimple: privilegi di de-escalation

È ora possibile [riassegnare i privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges) dell'utente 'cloudowner'.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gestione continua della soluzione Horizon

Hai il pieno controllo sul software Horizon e App Volume Manager nel tuo ambiente Private Cloud e dovresti eseguire la gestione del ciclo di vita del software necessaria. Assicurati che tutte le nuove versioni del software siano compatibili con il vCenter e PSC del cloud privato prima di aggiornare o aggiornare Horizon o App Volume.
