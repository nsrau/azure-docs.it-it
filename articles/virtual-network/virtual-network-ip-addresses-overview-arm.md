<properties
   pageTitle="Indirizzi IP pubblici e privati in Gestione risorse di Azure | Microsoft Azure"
   description="Informazioni sugli indirizzi IP pubblici e privati in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />  
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />  

# Indirizzi IP in Azure
È possibile assegnare indirizzi IP alle risorse di Azure per comunicare con altre risorse di Azure, con la rete locale e Internet. In Azure è possibile usare due tipi di indirizzi IP:

- **Indirizzi IP pubblici**: usati per la comunicazione con Internet, compresi i servizi pubblici di Azure.
- **Indirizzi IP privati**: usati per la comunicazione all'interno di una rete virtuale Azure e della rete locale quando si usa un gateway VPN o un circuito ExpressRoute per estendere la rete ad Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

Se si ha familiarità con il modello di distribuzione classica, verificare le [differenze tra gli indirizzi IP nella versione classica e in Resource Manager](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## Indirizzi IP pubblici
Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e i servizi pubblici di Azure, ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache/), [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/), [Database SQL](../sql-database/sql-database-technical-overview.md) e [Archiviazione di Azure](../storage/storage-introduction.md).

In Gestione risorse di Azure un [indirizzo IP pubblico](resource-groups-networking.md#public-ip-address) è una risorsa che ha proprietà specifiche. È possibile associare una risorsa indirizzo IP pubblico con una qualsiasi delle risorse seguenti:

- Macchine virtuali (VM)
- Servizi di bilanciamento del carico con connessione Internet
- Gateway VPN
- Gateway di applicazione

### Metodo di allocazione
Gli indirizzi IP vengono allocati a una *risorsa IP pubblico* con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è *dinamico*, dove un indirizzo IP **non** viene allocato al momento della creazione. Al contrario, l'indirizzo IP pubblico viene allocato quando si avvia, o si crea, la risorsa associata, ad esempio il servizio di bilanciamento del carico o una VM. L'indirizzo IP viene rilasciato quando si arresta o si elimina la risorsa. Di conseguenza, l'indirizzo IP viene modificato quando si arresta e si avvia una risorsa.

Per assicurare che l'indirizzo IP per la risorsa associata rimanga invariato, è possibile impostare in modo esplicito il metodo di allocazione su *statico*. In questo caso un indirizzo IP viene assegnato immediatamente. Viene rilasciato solo quando si elimina la risorsa o modifica il relativo metodo di allocazione in *dinamico*.

>[AZURE.NOTE] Anche quando si imposta il metodo di allocazione su *statico*, non è possibile specificare l'indirizzo IP effettivo assegnato alla *risorsa IP pubblica*. Viene invece allocato da un pool di indirizzi IP disponibili nel percorso di Azure in cui viene creata la risorsa.

Gli indirizzi IP pubblici statici sono comunemente usati negli scenari seguenti:

- Gli utenti finali hanno l'esigenza di aggiornare le regole del firewall per comunicare con le risorse di Azure.
- La risoluzione del nome DNS, in cui una modifica dell'indirizzo IP richiederebbe l'aggiornamento dei record A.
- Le risorse di Azure comunicano con altri servizi o altre app che usano il modello di sicurezza basato su indirizzi IP.
- Si usano certificati SSL collegati a un indirizzo IP.

>[AZURE.NOTE] L'elenco degli intervalli IP da cui gli indirizzi IP pubblici, statici o dinamici, vengono allocati alle risorse di Azure è pubblicato nell'articolo relativo agli [intervalli di indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Risoluzione del nome host DNS
È possibile specificare un'etichetta di nome di dominio DNS per una risorsa IP pubblica, che crea un mapping per *etichettanomedominio*.*percorso*.cloudapp.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un *etichettanomedominio* nel **percorso di** Azure *Stati Uniti occidentali*, il nome di dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** verrà risolto nell'indirizzo IP pubblico della risorsa. È possibile usare questo FQDN per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

>[AZURE.IMPORTANT] Ogni etichetta di nome di dominio creata deve essere univoca nella relativa posizione di Azure.

### Macchine virtuali
È possibile associare un indirizzo IP pubblico a una VM [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) assegnandola alla relativa **interfaccia di rete**. Nel caso di una VM con più interfacce di rete, è possibile assegnare la VM solo all'interfaccia di rete *primaria*. A una macchina virtuale è possibile assegnare un indirizzo IP pubblico statico o dinamico.

### Servizi di bilanciamento del carico con connessione Internet
È possibile associare un indirizzo IP pubblico a un [Azure Load Balancer](../load-balancer/load-balancer-overview.md) assegnandolo alla configurazione **front-end** del servizio di bilanciamento del carico. Questo indirizzo IP pubblico viene usato come indirizzo IP virtuale (VIP) di bilanciamento del carico. A un servizio di bilanciamento del carico front-end è possibile assegnare un indirizzo IP pubblico statico o dinamico. È anche possibile assegnare più indirizzi IP pubblici a un front-end del servizio di bilanciamento del carico, consentendo così scenari con [più indirizzi VIP](../load-balancer/load-balancer-multivip.md), ad esempio un ambiente multi-tenant con siti Web basati su SSL.

### Gateway VPN
Il [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) può essere usato per connettere una rete virtuale di Azure ad altre reti virtuali o locali di Azure. È necessario assegnare un indirizzo IP pubblico alla relativa **configurazione IP** per abilitarne la comunicazione con la rete remota. Attualmente a un gateway VPN è possibile assegnare solo un indirizzo IP pubblico *dinamico*.

### Gateway di applicazione
È possibile associare un indirizzo IP pubblico a un [gateway applicazione](../application-gateway/application-gateway-introduction.md) di Azure, assegnandolo alla configurazione **front-end** del gateway. Questo indirizzo IP pubblico viene usato come indirizzo VIP con carico bilanciato. Attualmente alla configurazione front-end di un gateway applicazione è possibile assegnare solo un indirizzo IP pubblico *dinamico*.

### Riepilogo
La tabella seguente illustra la proprietà specifica tramite la quale un indirizzo IP pubblico può essere associato a una risorsa di livello superiore e i metodi di allocazione possibili (dinamici o statici) utilizzabili.

|Risorse di livello superiore|Associazione di indirizzi IP|Dinamico|Statico|
|---|---|---|---|
|Macchine virtuali|Interfaccia di rete|Sì|Sì|
|Bilanciamento del carico|Configurazione front-end|Sì|Sì|
|Gateway VPN|Configurazione IP del gateway|Sì|No|
|Gateway applicazione|Configurazione front-end|Sì|No|

## Indirizzi IP privati
Gli indirizzi IP privati consentono alle risorse di Azure di comunicare con altre risorse in una [rete virtuale](virtual-networks-overview.md) o in una rete locale tramite un gateway VPN o un circuito ExpressRoute, senza usare un indirizzo IP raggiungibile tramite Internet.

Nel modello di distribuzione Azure Resource Manager un indirizzo IP privato è associato ai tipi di risorse di Azure seguenti:

- VM
- Servizi di bilanciamento del carico interno
- Gateway di applicazione

### Metodo di allocazione
Un indirizzo IP privato viene allocato dall'intervallo di indirizzi della subnet a cui è collegata la risorsa. L'intervallo di indirizzi della subnet stessa fa parte dell'intervallo di indirizzi della rete virtuale.

Un indirizzo IP privato viene allocato con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è quello *dinamico*, in base al quale l'indirizzo IP viene allocato automaticamente dalla subnet della risorsa usando DHCP. Questo indirizzo IP può cambiare quando si arresta e avvia la risorsa.

È possibile impostare il metodo di allocazione su *statico* per garantire che l'indirizzo IP rimanga invariato. In questo caso, è necessario fornire anche un indirizzo IP valido che faccia parte della subnet della risorsa.

Gli indirizzi IP privati statici vengono comunemente usati per:

- Macchine virtuali che fungono da controller di dominio o server DNS.
- Risorse che richiedono regole del firewall basate su indirizzi IP.
- Risorse accessibili da altre app o risorse tramite un indirizzo IP.

### Macchine virtuali
Un indirizzo IP privato viene assegnato all'**interfaccia di rete** di una VM [Windows](../virtual-machines/virtual-machines-windows-about.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md). Nel caso di una macchina virtuale con più interfacce di rete, a ogni interfaccia viene assegnato un indirizzo IP privato. Per un'interfaccia di rete è possibile specificare un metodo di allocazione statico o dinamico.

#### Risoluzione del nome host DNS interno per le macchine virtuali
Tutte le macchine virtuali di Azure sono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non si configurino in modo esplicito server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interna per le macchine virtuali che risiedono nella stessa rete virtuale.

Quando si crea una macchina virtuale, ai server DNS gestiti da Azure viene aggiunto un mapping del nome host al relativo indirizzo IP privato. Se si usa una VM con più interfacce di rete, viene eseguito il mapping del nome host all'indirizzo IP privato dell'interfaccia di rete primaria.

Le macchine virtuali configurate con server DNS gestiti di Azure potranno risolvere i nomi host di tutte le VM all'interno la rete virtuale nei relativi indirizzi IP privati.

### Servizi di bilanciamento del carico interno e gateway applicazione
È possibile assegnare un indirizzo IP privato alla configurazione **front-end** di un [servizio di bilanciamento del carico interno di Azure](../load-balancer/load-balancer-internal-overview.md) o di un [gateway applicazione di Azure](../application-gateway/application-gateway-introduction.md). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale e alle reti remote connesse alla rete virtuale. È possibile assegnare un indirizzo IP privato dinamico o statico alla configurazione front-end.

### Riepilogo
La tabella seguente illustra la proprietà specifica tramite la quale un indirizzo IP privato può essere associato a una risorsa di livello superiore e i metodi di allocazione possibili (dinamici o statici) utilizzabili.

|Risorse di livello superiore|Associazione di indirizzi IP|Dinamico|Statico|
|---|---|---|---|
|Macchine virtuali|Interfaccia di rete|Sì|Sì|
|Bilanciamento del carico|Configurazione front-end|Sì|Sì|
|Gateway applicazione|Configurazione front-end|Sì|Sì|

## Limiti

I limiti imposti agli indirizzi IP sono indicati nel set completo di [limiti della rete](azure-subscription-service-limits.md#networking-limits) in Azure. Questi limiti sono classificati per area e per sottoscrizione. È possibile [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai massimi consentiti in base alle esigenze aziendali.

## Prezzi

Nella maggior parte dei casi, gli indirizzi IP pubblici sono gratuiti. È prevista una tariffa nominale per l'uso di indirizzi IP pubblici statici e/o aggiuntivi. Verificare di aver compreso la [struttura dei prezzi per gli IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/).

## Passaggi successivi
- [Distribuire una VM con un IP pubblico statico tramite il portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
- [Distribuire una VM con un IP pubblico statico tramite un modello](virtual-network-deploy-static-pip-arm-template.md)
- [Come impostare un indirizzo IP statico privato nel portale di Azure](virtual-networks-static-private-ip-arm-pportal.md)

<!---HONumber=AcomDC_0810_2016-->