---
title: Eseguire il failover locale delle macchine virtuali di Azure con Azure Site RecoveryConnect to Azure VMs on-premises failover with Azure Site Recovery
description: Descrive come connettersi alle macchine virtuali di Azure dopo il failover da locale ad Azure usando Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281990"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Connettersi alle macchine virtuali di Azure dopo il failover dall'ambiente localeConnect to Azure VMs after failover from on-premises 


Questo articolo descrive come configurare la connettività in modo da potersi connettere correttamente alle macchine virtuali di Azure dopo il failover.

Quando si configura il ripristino di emergenza di macchine virtuali locali e server fisici in Azure, Azure Site Recovery avvia la replica delle macchine in Azure.When you set up disaster recovery of on-premises virtual machines (VMs) and physical servers to Azure, [Azure Site Recovery](site-recovery-overview.md) starts replicating machines to Azure. Quindi, quando si verificano interruzioni, è possibile eseguire il failover in Azure dal sito locale. Quando si verifica il failover, Site Recovery crea macchine virtuali di Azure usando i dati locali replicati. Come parte della pianificazione del ripristino di emergenza, è necessario capire come connettersi alle app in esecuzione in queste macchine virtuali di Azure dopo il failover.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Preparare i computer locali prima del failover.
> * Preparare le macchine virtuali di Azure dopo il failover. 
> * Mantenere gli indirizzi IP nelle macchine virtuali di Azure dopo il failover.
> * Assegnare nuovi indirizzi IP alle macchine virtuali di Azure dopo il failover.

## <a name="prepare-on-premises-machines"></a>Preparare i computer localiPrepare on-premises machines

Per garantire la connettività alle macchine virtuali di Azure, preparare le macchine locali prima del failover.

### <a name="prepare-windows-machines"></a>Preparare i computer Windows

Nei computer Windows locali eseguire questa procedura:

1. Configurare le impostazioni di Windows. Tra queste sono la rimozione di route permanenti statiche o proxy WinHTTP e l'impostazione dei criteri SAN del disco **su OnlineAll**. [Seguire](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) queste istruzioni.

2. Assicurarsi che [questi servizi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) siano in esecuzione.

3. Abilitare Desktop remoto (RDP) per consentire le connessioni remote al computer locale. [Informazioni](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) su come abilitare RDP con PowerShell.

4. Per accedere a una macchina virtuale di Azure tramite Internet dopo il failover, in Windows Firewall nel computer locale consentire TCP e UDP nel profilo pubblico e impostare RDP come app consentita per tutti i profili.

5. Se si vuole accedere a una macchina virtuale di Azure tramite una VPN da sito a sito dopo il failover, in Windows Firewall nel computer locale consentire RDP per i profili dominio e privato. [Informazioni](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) su come consentire il traffico RDP.
6. Assicurarsi che non siano presenti aggiornamenti di Windows in sospeso nella macchina virtuale locale quando si attiva un failover. Se sono presenti, gli aggiornamenti potrebbero avviare l'installazione nella macchina virtuale di Azure dopo il failover e non sarà possibile accedere alla macchina virtuale fino al completamento degli aggiornamenti.

### <a name="prepare-linux-machines"></a>Preparare i computer Linux

Nei computer Linux locali eseguire questa procedura:

1. Assicurarsi che il servizio Secure Shell sia impostato per l'avvio automatico all'avvio del sistema.
2. Controllare che le regole del firewall consentano una connessione SSH.


## <a name="configure-azure-vms-after-failover"></a>Configurare le macchine virtuali di Azure dopo il failoverConfigure Azure VMs after failover

Dopo il failover, eseguire le operazioni seguenti nelle macchine virtuali di Azure create.

1. Per connettersi alla macchina virtuale tramite Internet, assegnare alla macchina virtuale un indirizzo IP pubblico. Per la macchina virtuale di Azure non è possibile usare lo stesso indirizzo IP pubblico usato in precedenza per il computer locale. [Scopri di più](../virtual-network/virtual-network-public-ip-address.md)
2. Verificare che le regole del gruppo di sicurezza di rete nella macchina virtuale consentano le connessioni in ingresso alla porta RDP o SSH.
3. Controllare la [Diagnostica di avvio](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) per visualizzare la macchina virtuale.


> [!NOTE]
> Il servizio Azure Bastion offre accesso privato RDP e SSH alle macchine virtuali di Azure. [Altre informazioni](../bastion/bastion-overview.md) su questo servizio.

