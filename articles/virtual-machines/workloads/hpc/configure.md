---
title: High Performance Computing - macchine virtuali di Azure | Microsoft Docs
description: Informazioni su High Performance Computing in Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707818"
---
# <a name="optimization-for-linux"></a>Ottimizzazione per Linux

Questo articolo illustra alcune tecniche principali per ottimizzare l'immagine del sistema operativo. Altre informazioni sulle [abilitazione InfiniBand](enable-infiniband.md) e l'ottimizzazione di immagini del sistema operativo.

## <a name="update-lis"></a>Aggiornamento LIS

Se la distribuzione usando un'immagine personalizzata (ad esempio, un sistema operativo precedente, ad esempio CentOS/RHEL 7.4 o 7.5), aggiornare LIS nella VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recuperare la memoria

Migliorare l'efficienza con automaticamente il recupero della memoria per evitare l'accesso a memoria remota.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Per rendere questo persistenti dopo il riavvio della macchina virtuale:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Disabilitare SELinux e firewall

Disabilitare SELinux e firewall.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Disabilitare cpupower

Disabilitare cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [abilitazione InfiniBand](enable-infiniband.md) e ottimizzare le immagini del sistema operativo.

* Altre informazioni sulle [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
