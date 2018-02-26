---
title: Configurare DHCPv6 per macchine virtuali Linux | Microsoft Docs
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2017
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurare DHCPv6 per macchine virtuali Linux

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Per alcune delle immagini di macchina virtuale Linux in Azure Marketplace, DHCPv6 non è configurato per impostazione predefinita. Per supportare IPv6, è necessario configurare DHCPv6 nella distribuzione del sistema operativo Linux usato. Le diverse distribuzioni Linux richiedono modalità diverse per configurare DHCPv6 perché usano pacchetti diversi.

> [!NOTE]
> Le immagini recenti di SUSE Linux e CoreOS in Azure Marketplace sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche.

Questo documento descrive come abilitare DHCPv6 in modo che la macchina virtuale Linux ottenga un indirizzo IPv6.

> [!WARNING]
> La modifica non corretta dei file di configurazione di rete può causare la perdita dell'accesso di rete alla macchina virtuale. È consigliabile testare le modifiche alla configurazione nei sistemi non di produzione. Le istruzioni riportate in questo articolo sono state testate sulle versioni più recenti delle immagini Linux in Azure Marketplace. Per istruzioni più dettagliate, consultare la documentazione per la versione di Linux usata.

## <a name="ubuntu"></a>Ubuntu

1. Modificare il file */etc/dhcp/dhclient6.conf* e aggiungere la riga seguente:

        timeout 10;

2. Modificare la configurazione della rete per l'interfaccia eth0 con la configurazione seguente:

   * In **Ubuntu 12.04 e 14.04** modificare il file */etc/network/interfaces.d/eth0.cfg*. 
   * In **Ubuntu 16.04** modificare il file */etc/network/interfaces.d/50-cloud-init.cfg*.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Modificare il file */etc/dhcp/dhclient6.conf* e aggiungere la riga seguente:

        timeout 10;

2. Modificare il file */etc/network/interfaces* e aggiungere la configurazione seguente:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Modificare il file */etc/sysconfig/network* e aggiungere il parametro seguente:

        NETWORKING_IPV6=yes

2. Modificare il file */etc/sysconfig/network-scripts/ifcfg-eth0* e aggiungere i due parametri seguenti:

        IPV6INIT=yes
        DHCPV6C=yes

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e openSUSE 13

Le immagini SLES (SUSE Linux Enterprise Server) e openSUSE recenti in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine SUSE personalizzata o precedente, eseguire le operazioni seguenti:

1. Installare il pacchetto `dhcp-client` , se necessario:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Modificare il file */etc/sysconfig/network/ifcfg-eth0* e aggiungere il parametro seguente:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE Leap

Le immagini recenti di SLES e openSUSE in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine SUSE personalizzata o precedente, eseguire le operazioni seguenti:

1. Modificare il file */etc/sysconfig/network/ifcfg-eth0* e sostituire il parametro `#BOOTPROTO='dhcp4'` con il valore seguente:

        BOOTPROTO='dhcp'

2. Aggiungere il parametro seguente al file */etc/sysconfig/network/ifcfg-eth0*:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Le immagini recenti di CoreOS in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine CoreOS personalizzata o precedente, eseguire le operazioni seguenti:

1. Modificare il file */etc/systemd/network/10_dhcp.network*:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
