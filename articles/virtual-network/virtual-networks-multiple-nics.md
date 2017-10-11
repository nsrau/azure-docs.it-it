---
title: "Creare una VM (classica) con più schede di interfaccia di rete con PowerShell | Documentazione Microsoft"
description: "Informazioni su come creare e configurare VM con più schede di interfaccia di rete usando PowerShell."
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 68ccc1cac22e593b099729fe68c6bee63df44d9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics"></a>Creare una VM (classica) con più schede di interfaccia di rete
È possibile creare macchine virtuali (VM) in Azure e collegare più interfacce di rete (NIC) a ciascuna delle macchine virtuali. L'uso di più schede di interfaccia di rete è un requisito per molti dispositivi virtuali di rete, ad esempio le soluzioni di ottimizzazione WAN e la distribuzione di applicazioni. Più schede di interfaccia di rete forniscono anche l'isolamento del traffico tra le schede.

![Più NIC per la macchina virtuale](./media/virtual-networks-multiple-nics/IC757773.png)

La figura illustra una VM con tre schede di interfaccia di rete, ciascuna connessa a una subnet diversa.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare Resource Manager per la maggior parte delle distribuzioni più recenti.

* L’indirizzo VIP con connessione Internet (distribuzioni classiche) è supportato solo sulla NIC "predefinita". Esiste un solo indirizzo VIP per l'indirizzo IP della NIC predefinita.
* Attualmente, gli indirizzi IP pubblici a livello di istanza (LPIP) (distribuzioni classiche) non sono supportati per le macchine virtuali a più NIC.
* L'ordine delle NIC all'interno della macchina virtuale sarà casuale e potrebbe cambiare con gli aggiornamenti dell'infrastruttura di Azure. Tuttavia, gli indirizzi IP e gli indirizzi MAC ethernet corrispondenti resteranno invariati. Si supponga, ad esempio, che **Eth1** abbia l'indirizzo IP 10.1.0.100 e l'indirizzo MAC 00-0D-3A-B0-39-0D; dopo un aggiornamento dell'infrastruttura di Azure e il riavvio, potrebbe essere modificato in **Eth2**, ma l'abbinamento di indirizzo IP e MAC resterà invariato. Quando un riavvio è eseguito dal cliente, l'ordine delle NIC rimane invariato.
* L'indirizzo di ciascuna NIC su ciascuna macchina virtuale deve trovarsi in una subnet, a più NIC in una singola macchina virtuale possono essere assegnati indirizzi che si trovano nella stessa subnet.
* Le dimensioni della macchina virtuale determinano il numero di NIC che è possibile creare per una macchina virtuale. Fare riferimento agli articoli sulle dimensioni delle VM in [Windows Server](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per determinare il numero di schede di interfacce di rete supportate da ciascuna dimensione di VM. 

## <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NGS)
In una distribuzione di Gestione risorse, qualsiasi NIC in una macchina virtuale può essere associata a un Gruppo di sicurezza di rete, incluse eventuali NIC in una macchina virtuale che presenta la funzionalità Multi-NIC abilitata. Se a una NIC viene assegnato un indirizzo all'interno di una subnet dove la subnet è associata a un Gruppo di sicurezza di rete, le regole del Gruppo di sicurezza di rete della subnet si applicano anche a tale NIC. Oltre alle subnet, è possibile associare ai Gruppi di sicurezza di rete anche una NIC.

Se una subnet è associata a un Gruppo di sicurezza di rete e una NIC all'interno di tale subnet è associata singolarmente a un Gruppo di sicurezza di rete, le regole del Gruppo di sicurezza di rete associato vengono applicate in **ordine flusso** , in base alla direzione del traffico in entrata e in uscita dalla NIC:

* **Il traffico in entrata** , la cui destinazione è la NIC in questione, passa innanzitutto attraverso la subnet, attivando le regole del Gruppo di sicurezza di rete della subnet, prima di passare nella NIC, attivando quindi le regole del Gruppo di sicurezza di rete della NIC.
* **Il traffico in uscita** , la cui origine è la NIC in questione, fuoriesce innanzitutto dalla subnet, attivando le regole del Gruppo di sicurezza di rete della NIC, prima di passare attraverso la sunet, attivando quindi le regole del Gruppo di sicurezza di rete della subnet.

Ulteriori informazioni su [Gruppi di sicurezza di rete](virtual-networks-nsg.md) e su come vengono applicati in base alle associazioni con le subnet, con le macchine virtuali e con le schede di rete.

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Come configurare una macchina virtuale Multi-NIC in una distribuzione classica
Le istruzioni seguenti consentono di creare una macchina virtuale Multi-NIC contenente 3 NIC: una NIC predefinita e due NIC aggiuntive. La procedura di configurazione consente di creare una macchina virtuale che verrà configurata in base al frammento del file di configurazione del servizio riportato di seguito:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Per tentare di eseguire i comandi PowerShell riportati nell’esempio sono necessari i seguenti prerequisiti.

* Una sottoscrizione di Azure.
* Una rete virtuale configurata. Per altre informazioni sulle reti virtuali, vedere [Panoramica di Rete virtuale](virtual-networks-overview.md) .
* La versione più recente di Azure PowerShell scaricata e installata. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Per creare una VM con più schede di interfaccia di rete, completare i passaggi seguenti immettendo ogni comando in una singola sessione di PowerShell:

1. Selezionare un'immagine di macchina virtuale dalla raccolta immagini della macchina virtuale di Azure. Le immagini cambiano frequentemente e sono disponibili per area geografica. L'immagine specificata nell'esempio riportato di seguito può cambiare o potrebbe non trovarsi nell’area desiderata, assicurarsi pertanto di specificare l'immagine necessaria.

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. Creare una configurazione di macchina virtuale.

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. Creare l’account di accesso dell’amministratore predefinito.

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. Aggiungere le schede NIC aggiuntive alla configurazione della macchina virtuale.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. Specificare la subnet e l’indirizzo IP per la NIC predefinita.

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. Creare la macchina virtuale nella rete virtuale.

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > La rete virtuale specificata deve essere già esistente (come indicato nei prerequisiti). Nell'esempio seguente viene specificata una rete virtuale denominata **MultiNIC-VNet**.
    >

## <a name="limitations"></a>Limitazioni
Quando si usano più schede di interfaccia di rete, sono applicabili le seguenti limitazioni:

* È necessario creare VM con più schede di interfaccia di rete nelle reti virtuali di Azure. Non è possibile configurare VM che non si trovano in reti virtuali con più schede di interfaccia di rete.
* Tutte le VM in un set di disponibilità devono usare più schede di interfaccia di rete o una singola scheda. Non è possibile combinare VM con più schede di interfaccia di rete e VM con una singola scheda di interfaccia di rete all'interno di un set di disponibilità. Le stesse regole sono valide per le VM in un servizio cloud. Per le VM con più schede di interfaccia di rete, non è obbligatorio che il numero di schede siano lo stesso, a condizione che ognuna disponga almeno di due schede.
* Non è possibile configurare una VM con una singola scheda di interfaccia di rete con più schede di interfaccia di rete (e viceversa) dopo la distribuzione, senza eliminarla e crearla di nuovo.

## <a name="secondary-nics-access-to-other-subnets"></a>Accesso NIC secondarie ad altre subnet
Per impostazione predefinita, le NIC secondarie non verranno configurate con un gateway predefinito, pertanto il flusso del traffico sulle NIC secondarie sarà limitato esclusivamente all’interno della stessa subnet. Se si desidera abilitare le NIC secondarie per la comunicazione all'esterno della propria subnet, si dovrà aggiungere una voce nella tabella di routing per configurare il gateway come descritto di seguito.

> [!NOTE]
> Le VM create prima di luglio 2015 potrebbero avere un gateway predefinito configurato per tutte le NIC. Il gateway predefinito per le NIC secondarie non verrà rimosso fino al riavvio delle VM. Nei sistemi operativi che utilizzano il modello di routing dell'host vulnerabile, ad esempio Linux, la connettività Internet può interrompersi se il traffico in entrata e in uscita utilizza NIC diverse.
> 

### <a name="configure-windows-vms"></a>Configurare le macchine virtuali Windows
Supponiamo di utilizzare una macchina virtuale di Windows con due NIC come indicato di seguito:

* Indirizzo IP primario della NIC: 192.168.1.4
* Indirizzo IP secondario della NIC: 192.168.2.5

La tabella di route IPv4 per questa macchina virtuale sarà analoga alla seguente:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Si noti che la route predefinita (0.0.0.0) è disponibile solo per la NIC primaria. Non sarà possibile accedere alle risorse all'esterno della subnet per la NIC secondaria, come indicato di seguito:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Per aggiungere una route predefinita nella NIC secondaria, attenersi alla procedura seguente:

1. Dal prompt dei comandi, eseguire il seguente comando per identificare il numero di indice per la NIC secondaria:
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. Si noti la seconda voce nella tabella, con un indice pari a 27 (in questo esempio).
3. Dal prompt dei comandi, eseguire il comando **aggiungere route** come illustrato di seguito. In questo esempio si specifica 192.168.2.1 come gateway predefinito per la NIC secondaria:
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. Per verificare la connettività, tornare al prompt dei comandi e provare a effettuare il ping a una subnet diversa dalla NIC secondaria come illustrato nell’esempio riportato di seguito:
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. È inoltre possibile controllare la tabella di route per controllare la route appena aggiunta, come illustrato di seguito:
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurare le macchine virtuali Linux
Per le macchine virtuali Linux, poiché è stato utilizzato il comportamento predefinito dell'host routing vulnerabile, è consigliabile che le schede NIC secondarie siano limitate ai flussi di traffico all'interno della stessa subnet. Tuttavia se alcune situazioni richiedono la connettività all'esterno della subnet, gli utenti devono attivare la “policy based routing” per fare in modo che il traffico in entrata e in uscita utilizzi la stessa scheda NIC.

## <a name="next-steps"></a>Passaggi successivi
* Distribuire [Macchine virtuali MultiNIC in uno scenario di applicazione a 2 livelli in una distribuzione di Gestione risorse](virtual-network-deploy-multinic-arm-template.md).
* Distribuire [Macchine virtuali MultiNIC in uno scenario di applicazione a 2 livelli in una distribuzione classica](virtual-network-deploy-multinic-classic-ps.md).

