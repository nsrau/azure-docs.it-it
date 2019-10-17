---
title: Connettersi alle macchine virtuali di Azure dopo il failover da locale ad Azure con Azure Site Recovery
description: Descrive come connettersi alle macchine virtuali di Azure dopo il failover da locale ad Azure usando Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f535a681ac3508aafc2823bcc9b9ae7f22cc2d8e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333037"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Connettersi alle macchine virtuali di Azure dopo il failover da locale 


Questo articolo descrive come configurare la connettività per potersi connettere correttamente alle macchine virtuali di Azure dopo il failover.

Quando si configura il ripristino di emergenza di macchine virtuali (VM) locali e server fisici in Azure, [Azure Site Recovery](site-recovery-overview.md) avvia la replica di computer in Azure. Quindi, quando si verificano interruzioni, è possibile eseguire il failover in Azure dal sito locale. Quando si verifica il failover, Site Recovery crea macchine virtuali di Azure usando dati locali replicati. Come parte della pianificazione del ripristino di emergenza, è necessario capire come connettersi alle app in esecuzione in queste macchine virtuali di Azure dopo il failover.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Preparare i computer locali prima del failover.
> * Preparare le macchine virtuali di Azure dopo il failover. 
> * Mantenere gli indirizzi IP nelle VM di Azure dopo il failover.
> * Assegnare nuovi indirizzi IP alle macchine virtuali di Azure dopo il failover.

## <a name="prepare-on-premises-machines"></a>Preparare i computer locali

Per garantire la connettività alle VM di Azure, preparare i computer locali prima del failover.

### <a name="prepare-windows-machines"></a>Preparare i computer Windows

Nei computer Windows locali eseguire questa procedura:

1. Configurare le impostazioni di Windows. Sono incluse la rimozione di route persistenti statiche o proxy WinHTTP e l'impostazione dei criteri SANdel disco su onlineal. [Seguire](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) queste istruzioni.

2. Assicurarsi che [questi servizi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) siano in esecuzione.

3. Abilitare Desktop remoto (RDP) per consentire le connessioni remote al computer locale. [Informazioni](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) su come abilitare RDP con PowerShell.

4. Per accedere a una macchina virtuale di Azure tramite Internet dopo il failover, in Windows Firewall nel computer locale, consentire TCP e UDP nel profilo pubblico e impostare RDP come app consentita per tutti i profili.

5. Se si vuole accedere a una macchina virtuale di Azure tramite una VPN da sito a sito dopo il failover, in Windows Firewall nel computer locale, consentire il protocollo RDP per il dominio e i profili privati. [Informazioni](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) su come consentire il traffico RDP.
6. Quando si attiva un failover, assicurarsi che non ci siano aggiornamenti di Windows in sospeso nella macchina virtuale locale. In tal caso, gli aggiornamenti potrebbero iniziare a installare nella macchina virtuale di Azure dopo il failover e non sarà possibile accedere alla macchina virtuale fino al completamento degli aggiornamenti.

### <a name="prepare-linux-machines"></a>Preparare i computer Linux

Nei computer Linux locali eseguire questa procedura:

1. Assicurarsi che il servizio Secure Shell sia impostato per l'avvio automatico all'avvio del sistema.
2. Controllare che le regole del firewall consentano una connessione SSH.


## <a name="configure-azure-vms-after-failover"></a>Configurare VM di Azure dopo il failover

Dopo il failover, eseguire le operazioni seguenti nelle macchine virtuali di Azure create.

1. Per connettersi alla macchina virtuale tramite Internet, assegnare alla macchina virtuale un indirizzo IP pubblico. Per la macchina virtuale di Azure non è possibile usare lo stesso indirizzo IP pubblico usato in precedenza per il computer locale. [Ulteriori informazioni](../virtual-network/virtual-network-public-ip-address.md)
2. Verificare che le regole del gruppo di sicurezza di rete nella macchina virtuale consentano le connessioni in ingresso alla porta RDP o SSH.
3. Controllare la [Diagnostica di avvio](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) per visualizzare la macchina virtuale.


> [!NOTE]
> Il servizio Azure Bastion offre accesso privato RDP e SSH alle macchine virtuali di Azure. [Altre informazioni](../bastion/bastion-overview.md) su questo servizio.

## <a name="set-a-public-ip-address"></a>Impostare un indirizzo IP pubblico

In alternativa all'assegnazione manuale di un indirizzo IP pubblico a una macchina virtuale di Azure, è possibile assegnare l'indirizzo durante il failover usando uno script o Runbook di automazione di Azure in un [piano di ripristino](site-recovery-create-recovery-plans.md)Site Recovery oppure è possibile configurare il routing a livello di DNS con gestione traffico di Azure. [Altre](concepts-public-ip-address-with-site-recovery.md) informazioni sulla configurazione di un indirizzo pubblico.


## <a name="assign-an-internal-address"></a>Assegnare un indirizzo interno

Per impostare l'indirizzo IP interno di una macchina virtuale di Azure dopo il failover, sono disponibili due opzioni:

- **Mantenere lo stesso indirizzo IP**: è possibile usare lo stesso indirizzo IP nella macchina virtuale di Azure come quello allocato al computer locale.
- **Usare un indirizzo IP diverso**: è possibile usare un indirizzo IP diverso per la macchina virtuale di Azure.


## <a name="retain-ip-addresses"></a>Mantenere gli indirizzi IP

Site Recovery consente di mantenere gli stessi indirizzi IP in caso di failover in Azure. Mantenere lo stesso indirizzo IP evita potenziali problemi di rete dopo il failover, ma introduce una certa complessità.

