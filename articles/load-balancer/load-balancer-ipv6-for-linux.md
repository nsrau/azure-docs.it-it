---
title: Configurare DHCPv6 per le macchine virtuali Linux | Documenti Microsoft
description: Come configurare DHCPv6 per VM Linux.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b46c2107dcfda5f02407e08daf08bd42d722dfda
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurare DHCPv6 per le macchine virtuali Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Alcune delle immagini di macchina virtuale di Linux in Azure Marketplace non dispone di Dynamic Host Configuration Protocol IPv6 (DHCPv6) configurato per impostazione predefinita. Per supportare IPv6, DHCPv6 deve essere configurato nella distribuzione del sistema operativo Linux che si sta utilizzando. Diverse distribuzioni di Linux configurare DHCPv6 in diversi modi, perché utilizzano pacchetti diversi.

> [!NOTE]
> Le immagini recenti di SUSE Linux e CoreOS in Azure Marketplace sono state preconfigurate con DHCPv6. Non aggiuntive sono necessarie modifiche quando si utilizzano queste immagini.

Questo documento descrive come abilitare DHCPv6 in modo che la macchina virtuale Linux ottenga un indirizzo IPv6.

> [!WARNING]
> Modificando il file di configurazione di rete in modo non corretto, è possibile perdere l'accesso alla rete per la macchina virtuale. È consigliabile testare le modifiche alla configurazione nei sistemi non di produzione. Le istruzioni riportate in questo articolo sono state testate sulle versioni più recenti delle immagini Linux in Azure Marketplace. Per istruzioni più dettagliate, consultare la documentazione per la propria versione di Linux.

## <a name="ubuntu"></a>Ubuntu

1. Modificare il */etc/dhcp/dhclient6.conf* file e aggiungere la riga seguente:

        timeout 10;

2. Modificare la configurazione della rete per l'interfaccia eth0 con la configurazione seguente:

   * In **Ubuntu 12.04 e 14.04**, modificare il */etc/network/interfaces.d/eth0.cfg* file. 
   * In **Ubuntu 16.04**, modificare il */etc/network/interfaces.d/50-cloud-init.cfg* file.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Modificare il */etc/dhcp/dhclient6.conf* file e aggiungere la riga seguente:

        timeout 10;

2. Modificare il */etc/network/interfaces* file e aggiungere la configurazione seguente:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, in CentOS e Oracle Linux

1. Modificare il */etc/sysconfig/network* file, quindi aggiungere il parametro seguente:

        NETWORKING_IPV6=yes

2. Modificare il */etc/sysconfig/network-scripts/ifcfg-eth0* file e aggiungere i due parametri seguenti:

        IPV6INIT=yes
        DHCPV6C=yes

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e 13 openSUSE

Sono state configurate in precedenza con DHCPv6 recenti SUSE Linux Enterprise Server (SLES) e openSUSE immagini in Azure. Non aggiuntive sono necessarie modifiche quando si utilizzano queste immagini. Se si dispone di una macchina virtuale basata su un'immagine SUSE precedenti o personalizzata, eseguire le operazioni seguenti:

1. Installare il pacchetto `dhcp-client` , se necessario:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Modificare il */etc/sysconfig/network/ifcfg-eth0* file, quindi aggiungere il parametro seguente:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE Leap

Le immagini recenti di SLES e openSUSE in Azure sono state preconfigurate con DHCPv6. Non aggiuntive sono necessarie modifiche quando si utilizzano queste immagini. Se si dispone di una macchina virtuale basata su un'immagine SUSE precedenti o personalizzata, eseguire le operazioni seguenti:

1. Modificare il */etc/sysconfig/network/ifcfg-eth0* file e sostituire il `#BOOTPROTO='dhcp4'` parametro con il valore seguente:

        BOOTPROTO='dhcp'

2. Per il */etc/sysconfig/network/ifcfg-eth0* file, aggiungere il parametro seguente:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Le immagini recenti di CoreOS in Azure sono state preconfigurate con DHCPv6. Non aggiuntive sono necessarie modifiche quando si utilizzano queste immagini. Se si dispone di una macchina virtuale in base a un'immagine di CoreOS precedenti o personalizzata, eseguire le operazioni seguenti:

1. Modificare il */etc/systemd/network/10_dhcp.network* file:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
