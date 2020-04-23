---
title: 'Guida introduttiva: Creare un cloud privato'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Informazioni su come creare e configurare un cloud privato con soluzioni VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a2fe7daf1476f19f6a6eea83174342fa1273fe14
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867970"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Guida introduttiva - Configurare un ambiente Cloud privato

In questo articolo viene illustrato come creare un cloud privato CloudSimple e configurare l'ambiente Private Cloud.

## <a name="before-you-begin"></a>Prima di iniziare

Esaminare [I prerequisiti di rete](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Creare un cloud privato

Un cloud privato è uno stack VMware isolato che supporta host ESXi, vCenter, vSAN e NSX.

I cloud privati vengono gestiti tramite il portale CloudSimple. Hanno il proprio server vCenter nel proprio dominio di gestione. Lo stack viene eseguito su nodi dedicati e nodi hardware bare metal isolati.

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Services**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il cloud privato.
4. In **Panoramica**fare clic su **Crea cloud privato** per aprire una nuova scheda del browser per il portale CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.If prompted, sign in with your Azure sign in credentials.  

    ![Creare un cloud privato da AzureCreate Private Cloud from Azure](media/create-private-cloud-from-azure.png)

5. Nel portale CloudSimple specificare un nome per il cloud privato.
6. Selezionare la **posizione** del cloud privato.
7. Selezionare **Tipo di nodo**, coerente con il provisioning in Azure.Select Node type , consistent with what you provisioned on Azure.
8. Specificare **il numero di nodi**.  Per creare un cloud privato sono necessari almeno tre nodi.

    ![Creare un cloud privato - Informazioni di base](media/create-private-cloud-basic-info.png)

9. Fare clic su **Successivo: Opzioni avanzate**.
10. Immettere l'intervallo CIDR per le subnet vSphere/vSAN. Assicurarsi che l'intervallo CIDR non si sovrapponga a nessuna delle subnet di Azure (reti virtuali) locali o di altre subnet di Azure o con la subnet del gateway.

    **Opzioni dell'intervallo CIDR:** /24, /23, /22 o /21. Un intervallo CIDR /24 supporta fino a 26 nodi, un intervallo /23 CIDR supporta fino a 58 nodi e un intervallo CIDR /22 e /21 supporta 64 nodi (il numero massimo di nodi in un cloud privato).  Per altre informazioni e VLAN e subnet, vedere [VLAN e subnet overview](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Gli indirizzi IP nell'intervallo CIDR vSphere/vSAN sono riservati per l'utilizzo da parte dell'infrastruttura Private Cloud.  Non usare l'indirizzo IP in questo intervallo in qualsiasi macchina virtuale.

11. Fare clic su **Avanti: Revisione e creazione**.
12. Rivedere le impostazioni. Se è necessario modificare le impostazioni, fare clic su **Indietro**.
13. Fare clic su **Crea**.

Viene avviato il processo di provisioning del cloud privato.  Il provisioning del cloud privato può richiedere fino a due ore.

## <a name="launch-cloudsimple-portal"></a>Avvia il portale CloudSimple

È possibile accedere al portale CloudSimple dal portale di Azure.You can access the CloudSimple portal from Azure portal.  Il portale CloudSimple verrà avviato con le credenziali di accesso di Azure tramite Single Sign-On (SSO).  Per accedere al portale CloudSimple è necessario autorizzare l'applicazione **CloudSimple Service Authorization.**  Per ulteriori informazioni sulla concessione delle autorizzazioni, vedere [Consenso all'applicazione cloudSimple Service Authorization](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Selezionare **Tutti i servizi**.
2. Cercare **CloudSimple Services**.
3. Selezionare il servizio CloudSimple in cui si desidera creare il cloud privato.
4. Dalla panoramica, fare clic su **Vai al portale CloudSimple** per aprire una nuova scheda del browser per il portale CloudSimple.  Se richiesto, accedere con le credenziali di accesso di Azure.If prompted, sign in with your Azure sign in credentials.  

    ![Avvia il portale CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Creare una VPN da punto a sito

Una connessione VPN da punto a sito è il modo più semplice per connettersi al cloud privato dal computer. Utilizzare la connessione VPN da punto a sito se ci si connette al cloud privato in remoto.  Per un rapido accesso al tuo cloud privato, segui questi passaggi.  L'accesso all'area CloudSimple dalla rete locale può essere eseguito usando VPN da [sito a sito](vpn-gateway.md) o Azure [ExpressRoute.](on-premises-connection.md)

### <a name="create-gateway"></a>Creare il gateway

1. Avviare il portale CloudSimple e selezionare **Rete**.
2. Selezionare **Gateway VPN**.
3. Fare clic su **Nuovo gateway VPN**.

    ![Creare un gateway VPN](media/create-vpn-gateway.png)

4. Per **Configurazione gateway**, specificare le impostazioni seguenti e fare clic su **Avanti**.

    * Selezionare **VPN da punto a sito** come tipo di gateway.
    * Immettere un nome per identificare il gateway.
    * Selezionare il percorso di Azure in cui viene distribuito il servizio CloudSimple.Select the Azure location where your CloudSimple service is deployed.
    * Specificare la subnet client per il gateway da punto a sito.  Gli indirizzi DHCP verranno forniti da questa subnet quando ci si connette.

5. In **Connessione/Utente**, specificare le seguenti impostazioni e fare clic su **Avanti**.

    * Per consentire automaticamente a tutti gli utenti attuali e futuri di accedere al cloud privato tramite questo gateway da punto a sito, selezionare **Aggiungi automaticamente tutti gli utenti**. Quando si seleziona questa opzione, tutti gli utenti nell'elenco Utente vengono selezionati automaticamente. È possibile ignorare l'opzione automatica deselezionando singoli utenti nell'elenco.
    * Per selezionare solo singoli utenti, fare clic sulle caselle di controllo nell'elenco Utente.

6. La sezione VLAN/subnet consente di specificare le VLAN/subnet di gestione e utente per il gateway e le connessioni.

    * Le opzioni **aggiungi automaticamente** impostano i criteri globali per questo gateway. Le impostazioni si applicano al gateway corrente. Le impostazioni possono essere sostituite nell'area **Seleziona.**
    * Selezionare **Aggiungi VLAN/subnet di gestione di cloud privati**.
    * Per aggiungere tutte le VLAN/subnet definite dall'utente, fare clic su **Aggiungi VLAN/subnet definite dall'utente**.
    * Le impostazioni **di selezione** sostituiscono le impostazioni globali in **Aggiungi automaticamente**.

7. Fare clic su **Avanti** per rivedere le impostazioni. Fare clic sulle icone Modifica per apportare eventuali modifiche.
8. Fare clic su **Crea** per creare il gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Connettersi a CloudSimple tramite VPN da punto a sito

Il client VPN è necessario per la connessione a CloudSimple dal computer.  Scarica il [client OpenVPN](https://openvpn.net/community-downloads/) per Windows o [Viscosity](https://www.sparklabs.com/viscosity/download/) per macOS e OS X.

1. Avviare il portale CloudSimple e selezionare **Rete**.
2. Selezionare **Gateway VPN**.
3. Nell'elenco dei gateway VPN fare clic sul gateway VPN da punto a sito.
4. Selezionare **Utenti**.
5. Fare clic su **Scarica configurazione VPN**.

    ![Scaricare la configurazione della VPN](media/download-p2s-vpn-configuration.png)

6. Importare la configurazione nel client VPN.

    * Istruzioni per [l'importazione della configurazione nel client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Istruzioni per [l'importazione della configurazione su macOS o OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Connettiti a CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Creare una VLAN per le macchine virtuali del carico di lavoroCreate a VLAN for your workload VMs

Dopo aver creato un cloud privato, creare una VLAN in cui distribuire le macchine virtuali del carico di lavoro/applicazione.

1. Nel portale CloudSimple selezionare **Rete**.
2. Fare clic su **VLAN/Subnets**.
3. Fare clic su **Crea VLAN/Subnet**.

    ![Creazione di VLAN/Subnet](media/create-new-vlan-subnet.png)

4. Selezionare il **cloud privato** per la nuova VLAN/subnet.
5. Selezionare un ID VLAN dall'elenco.  
6. Immettere un nome di subnet per identificare la subnet.
7. Specificare l'intervallo CIDR della subnet e la maschera.  Questo intervallo non deve sovrapporsi alle subnet esistenti.
8. Fare clic su **Submit** (Invia).

    ![Creare dettagli VLAN/SubnetCreate VLAN/Subnet details](media/create-new-vlan-subnet-details.png)

Verrà creata la VLAN/subnet.  È ora possibile utilizzare questo ID VLAN per creare un gruppo di porte distribuite nel vCenter del cloud privato.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connettere l'ambiente a una rete virtuale di AzureConnect your environment to an Azure virtual network

CloudSimple fornisce un circuito ExpressRoute per il cloud privato. È possibile connettere la rete virtuale in Azure al circuito ExpressRoute.You can connect your virtual network on Azure to the ExpressRoute circuit. Per informazioni dettagliate sulla configurazione della connessione, seguire i passaggi in Connessione di rete virtuale di [Azure tramite ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-azure-network-connection).

## <a name="sign-in-to-vcenter"></a>Accedere a vCenter

È ora possibile accedere a vCenter per configurare le macchine virtuali e i criteri.

1. Per accedere a vCenter, iniziare dal portale CloudSimple. Nella home page, in **Attività comuni**, fare clic su Avvia **client vSphere**.  Selezionare il cloud privato e quindi fare clic su **Avvia client vSphere** sul cloud privato.

    ![Avvia vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selezionare il client vSphere preferito per accedere a vCenter e accedere con il nome utente e la password.  Le impostazioni predefinite sono:
    * Nome utente:`CloudOwner@cloudsimple.local`
    * Password: `CloudSimple123!`

Le schermate di vCenter nelle procedure successive provengono dal client vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modificare la password di vCenter

CloudSimple consiglia di modificare la password al primo accesso a vCenter.  
La password impostata deve soddisfare i seguenti requisiti:

* Durata massima: la password deve essere modificata ogni 365 giorniMaximum lifetime: Password must be changed every 365 days
* Limitare il riutilizzo: gli utenti non possono riutilizzare nessuna delle cinque password precedenti
* Lunghezza: 8 - 20 caratteri
* Carattere speciale: almeno un carattere speciale
* Caratteri alfabetici: almeno un carattere maiuscolo, dalla A alla z, e almeno un carattere minuscolo, a-z
* Numeri: almeno un carattere numerico, 0-9
* Numero massimo di caratteri adiacenti identici: Tre

    Esempio: CC o CCC è accettabile come parte della password, ma CCCC non lo è.

Se imposti una password che non soddisfa i requisiti:

* se si utilizza vSphere Flash Client, viene segnalato un errore
* Se si utilizza il client HTML5, non viene segnalato un errore. Il client non accetta la modifica e la vecchia password continua a funzionare.

## <a name="access-nsx-manager"></a>Accedere a Gestore NSX

NsX manager viene distribuito con una password predefinita. 

* Nome utente: **admin**
* Password: **CloudSimple123!**

È possibile trovare il nome di dominio completo (FQDN) e l'indirizzo IP del gestore NSX nel portale CloudSimple.You can find the fully qualified domain name (FQDN) and IP address of NSX manager on CloudSimple portal.

1. Avviare il portale CloudSimple e selezionare **Risorse**.
2. Fare clic sul cloud privato, che si desidera utilizzare.
3. Selezionare **la rete di gestione vSphereSelect vSphere management network**
4. Utilizzare il nome di dominio completo o l'indirizzo IP di **NSX Manager** e connettersi tramite un Web browser.

    ![Trovare NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Creare un gruppo di porteCreate a port group

Per creare un gruppo di porte distribuite in vSphere:To create a distributed port group in vSphere:

1. Seguire le istruzioni in "Aggiungere un gruppo di porte distribuite" in [vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Quando si configura il gruppo di porte distribuite, specificare l'ID VLAN creato in [Creare una VLAN per le macchine virtuali del carico](#create-a-vlan-for-your-workload-vms)di lavoro .

## <a name="next-steps"></a>Passaggi successivi

* [Utilizzare VM di VMware in Azure](quickstart-create-vmware-virtual-machine.md)
* [Connettersi alla rete locale usando Azure ExpressRouteConnect to on-premises network using Azure ExpressRoute](on-premises-connection.md)
* [Configurare la VPN da sito a sito dall'ambiente locale](vpn-gateway.md)
