---
title: "Azure VMware Solutions (AVS): usare il sito del cloud privato AVS per ospitare un'infrastruttura di desktop virtuale con VMware Horizon"
description: Viene descritto come è possibile usare il sito del cloud privato AVS per ospitare un'infrastruttura di desktop virtuale con VMware Horizon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f909ce297344e80ed6004631d5218d6a7fcd5085
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025249"
---
# <a name="use-avs-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>Usare il sito del cloud privato AVS per ospitare un'infrastruttura di desktop virtuale usando VMware Horizon

È possibile usare il sito del cloud privato AVS per ospitare un'infrastruttura VDI (Virtual Desktop Infrastructure) con VMware Horizon 7. x. Nella figura seguente è illustrata l'architettura della soluzione logica per l'infrastruttura VDI.

![Distribuzione Horizon](media/horizon-deployment.png)

Con questa soluzione si ha il controllo completo su Horizon View Manager e volume dell'app. Le interfacce dell'interfaccia della riga di comando e dell'interfaccia utente, API e CLI, consentono di usare gli script e gli strumenti esistenti.

Per la soluzione AVS è necessario eseguire le operazioni seguenti:

* Installare, configurare e gestire VMware Horizon 7. x nel cloud privato AVS.
* Fornire le proprie licenze Horizon.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Le sezioni seguenti descrivono come distribuire una soluzione VDI usando Horizon nel cloud privato AVS.