## <a name="set-a-public-ip-address"></a>Impostare un indirizzo IP pubblico

In alternativa all'assegnazione manuale di un indirizzo IP pubblico a una macchina virtuale di Azure, è possibile assegnare l'indirizzo durante il failover usando uno script o un runbook di automazione di Azure in un piano di ripristino di Site Recovery oppure è possibile configurare il routing a livello di DNS tramite Gestione traffico di Azure.As an alternative to assigning a Public IP address manually to an Azure VM, you can assign the address during failover using a script or Azure automation runbook in a Site Recovery [recovery plan](site-recovery-create-recovery-plans.md), or you can set up DNS-level routing using Azure Traffic Manager. [Ulteriori informazioni](concepts-public-ip-address-with-site-recovery.md) sulla configurazione di un indirizzo pubblico.


## <a name="assign-an-internal-address"></a>Assegnare un indirizzo interno

Per impostare l'indirizzo IP interno di una macchina virtuale di Azure dopo il failover, sono disponibili due opzioni:To set the internal IP address of an Azure VM after failover, you have a couple of options:

- **Mantieni lo stesso indirizzo IP:** è possibile usare lo stesso indirizzo IP nella macchina virtuale di Azure di quella allocata alla macchina locale.
- **Usa indirizzo IP diverso:** è possibile usare un indirizzo IP diverso per la macchina virtuale di Azure.Use different IP address : You can use a different IP address for the Azure VM.


## <a name="retain-ip-addresses"></a>Mantenere gli indirizzi IP

Site Recovery consente di mantenere gli stessi indirizzi IP durante il failover in Azure.Site Recovery lets you retain the same IP addresses when failingover to Azure. Mantenere lo stesso indirizzo IP evita potenziali problemi di rete dopo il failover, ma introduce una certa complessità.

- Se la macchina virtuale di Azure di destinazione usa lo stesso indirizzo IP/subnet del sito locale, non è possibile connettersi tra di essi usando una connessione VPN da sito a sito o ExpressRoute, a causa della sovrapposizione degli indirizzi. Le subnet devono essere univoche.
- È necessaria una connessione da locale ad Azure dopo il failover, in modo che le app siano disponibili nelle macchine virtuali di Azure.You need a connection from on-premises to Azure after failover, so that apps are available on Azure VMs. Azure non supporta le VLAN estese, pertanto se si desidera mantenere gli indirizzi IP è necessario portare lo spazio IP in Azure eseguendo il failover dell'intera subnet, oltre al computer locale.
- Subnet failover ensures that a specific subnet isn't available simultaneously on-premises and in Azure.

La conservazione degli indirizzi IP richiede i passaggi seguenti:

- Nelle proprietà Calcolo & rete dell'elemento replicato impostare l'indirizzamento di rete e IP per la macchina virtuale di Azure di destinazione per il mirroring dell'impostazione locale.
- Le subnet devono essere gestite come parte del processo di ripristino di emergenza. È necessaria una rete virtuale di Azure in modo che corrisponda alla rete locale e dopo il failover le route di rete devono essere modificate per riflettere che la subnet è stata spostata in Azure e nuovi percorsi di indirizzi IP.  

### <a name="failover-example"></a>Esempio di failover

Ecco un esempio.

- La società fittizia Woodgrove Bank ospita le app aziendali locali Ospitano le app per dispositivi mobili in Azure.The fictitious company Woodgrove Bank hosts their business apps on-premises They host their mobile apps in Azure.
- Si connettono da locale ad Azure tramite VPN da sito a sito. 
- Woodgrove is using Site Recovery to replicate on-premises machines to Azure.
- Le app locali usano indirizzi IP hardcoded, pertanto desiderano mantenere gli stessi indirizzi IP in Azure.Their on-premises apps use hard-coded IP addresses, so they want to retain the same IP addresses in Azure.
- I computer locali che eseguono le app sono in esecuzione in tre subnet:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Le app in esecuzione in Azure si trovano nella rete di Azure VNet Azure in due subnet:Their apps running in Azure are located in the Azure VNet **Azure Network** in two subnets:
- 172.16.1.0/24
- 172.16.2.0/24.

Al fine di mantenere gli indirizzi, ecco cosa fanno.

