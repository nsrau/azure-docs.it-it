<properties
   pageTitle="Indirizzi IP pubblici e privati in Gestione risorse di Azure | Microsoft Azure"
   description="Informazioni sugli indirizzi IP pubblici e privati in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/12/2015"
   ms.author="telmos" />

# Indirizzi IP in Azure
È possibile assegnare indirizzi IP alle risorse di Azure per comunicare con altre risorse di Azure, con la rete locale e Internet. Sono disponibili due tipi di indirizzi IP che è possibile usare in Azure: pubblici e privati.

Gli indirizzi IP pubblici consentono di comunicare con Internet e con i servizi pubblici di Azure.

Gli indirizzi IP privati vengono usati per la comunicazione all'interno di una rete virtuale Azure (VNet) e della rete locale quando si usa un gateway VPN o un circuito ExpressRoute per estendere la rete ad Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-ip-addresses-overview-classic.md).

Se si ha familiarità con il modello di distribuzione classico, verificare le [differenze tra gli indirizzi IP nella versione classica e in Gestione risorse](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments).

## Indirizzi IP pubblici
Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e i servizi pubblici di Azure, ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache), [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs), [Database SQL](sql-database-technical-overview.md) e [Archiviazione di Azure](storage-introduction.md).

In Gestione risorse di Azure un [indirizzo IP pubblico](resource-groups-networking.md#public-ip-address) è una risorsa che ha proprietà specifiche. È possibile associare una risorsa indirizzo IP pubblico con una qualsiasi delle risorse seguenti:

- VM
- Servizi di bilanciamento del carico con connessione Internet
- Gateway VPN
- Gateway di applicazione

### Metodo di allocazione
Gli indirizzi IP vengono allocati a una *risorsa IP pubblico* con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è *dinamico*, dove un indirizzo IP **non** viene allocato al momento della creazione. Al contrario, l'indirizzo IP pubblico viene allocato quando si avvia, o si crea, la risorsa associata, ad esempio il servizio di bilanciamento del carico o una macchina virtuale. L'indirizzo IP viene rilasciato quando si arresta o si elimina la risorsa. Di conseguenza, l'indirizzo IP viene modificato quando si arresta e si avvia una risorsa.

Per assicurare che l'indirizzo IP per la risorsa associata rimanga invariato, è possibile impostare in modo esplicito il metodo di allocazione su *statico*. In questo caso un indirizzo IP viene assegnato immediatamente. Viene rilasciato solo quando si elimina la risorsa o modifica il relativo metodo di allocazione in *dinamico*.

>[AZURE.NOTE]Anche quando si imposta il metodo di allocazione su *statico*, non è possibile specificare l'indirizzo IP effettivo assegnato alla *risorsa IP pubblica*. Viene invece allocato da un pool di indirizzi IP disponibili nel percorso di Azure in cui viene creata la risorsa.

Gli indirizzi IP pubblici statici sono comunemente usati negli scenari seguenti:

- Gli utenti finali hanno l'esigenza di aggiornare le regole del firewall per comunicare con le risorse di Azure.
- La risoluzione del nome DNS, in cui una modifica dell'indirizzo IP richiederebbe l'aggiornamento dei record A.
- Le risorse di Azure comunicano con altri servizi o altre app che usano il modello di sicurezza basato su indirizzi IP.
- Si usano certificati SSL collegati a un indirizzo IP.

>[AZURE.NOTE]L'elenco degli intervalli IP da cui gli indirizzi IP pubblici, statici o dinamici, vengono allocati alle risorse di Azure è pubblicato nell'articolo relativo agli [intervalli di indirizzi IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### Risoluzione del nome host DNS
È possibile specificare un'etichetta di nome di dominio DNS per una risorsa IP pubblica, che crea un mapping per *etichettanomedominio*.*percorso*.cloudapp.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. Ad esempio, se si crea una risorsa IP pubblica con **contoso** come un *etichettanomedominio* nel **percorso di** Azure *Stati Uniti occidentali*, il nome di dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** verrà risolto nell'indirizzo IP pubblico della risorsa. È possibile usare questo FQDN per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

>[AZURE.IMPORTANT]Ogni etichetta di nome di dominio creata deve essere univoca nella relativa posizione di Azure.

### VM
È possibile associare un indirizzo IP pubblico a una [macchina virtuale](virtual-machines-about.md) (VM) assegnandola alla relativa **scheda di interfaccia di rete**. In caso di una macchina virtuale con più schede di interfaccia di rete, è possibile assegnarla solo alla scheda di interfaccia di rete *primaria*. A una macchina virtuale è possibile assegnare un indirizzo IP pubblico statico o dinamico.

### Servizi di bilanciamento del carico con connessione Internet
È possibile associare un indirizzo IP pubblico a un [servizio di bilanciamento del carico di Azure](load-balancer-overview.md), assegnandolo alla configurazione **front-end** del servizio di bilanciamento del carico. Questo indirizzo IP pubblico viene usato come indirizzo IP virtuale (VIP) di bilanciamento del carico. A un servizio di bilanciamento del carico front-end è possibile assegnare un indirizzo IP pubblico statico o dinamico. È anche possibile assegnare più indirizzi IP pubblici a un servizio di bilanciamento del carico front-end, consentendo così scenari con [più indirizzi VIP](load-balancer-multivip.md) come un ambiente multi-tenant con siti Web basati su SSL.

### Gateway VPN
Il [gateway VPN di Azure](vpn-gateway-about-vpngateways.md) può essere usato per connettere una rete virtuale di Azure ad altre reti virtuali o locali di Azure. È necessario assegnare un indirizzo IP pubblico alla relativa **configurazione IP** per abilitare la comunicazione con la rete remota. Attualmente, è possibile assegnare solo un indirizzo IP pubblico dinamico a un gateway VPN.

### Gateway di applicazione
È possibile associare un indirizzo IP pubblico a un [gateway applicazione](application-gateway-introduction.md) di Azure, assegnandolo alla configurazione **front-end** del gateway. Questo indirizzo IP pubblico viene usato come indirizzo VIP con carico bilanciato. Attualmente, è possibile assegnare solo un indirizzo IP pubblico *dinamico* alla configurazione front-end di un gateway applicazione. È anche possibile assegnare più indirizzi IP pubblici per abilitare scenari con più indirizzi VIP.

### Riepilogo
La tabella seguente illustra ogni tipo di risorsa con i metodi di allocazione possibili, dinamico o statico, e la possibilità di assegnare più indirizzi IP pubblici.

|Risorsa|Dinamico|Statico|Più indirizzi IP|
|---|---|---|---|
|Scheda di interfaccia di rete di una macchina virtuale|Sì|Sì|No|
|Front-end del servizio di bilanciamento del carico|Sì|Sì|Sì|
|Gateway VPN|Sì|No|No|
|Front-end del gateway applicazione|Sì|No|No|

## Indirizzi IP privati
Gli indirizzi IP privati consentono alle risorse di Azure di comunicare con altre risorse in una [rete virtuale](virtual-networks-overview.md) o nella rete locale tramite un gateway VPN o il circuito ExpressRoute, senza usare un indirizzo IP raggiungibile tramite Internet.

Nel modello di distribuzione di Gestione risorse di Azure viene associato un indirizzo IP privato a diverse risorse di Azure.

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

### VM
Un indirizzo IP privato viene assegnato alla **scheda di interfaccia di rete** di una [macchina virtuale](virtual-machines-about.md). Nel caso di una macchina virtuale con più schede di interfaccia di rete, a ogni scheda viene assegnato un indirizzo IP privato. Per una scheda di interfaccia di rete è possibile specificare il metodo di allocazione statico o dinamico.

#### Risoluzione del nome host DNS interno per le macchine virtuali
Tutte le macchine virtuali di Azure sono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non si configurino in modo esplicito server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interna per le macchine virtuali che risiedono nella stessa rete virtuale.

Quando si crea una macchina virtuale, ai server DNS gestiti da Azure viene aggiunto un mapping del nome host al relativo indirizzo IP privato. Se si usa una VM con più schede di interfaccia di rete, viene eseguito il mapping del nome host all'indirizzo IP privato della scheda di interfaccia di rete primaria.

Le macchine virtuali configurate con server DNS gestiti di Azure potranno risolvere i nomi host di tutte le VM all'interno la rete virtuale nei relativi indirizzi IP privati.

### Servizi di bilanciamento del carico interno e gateway applicazione
È possibile assegnare un indirizzo IP privato alla configurazione **front-end** di un [servizio di bilanciamento del carico interno di Azure](load-balancer-internal-overview.md) o di un [gateway applicazione di Azure](application-gateway-introduction.md). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale e alle reti remote connesse alla rete virtuale. È possibile assegnare un indirizzo IP privato dinamico o statico alla configurazione front-end. È anche possibile assegnare più indirizzi IP privati per abilitare scenari con più indirizzi VIP.

### Riepilogo
La tabella seguente illustra ogni tipo di risorsa con i metodi di allocazione possibili, dinamico o statico, e la possibilità di assegnare più indirizzi IP privati.

|Risorsa|Statico|Dinamico|Più indirizzi IP|
|---|---|---|---|
|Macchina virtuale (VM)/scheda di interfaccia di rete|Sì|Sì|Sì|
|Front-end del servizio di bilanciamento del carico interno|Sì|Sì|Sì|
|Front-end del gateway applicazione|Sì|Sì|Sì|

## Limiti

La tabella seguente illustra i limiti imposti sugli indirizzi IP in Azure per ogni area e ogni sottoscrizione. È possibile [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai limiti massimi consentiti, in base alle esigenze aziendali.

||Limite predefinito|Limite massimo| |---|---|---| |Indirizzi IP pubblici (dinamici)|60|Contattare il supporto tecnico| |Indirizzi IP pubblici (statici)|20|Contattare il supporto tecnico| |IP del front-end pubblico per ogni servizio di bilanciamento del carico|5|Contattare il supporto tecnico| |IP del front-end privato per ogni servizio di bilanciamento del carico|1|Contattare il supporto tecnico|

Assicurarsi di aver letto l'elenco completo di [Limiti relativi alle reti](azure-subscription-service-limits.md#networking-limits) in Azure.

## Prezzi

Nella maggior parte dei casi, gli indirizzi IP pubblici sono gratuiti. È prevista una tariffa nominale per l'uso di indirizzi IP pubblici statici e/o aggiuntivi. Accertarsi di aver compreso la [struttura dei prezzi per gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/).

In sintesi, la struttura di prezzi seguenti si applica a risorse IP pubbliche:

- I gateway VPN e i gateway di applicazione usano solo un indirizzo IP pubblico dinamico gratuito.
- Le VM usano solo un indirizzo IP pubblico, che è gratuito solo se è un indirizzo IP dinamico. Se una VM usa un indirizzo IP pubblico statico, l'indirizzo IP verrà incluso nel conteggio relativo all'utilizzo di indirizzi IP pubblici (riservati) statici.
- Ogni servizio di bilanciamento del carico può usare più indirizzi IP pubblici. Il primo indirizzo IP pubblico è gratuito. Per ogni indirizzo IP dinamico aggiuntivo vengono addebitati $ 0,004/ora. Gli indirizzi IP pubblici statici vengono inclusi nel conteggio relativo all'utilizzo di indirizzi IP pubblici (riservati) statici.
- Utilizzo di indirizzi IP pubblici (riservati) statici: 
	- I primi 5 (in uso) sono gratuiti. Per ogni indirizzo IP statico aggiuntivo vengono addebitati $ 0,004/ora. 
	- Per ogni indirizzo IP pubblico statico non assegnato ad alcuna risorsa vengono addebitati $ 0,004/ora.
	- L'utilizzo viene calcolato in base al numero totale di indirizzi IP pubblici statici nella sottoscrizione.

## Passaggi successivi
- [Distribuire una VM con un IP pubblico statico](virtual-network-deploy-static-pip-arm-portal.md) tramite il portale di Azure.
- Scoprire come [distribuire una VM con un IP pubblico statico tramite un modello](virtual-network-deploy-static-pip-arm-template.md).
- [Distribuire una VM con un indirizzo IP privato statico](virtual-networks-static-private-ip-arm-pportal.md) tramite il portale di Azure.

<!---HONumber=AcomDC_0114_2016-->