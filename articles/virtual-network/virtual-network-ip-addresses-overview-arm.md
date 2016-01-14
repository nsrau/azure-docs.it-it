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
   ms.date="12/14/2015"
   ms.author="telmos" />

# Indirizzi IP in Gestione risorse di Azure
È possibile assegnare indirizzi IP alle risorse di Azure per comunicare con altre risorse di Azure, con la rete locale e Internet. Sono disponibili due tipi di indirizzi IP che è possibile usare in Azure: pubblici e privati.

Gli indirizzi IP pubblici consentono di comunicare con Internet e con i servizi pubblici di Azure.

Gli indirizzi IP privati vengono usati per la comunicazione all'interno di una rete virtuale Azure (VNet) e della rete locale quando si usa un gateway VPN o un circuito ExpressRoute per estendere la rete ad Azure.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [Modello di distribuzione classica](virtual-network-ip-addresses-overview-classic.md).

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
- Le risorse di Azure comunicano con altri servizi Web che usano il modello di sicurezza basato su IP.
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

## Confronto tra distribuzioni con Gestione risorse e il modello classico
Di seguito è riportato un confronto tra l'indirizzo IP in Gestione risorse e il modello di distribuzione classico.

|| Risorsa| Classico| Gestione risorse|
|---|---|---|---|
|**Indirizzo IP pubblico**|VM|Definito come un ILPIP (solo dinamico)|Definito come un indirizzo IP pubblico (dinamico o statico)|
|||Assegnato a una VM IaaS o a un'istanza del ruolo PaaS|Associato alla scheda di interfaccia di rete della VM|
||Servizio di bilanciamento del carico con connessione Internet|Definito indirizzo VIP (dinamico) o indirizzo IP riservato (statico)|Definito come un indirizzo IP pubblico (dinamico o statico|
||| Assegnato a un servizio cloud|Associato alla configurazione front-end del servizio di bilanciamento del carico|
||||
|**Indirizzo IP privato**|VM|Definito come DIP|Definito come indirizzo di IP privato|
|||Assegnato a una VM IaaS o a un'instanza del ruolo PaaS|Assegnato a una scheda di interfaccia di rete della macchina virtuale|
||Servizio di bilanciamento del carico interno|Assegnato al servizio di bilanciamento del carico interno (statico o dinamico)|Assegnato alla configurazione front-end del servizio di bilanciamento del carico interno (dinamico o statico)|

## Passaggi successivi
- [Distribuire una macchina virtuale con un indirizzo IP pubblico statico](virtual-network-deploy-static-pip-arm-template.md)
- [Creare un indirizzo IP pubblico per un servizio di bilanciamento del carico con connessione Internet tramite l'interfaccia della riga di comando di Azure](load-balancer-get-started-internet-arm-cli.md#create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool)
- [Creare un indirizzo IP pubblico per un gateway applicazione usando PowerShell](application-gateway-create-gateway-arm.md#create-public-ip-address-for-front-end-configuration)
- [Creare un indirizzo IP pubblico per un gateway VPN usando PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md#4-request-a-public-ip-address-for-the-gateway)
- [Distribuire una macchina virtuale con un indirizzo IP privato statico](virtual-networks-static-private-ip-arm-pportal.md)
- [Creare un indirizzo IP privato statico front-end per un servizio di bilanciamento del carico interno tramite PowerShell](load-balancer-get-started-ilb-arm-ps.md#create-front-end-ip-pool-and-backend-address-pool)
- [Creare un pool back-end con indirizzi IP statici privati per un gateway applicazione usando PowerShell](application-gateway-create-gateway-arm.md#create-an-application-gateway-configuration-object)

<!---HONumber=AcomDC_1223_2015-->