- Se la macchina virtuale di Azure di destinazione usa la stessa subnet o indirizzo IP del sito locale, non è possibile connettersi tra loro tramite una connessione VPN da sito a sito o ExpressRoute, a causa della sovrapposizione degli indirizzi. Le subnet devono essere univoche.
- È necessaria una connessione da locale ad Azure dopo il failover, in modo che le app siano disponibili nelle VM di Azure. Azure non supporta le VLAN estese, pertanto se si vuole mantenere gli indirizzi IP è necessario portare lo spazio IP in Azure eseguendo il failover dell'intera subnet, oltre al computer locale.
- Il failover della subnet garantisce che una subnet specifica non sia disponibile contemporaneamente in locale e in Azure.

Per mantenere gli indirizzi IP sono necessari i passaggi seguenti:

- Nelle proprietà di rete del & di calcolo dell'elemento replicato impostare la rete e gli indirizzi IP per la macchina virtuale di Azure di destinazione per eseguire il mirroring dell'impostazione locale.
- Le subnet devono essere gestite come parte del processo di ripristino di emergenza. È necessario disporre di una VNet di Azure in modo che corrisponda alla rete locale e dopo che le route di rete di failover devono essere modificate per indicare che la subnet è stata spostata in Azure e le nuove posizioni degli indirizzi IP.  

### <a name="failover-example"></a>Esempio di failover

Ecco un esempio.

- La società fittizia Woodgrove Bank ospita le app aziendali locali che ospitano le app per dispositivi mobili in Azure.
- Si connettono da locale ad Azure tramite VPN da sito a sito. 
- Woodgrove USA Site Recovery per replicare i computer locali in Azure.
- Le app locali usano indirizzi IP hardcoded, quindi vogliono mantenere gli stessi indirizzi IP in Azure.
- I computer locali che eseguono le app sono in esecuzione in tre subnet:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Le app in esecuzione in Azure si trovano nella **rete** Azure VNet di Azure in due subnet:
- 172.16.1.0/24
- 172.16.2.0/24.

Per mantenere gli indirizzi, ecco cosa accade.

1. Quando abilitano la replica, specificano che le macchine virtuali devono essere replicate nella **rete di Azure**.
2. Creano la **rete di ripristino** in Azure. Questo VNet riflette la subnet 192.168.1.0/24 nella rete locale.
3. Woodgrove configura una [connessione da VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) tra le due reti. 

    > [!NOTE]
    > A seconda dei requisiti dell'applicazione, è possibile configurare una connessione da VNet a VNet prima del failover, come passaggio manuale/passaggio con script/Runbook di automazione di Azure in un [piano di ripristino](site-recovery-create-recovery-plans.md)Site Recovery o dopo il completamento del failover.

4. Prima del failover, nelle proprietà del computer in Site Recovery, l'indirizzo IP di destinazione viene impostato sull'indirizzo del computer locale, come descritto nella procedura seguente.
5. Dopo il failover, le macchine virtuali di Azure vengono create con lo stesso indirizzo IP. Woodgrove si connette dalla **rete di Azure** alla rete di **ripristino** VNet usando il peering VNet (con connettività di transito abilitata).
6. In locale, Woodgrove deve apportare modifiche alla rete, inclusa la modifica delle route per indicare che 192.168.1.0/24 è stato spostato in Azure.  

**Infrastruttura prima del failover**

![Prima del failover sulla subnet](./media/site-recovery-network-design/network-design7.png)


**Infrastruttura dopo il failover**

![Dopo il failover sulla subnet](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Imposta impostazioni di rete di destinazione

Prima del failover, specificare le impostazioni di rete e l'indirizzo IP per la macchina virtuale di Azure di destinazione.

1.  Nell'insieme di credenziali di servizi di ripristino-> **gli elementi replicati**selezionare il computer locale.
2. Nella pagina **calcolo e rete** per il computer, fare clic su **modifica**per configurare le impostazioni di rete e scheda per la macchina virtuale di Azure di destinazione.
3. In **proprietà di rete**selezionare la rete di destinazione in cui si trova la macchina virtuale di Azure quando viene creata dopo il failover.
4. In **interfacce di rete**configurare le schede di rete nella rete di destinazione. Per impostazione predefinita Site Recovery Mostra tutte le schede di rete rilevate nel computer locale.
    - In **tipo di interfaccia di rete di destinazione** è possibile impostare ogni NIC come **primaria**, **secondaria**o non **creare** se non è necessaria una scheda di interfaccia di rete specifica nella rete di destinazione. Una scheda di rete deve essere impostata come primaria per il failover. Si noti che la modifica della rete di destinazione interessa tutte le schede di rete per la macchina virtuale di Azure.
    - Fare clic sul nome della scheda di interfaccia di rete per specificare la subnet in cui verrà distribuita la VM di Azure.
    - Sovrascrivere **Dynamic** con l'indirizzo IP privato che si vuole assegnare alla macchina virtuale di Azure di destinazione. Se non viene specificato un indirizzo IP Site Recovery assegnerà il successivo indirizzo IP disponibile nella subnet alla scheda di interfaccia di rete in fase di failover.
    - [Altre](site-recovery-manage-network-interfaces-on-premises-to-azure.md) informazioni sulla gestione delle schede di rete per il failover locale in Azure.


## <a name="get-new-ip-addresses"></a>Ottenere nuovi indirizzi IP

In questo scenario, la macchina virtuale di Azure ottiene un nuovo indirizzo IP dopo il failover. Un aggiornamento DNS per aggiornare i record per i computer di cui è stato eseguito il failover in modo che puntino all'indirizzo IP della macchina virtuale di Azure.



## <a name="next-steps"></a>Passaggi successivi
Informazioni [sulla](site-recovery-active-directory.md) replica di Active Directory locali e DNS in Azure.


