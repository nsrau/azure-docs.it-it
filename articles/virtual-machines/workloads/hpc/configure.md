---
title: Calcolo ad alte prestazioni-macchine virtuali di Azure | Microsoft Docs
description: Informazioni sul calcolo ad alte prestazioni in Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707818"
---
# <a name="optimization-for-linux"></a>Ottimizzazione per Linux

Questo articolo illustra alcune tecniche chiave per ottimizzare l'immagine del sistema operativo. Altre informazioni su come [abilitare InfiniBand](enable-infiniband.md) e ottimizzare le immagini del sistema operativo.

## <a name="update-lis"></a>Aggiorna LIS

Se si esegue la distribuzione usando un'immagine personalizzata, ad esempio un sistema operativo meno recente, ad esempio CentOS/RHEL 7,4 o 7,5, aggiornare LIS nella macchina virtuale.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recupera memoria

Migliorare l'efficienza recuperando automaticamente la memoria per evitare l'accesso remoto alla memoria.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Per renderlo permanente dopo il riavvio della macchina virtuale:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Disabilitare il firewall e SELinux

Disabilitare il firewall e SELinux.

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

* Altre informazioni su come [abilitare InfiniBand](enable-infiniband.md) e ottimizzare le immagini del sistema operativo.

* Scopri di più su [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) in Azure.
