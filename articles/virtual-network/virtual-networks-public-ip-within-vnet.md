<properties 
   pageTitle="Come usare gli indirizzi IP pubblici in una rete virtuale"
   description="Informazioni su come configurare una rete virtuale per l'uso di indirizzi IP pubblici"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/08/2015"
   ms.author="telmos" />

# Spazio degli indirizzi IP pubblico in una rete virtuale (VNet)

È ora possibile aggiungere uno spazio degli indirizzi IP pubblico alle proprie reti virtuali. In precedenza, alle reti virtuali era possibile aggiungere solo blocchi di indirizzi RFC 1918 (spazio privato). Quando si aggiunge un intervallo di indirizzi IP pubblici, tale intervallo verrà considerato come parte dello spazio degli indirizzi IP privato della rete virtuale raggiungibile solo all'interno della rete virtuale, delle reti virtuali interconnesse e dal percorso locale.

L'aggiunta di uno spazio degli indirizzi IP pubblico funziona concettualmente come illustrato di seguito:

![IP pubblico dal punto di vista concettuale](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## Come è possibile aggiungere un intervallo di indirizzi IP pubblici?

È possibile aggiungere un intervallo di indirizzi IP pubblici nello stesso modo in cui si aggiunge un intervallo di indirizzi IP privati, ovvero usando un file *netcfg* o eseguendo la configurazione nel portale. Un intervallo di indirizzi IP pubblici può essere aggiunto durante la creazione della rete virtuale o in un secondo momento. L'esempio seguente illustra uno spazio degli indirizzi IP sia pubblico che privato configurato nella stessa rete virtuale.

![Indirizzo IP pubblico nel portale](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## Esistono delle limitazioni?

Esistono alcuni intervalli di indirizzi IP che non sono consentiti:

- 224.0.0.0/4 (multicast)

- 255.255.255.255/32 (broadcast)

- 127.0.0.0/8 (loopback)

- 169.254.0.0/16 (locale rispetto al collegamento)

- 68.63.129.16/32 (DNS interno)

## Passaggi successivi

[Come gestire le proprietà della rete virtuale (VNet)](../virtual-networks-settings)

[Come gestire i server DNS usati da una rete virtuale (VNet)](../virtual-networks-manage-dns-in-vnet)

[Come eliminare una rete virtuale (VNet)](../virtual-networks-delete-vnet)

<!---HONumber=62-->