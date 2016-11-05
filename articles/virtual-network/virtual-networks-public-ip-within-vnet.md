---
title: Come usare gli indirizzi IP pubblici in una rete virtuale
description: Informazioni su come configurare una rete virtuale per l'uso di indirizzi IP pubblici
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial

---
# Spazio degli indirizzi IP pubblico in una rete virtuale (VNet)
Le reti virtuali possono contenere spazi di indirizzi IP (blocchi di indirizzi RFC 1918) sia pubblici che privati. Quando si aggiunge un intervallo di indirizzi IP pubblici, tale intervallo verrà considerato come parte dello spazio degli indirizzi IP privato della rete virtuale raggiungibile solo all'interno della rete virtuale, delle reti virtuali interconnesse e dal percorso locale.

La figura seguente mostra una rete virtuale che include spazi degli indirizzi IP pubblici e privati.

![IP pubblico dal punto di vista concettuale](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Come è possibile aggiungere un intervallo di indirizzi IP pubblici?
È possibile aggiungere un intervallo di indirizzi IP pubblici nello stesso modo in cui si aggiunge un intervallo di indirizzi IP privati, usando un file *netcfg* o aggiungendo la configurazione nel [portale di Azure](http://portal.azure.com). Un intervallo di indirizzi IP pubblici può essere aggiunto durante la creazione della rete virtuale o in un secondo momento. L'esempio seguente illustra uno spazio degli indirizzi IP sia pubblico che privato configurato nella stessa rete virtuale.

![Indirizzo IP pubblico nel portale](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Esistono delle limitazioni?
Esistono alcuni intervalli di indirizzi IP che non sono consentiti:

* 224\.0.0.0/4 (multicast)
* 255\.255.255.255/32 (broadcast)
* 127\.0.0.0/8 (loopback)
* 169\.254.0.0/16 (locale rispetto al collegamento)
* 168\.63.129.16/32 (DNS interno)

## Passaggi successivi
[Gestire server DNS usati da una rete virtuale](virtual-networks-manage-dns-in-vnet.md)

<!---HONumber=AcomDC_0810_2016-->