1. [Verificare che le versioni del prodotto VMware siano compatibili](#verify-that-vmware-product-versions-are-compatible)
2. [Stima delle dimensioni dell'ambiente desktop](#estimate-the-size-of-your-desktop-environment)
3. [Creare un cloud privato AVS per l'ambiente](#create-an-avs-private-cloud-for-your-environment)
4. [Installare VMware Horizon nel cloud privato AVS](#install-vmware-horizon-in-your-avs-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>Verificare che le versioni del prodotto VMware siano compatibili

* Verificare che le versioni correnti e pianificate di Horizon, volumi di app, Unified Access Gateway e gestione dell'ambiente utente siano compatibili tra loro e con vCenter e PSC nel cloud privato AVS. Per informazioni sulla compatibilità, vedere la pagina relativa alla [matrice di compatibilità VMware per Horizon 7,5](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Per individuare le versioni correnti di vCenter e PSC nel cloud privato AVS, passare a **risorse** nel [portale AVS](access-cloudsimple-portal.md), selezionare il cloud privato AVS, quindi fare clic sulla scheda **rete di gestione vSphere** .

![versioni di vCenter e PSC](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Stima delle dimensioni dell'ambiente desktop

* Verificare che la configurazione identificata rientri nei limiti operativi di VMware.
* Stimare le risorse necessarie per tutti i desktop e i componenti di gestione degli orizzonti.

### <a name="create-an-avs-private-cloud-for-your-environment"></a>Creare un cloud privato AVS per l'ambiente

1. Per creare un cloud privato AVS dal portale AVS, seguire le istruzioni riportate in [configurare un ambiente cloud privato AVS](quickstart-create-private-cloud.md). AVS crea un utente vCenter predefinito denominato "cloudowner" in ogni nuovo cloud privato AVS creato. Per informazioni dettagliate sull'utente e sul modello di autorizzazione del cloud privato AVS, vedere [informazioni sul modello di autorizzazione AVS private cloud](learn-private-cloud-permissions.md).
2. Creare una VLAN nel cloud privato AVS per il piano di gestione Horizon e assegnarle un CIDR della subnet. Per istruzioni, vedere [creare e gestire VLAN/subnet](create-vlan-subnet.md). Si tratta della rete in cui verranno installati tutti i componenti della soluzione (il gateway di accesso unificato, il server di connessione, il server del volume app e i server di gestione dell'ambiente utente).
3. Decidere se si vuole usare un provider di identità esterno con AVS private cloud vCenter. In caso affermativo, scegliere una delle opzioni seguenti:
    * Usare il Active Directory locale come provider di identità esterno. Per istruzioni, vedere [origini di identità vCenter](set-vcenter-identity.md).
    * Configurare un server di Active Directory nel cloud privato AVS in una VLAN del piano di gestione Horizon da usare come provider di identità esterno. Per istruzioni, vedere [origini di identità vCenter](set-vcenter-identity.md).
    * Configurare un server DHCP e DNS nella VLAN del piano di gestione Horizon nel cloud privato AVS. Per istruzioni, vedere [configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato AVS](dns-dhcp-setup.md).
4. Configurare l'invio DNS nel server DNS installato nel cloud privato AVS. Per istruzioni, vedere la pagina relativa alla [creazione di un server d'invio condizionale](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-avs-private-cloud"></a>Installare VMware Horizon nel cloud privato AVS

Il diagramma di distribuzione seguente illustra una soluzione Horizon distribuita in un cloud privato AVS. Unified Access Gateway, AD/DC, View e app volume server sono installati nella VLAN 234 creata dall'utente. Unified Access Gateway ha un indirizzo IP pubblico assegnato raggiungibile da Internet. Le macchine virtuali del pool di desktop Horizon vengono distribuite nella VLAN 235 per fornire isolamento e sicurezza aggiuntivi.

![Distribuzione Horizon nel cloud privato AVS](media/horizon-private-cloud.png)

Le sezioni seguenti illustrano le istruzioni per configurare una distribuzione simile a quella illustrata nella figura. Prima di iniziare, verificare che siano disponibili gli elementi seguenti:

* Un cloud privato AVS creato usando il portale AVS con capacità sufficiente per l'esecuzione dei pool di desktop.
* Larghezza di banda sufficiente tra l'ambiente locale e l'ambiente di cloud privato AVS per supportare il traffico di rete per i desktop.
* Un tunnel VPN da sito a sito configurato tra il Data Center locale e il cloud privato AVS.
* Raggiungibilità IP dalle subnet degli utenti finali nell'ambiente locale alle subnet del cloud privato AVS.
* AD/DHCP/DNS installato per il cloud privato AVS.

#### <a name="avs-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>Portale AVS: creare una VLAN/subnet dedicata per i pool di desktop

Creare una VLAN per i pool di desktop Horizon e assegnarle un CIDR della subnet. Per istruzioni, vedere [creare e gestire VLAN/subnet](create-vlan-subnet.md). Si tratta della rete in cui vengono eseguite tutte le macchine virtuali desktop.

Seguire le procedure consigliate per la protezione standard per proteggere la distribuzione di Horizon:

* Consenti solo traffico RDP desktop/traffico SSH alle VM desktop.
* Consenti solo il traffico di gestione tra la VLAN del piano di gestione Horizon e la VLAN del pool desktop.
* Consenti solo il traffico di gestione dalla rete locale.

È possibile applicare queste procedure consigliate configurando le [regole del firewall](firewall.md) dal portale AVS.

#### <a name="avs-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>Portale AVS: configurare le regole del firewall per proteggere il piano di gestione Horizon

Configurare le regole seguenti nel portale di AVS. Per istruzioni, vedere [configurare le regole e le tabelle del firewall](firewall.md).

1. Configurare le regole del firewall nel firewall AVS N-S per consentire la comunicazione tra subnet locali e la VLAN di gestione Horizon in modo che siano consentite solo le porte di rete elencate nell'elenco delle porte di [orizzonte](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) dei documenti VMware.

2. Creare regole del firewall E-W tra la VLAN di gestione Horizon e la VLAN del pool desktop nel cloud privato AVS.

#### <a name="avs-portal-create-a-public-ip-address-for-unified-access-gateway"></a>Portale AVS: creare un indirizzo IP pubblico per Unified Access Gateway

Creare un indirizzo IP pubblico per l'appliance del gateway di accesso unificato per abilitare le connessioni client desktop da Internet. Per istruzioni, vedere [allocare indirizzi IP pubblici](public-ips.md).

Al termine dell'installazione, l'indirizzo IP pubblico viene assegnato ed elencato nella pagina indirizzi IP pubblici.

#### <a name="avs-portal-escalate-privileges"></a>Portale AVS: escalation dei privilegi

L'utente ' cloudowner ' predefinito non dispone di privilegi sufficienti in AVS private cloud vCenter per installare Horizon, quindi è necessario eseguire l'escalation dei privilegi vCenter dell'utente. Per ulteriori informazioni, vedere [privilegi di escalation](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-avs-private-cloud-for-horizon-installation"></a>INTERFACCIA utente di vCenter: creare un utente nel cloud privato AVS per l'installazione di Horizon

1. Accedere a vCenter usando le credenziali utente ' cloudowner '.
2. Creare un nuovo utente, "Horizon-soln-admin", in vCenter e aggiungere l'utente al gruppo Administrators in vCenter.
3. Disconnettersi da vCenter come utente "cloudowner" e accedere come utente "Horizon-soln-admin".

#### <a name="vcenter-ui-install-vmware-horizon"></a>interfaccia utente di vCenter: installare VMware Horizon

Come indicato nella sezione dell'architettura logica precedente, la soluzione Horizon presenta i componenti seguenti:

* VMware Horizon View
* Gateway di accesso unificato VMware
* Gestione volume app VMware
* Gestione ambiente utente VMware

Installare i componenti come segue:

1. Installare e configurare Unified Access Gateway seguendo le istruzioni fornite nel documento VMware [distribuzione e configurazione di VMware Unified Access Gateway](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html).

2. Installare la visualizzazione Horizon nel cloud privato AVS seguendo le istruzioni riportate in [visualizzare la Guida all'installazione](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html).

3. Installare app Volume Manager seguendo le istruzioni riportate in [installare e configurare i volumi delle app VMware](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html).

4. Installare e configurare Gestione ambiente utente seguendo le istruzioni riportate in [informazioni sull'installazione e la configurazione di VMware User Environment Manager](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html).

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>Inviare una richiesta di supporto per caricare i volumi delle app pre-assemblate VMware Horizon

Nell'ambito del processo di installazione, App Volume Manager usa i volumi pre-assemblati per eseguire il provisioning di stack di app e volumi scrivibili. Questi volumi vengono usati come modelli per gli stack di app e i volumi scrivibili.

Il caricamento dei volumi in AVS private cloud datastore richiede la password radice ESXi. Per assistenza, inviare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Allegare il bundle del programma di installazione di AppVolumes in modo che il personale di supporto AVS possa caricare i modelli nell'ambiente di cloud privato AVS.

#### <a name="avs-portal-de-escalate-privileges"></a>Portale di AVS: decalation dei privilegi

È ora possibile [deinoltrare i privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges) dell'utente ' cloudowner '.

## <a name="ongoing-management-of-your-horizon-solution"></a>Gestione continuativa della soluzione Horizon

Si ha il controllo completo sul software di Horizon e volume Manager nell'ambiente di cloud privato AVS e si prevede di eseguire la necessaria gestione del ciclo di vita del software. Verificare che tutte le nuove versioni del software siano compatibili con AVS private cloud vCenter e PSC prima di aggiornare o aggiornare il volume dell'app o dell'orizzonte.