1. Quando abilitano la replica, specificano che i computer devono eseguire la replica nella rete di **Azure.**
2. Creano **la rete di ripristino** in Azure.They create Recovery Network in Azure. Questa rete virtuale esegue il mirroring della subnet 192.168.1.0/24 nella rete locale.
3. Woodgrove configura una [connessione da vNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) tra le due reti. 

    > [!NOTE]
    > A seconda dei requisiti dell'applicazione, è possibile configurare una connessione da una rete virtuale a una rete virtuale prima del failover, come passaggio manuale o passaggio tramite script/runbook di automazione di Azure in un piano di [ripristino](site-recovery-create-recovery-plans.md)di Site Recovery oppure al termine del failover.

4. Prima del failover, nelle proprietà del computer in Site Recovery, impostano l'indirizzo IP di destinazione sull'indirizzo del computer locale, come descritto nella procedura successiva.
5. Dopo il failover, le macchine virtuali di Azure vengono create con lo stesso indirizzo IP. Woodgrove si connette da **Rete di Azure** a Rete virtuale di **ripristino** tramite il peering della rete virtuale (con connettività di transito abilitata).
6. In locale, Woodgrove deve apportare modifiche alla rete, inclusa la modifica delle route per riflettere che 192.168.1.0/24 è stato spostato in Azure.On-premises, Woodgrove needs to make network changes, including modifying routes to reflect that 192.168.1.0/24 has moved to Azure.  

**Infrastruttura prima del failover**

![Prima del failover sulla subnet](./media/site-recovery-network-design/network-design7.png)


**Infrastruttura dopo il failover**

![Dopo il failover sulla subnet](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Impostare le impostazioni di rete di destinazione

Prima del failover, specificare le impostazioni di rete e l'indirizzo IP per la macchina virtuale di Azure di destinazione.

1.  Nell'insieme di credenziali di Servizi di ripristino -> **elementi replicati**selezionare il computer locale.
2. Nella pagina **Calcolo e rete** del computer fare clic su **Modifica**per configurare le impostazioni di rete e della scheda per la macchina virtuale di Azure di destinazione.
3. In **Proprietà di rete**selezionare la rete di destinazione in cui si troverà la macchina virtuale di Azure quando viene creata dopo il failover.
4. In **Interfacce**di rete configurare le schede di rete nella rete di destinazione. Per impostazione predefinita, Site Recovery mostra tutte le schede di interfaccia di rete rilevate nel computer locale.
    - In **Tipo di interfaccia** di rete di destinazione è possibile impostare ogni scheda di interfaccia di rete come **Primaria**, **Secondaria**o **Non creare** se tale scheda di interfaccia di rete specifica non è necessaria nella rete di destinazione. Una scheda di rete deve essere impostata come primaria per il failover. Si noti che la modifica della rete di destinazione influisce su tutte le schede di interfaccia di rete per la macchina virtuale di Azure.Note that modifying the target network affects all NIC for the Azure VM.
    - Fare clic sul nome della scheda di interfaccia di rete per specificare la subnet in cui verrà distribuita la macchina virtuale di Azure.Click the NIC name to specify the subnet in which the Azure VM will be deployed.
    - Sovrascrivi **dinamico** con l'indirizzo IP privato che si vuole assegnare alla macchina virtuale di Azure di destinazione. Se non viene specificato un indirizzo IP Site Recovery, Site Recovery assegnerà il successivo indirizzo IP disponibile nella subnet alla scheda di interfaccia di rete al momento del failover.
    - [Altre informazioni](site-recovery-manage-network-interfaces-on-premises-to-azure.md) sulla gestione delle schede di interfaccia di rete per il failover locale in Azure.Learn more about managing NICs for on-premises failover to Azure.


## <a name="get-new-ip-addresses"></a>Ottenere nuovi indirizzi IPGet new IP addresses

In questo scenario, la macchina virtuale di Azure ottiene un nuovo indirizzo IP dopo il failover. Un aggiornamento DNS per aggiornare i record per i computer di cui è stato eseguito il failover in modo che punti all'indirizzo IP della macchina virtuale di Azure.A DNS update records to failed over machines to point to the IP address of the Azure VM.



## <a name="next-steps"></a>Passaggi successivi
[Informazioni sulla](site-recovery-active-directory.md) replica di Active Directory locale e DNS in Azure.Learn about replicating on-premises Active Directory and DNS to Azure.


