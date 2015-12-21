<properties 
   pageTitle="Indirizzi IP pubblici e privati in un provider di risorse di rete di Azure | Microsoft Azure"
   description="Informazioni sugli indirizzi IP pubblici e privati per un provider di risorse di rete in Gestione risorse di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Indirizzi IP in una rete virtuale di Azure
Questo articolo illustra l'impostazione degli indirizzi IP per macchine virtuali, servizi di bilanciamento del carico, gateway VPN e gateway app.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## Indirizzi IP pubblici
Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e altri servizi pubblici di Azure, ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache) e [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs). Un indirizzo IP pubblico è una risorsa indipendente che può essere associata a tipi diversi di risorse di Azure, ad esempio [macchine virtuali](virtual-machines-about.md) (VM) e [servizi di bilanciamento del carico](load-balancer-overview.md).

### Metodo di allocazione degli indirizzi IP pubblici
Gli indirizzi IP pubblici vengono allocati da un pool di indirizzi IP disponibili in una determinata posizione. L'elenco completo di intervalli di indirizzi IP usati dai data center di Microsoft Azure è disponibile in [Intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Gli indirizzi IP vengono allocati a una risorsa IP pubblico con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è quello *dinamico*. Con il metodo *dinamico*, un indirizzo IP **non** viene allocato alla *risorsa indirizzo IP pubblico* al momento della creazione. Quando viene creata o avviata una risorsa (ad esempio, una VM o un servizio di bilanciamento del carico) associata alla *risorsa indirizzo IP pubblico*, viene allocato un indirizzo IP del pool di indirizzi IP. Questo indirizzo IP viene deallocato e rilasciato al pool disponibile quando la risorsa associata viene eliminata o arrestata.

Se si usa il metodo di allocazione *statico*, un indirizzo IP viene allocato alla *risorsa indirizzo IP pubblico* al momento della creazione. In questo caso, indipendentemente dallo stato della risorsa associata, rimane allocato lo stesso indirizzo IP. Viene rilasciato al pool disponibile solo quando la *risorsa indirizzo IP pubblico* viene eliminata o il metodo di allocazione viene sostituito da quello *dinamico*.

### Risoluzione DNS
Per una risorsa IP pubblico può essere specificata un'etichetta del nome di dominio DNS, che crea una voce DNS corrispondente nei server DNS di Azure. Il corrispondente FQDN *etichettanomedominio*.*posizione*.cloudapp.azure.com sarà risolvibile a livello globale nell'indirizzo IP allocato alla risorsa IP pubblico.

Di seguito vengono esaminati i diversi tipi di risorsa che possono essere associati a indirizzo IP pubblico.

### Macchina virtuale
Un indirizzo IP pubblico viene associato a una [macchina virtuale](virtual-machines-about.md) (VM) con una scheda di interfaccia di rete. Ogni VM può avere un solo indirizzo IP pubblico indipendentemente dal numero di schede di interfaccia di rete associate. Solo una *risorsa indirizzo IP pubblico* allocata *in modo dinamico* può essere associata a una scheda di interfaccia di rete di una VM. In caso di una VM con più schede di interfaccia di rete, la *risorsa indirizzo IP pubblico* può essere associata solo alla scheda di interfaccia di rete principale.

### Servizio di bilanciamento del carico di Azure
Un indirizzo IP pubblico può essere associato a una configurazione front-end del [servizio di bilanciamento del carico di Azure](load-balancer-overview.md), che funge da indirizzo IP virtuale (indirizzo VIP) con carico bilanciato raggiungibile tramite Internet. A un servizio di bilanciamento del carico possono essere associate risorse IP pubblico sia *dinamico* che *statico*. È possibile associare più *risorse indirizzo IP pubblico* a una configurazione front-end del servizio di bilanciamento del carico, consentendo così scenari come quello di un ambiente multi-tenant con più siti Web basati su SSL.

### Gateway applicazione
In indirizzo IP pubblico può essere associato a una configurazione front-end del [gateway applicazione di Azure](application-gateway-introduction.md), per configurarlo con un IP virtuale (indirizzo VIP) con servizio di bilanciamento del carico raggiungibile tramite Internet. Attualmente solo una risorsa indirizzo IP pubblico allocata *in modo dinamico* può essere associata a un gateway applicazione. È tuttavia possibile associare più indirizzi IP pubblici.

### Gateway VPN
Un indirizzo IP pubblico deve essere associato a una configurazione IP del [gateway VPN di Azure](vpn-gateway-about-vpngateways.md), durante il processo di creazione della connessione VPN tra una rete virtuale di Azure e una rete locale o un'altra rete virtuale di Azure. Attualmente solo una risorsa indirizzo IP pubblico allocata *in modo dinamico* può essere associata a un gateway VPN.

### Riepilogo

|Risorsa|Allocazione statica|Allocazione dinamica|Indirizzi IP multipli|
|---|---|---|---|
|Macchina virtuale (VM)/scheda di interfaccia di rete|Sì|No|No|
|Front-end del servizio di bilanciamento del carico|Sì|Sì|Sì|
|Front-end del gateway applicazione|Sì|No|Sì|
|Gateway VPN|Sì|No|No|

## Indirizzi IP privati
Gli indirizzi IP privati consentono la comunicazione tra risorse in una rete virtuale senza usare indirizzi IP raggiungibili tramite Internet. Questo indirizzo IP viene allocato dall'intervallo di indirizzi della subnet nella rete virtuale.

### Metodo di allocazione degli indirizzi IP privati
Gli indirizzi IP vengono allocati con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è quello *dinamico*, in base al quale gli indirizzi IP vengono allocati usando DHCP. In alternativa, il metodo di allocazione può essere specificato in modo esplicito come *statico* mentre si specifica un indirizzo IP. In questo caso, alla risorsa viene allocato l'indirizzo IP specificato purché sia compreso nell'intervallo di indirizzi della subnet e sia libero (non allocato a nessuna altra risorsa).

Un indirizzo IP privato può essere assegnato a diversi tipi di risorsa. Si noti che entrambi i metodi di allocazione (*statico* o *dinamico*) sono adatti a tutti questi tipi di risorsa.

### Macchina virtuale
Un indirizzo IP privato viene assegnato a una scheda di interfaccia di rete di una [macchina virtuale](virtual-machines-about.md) (VM). Ogni VM può avere tanti indirizzi IP privati quante sono le schede di interfaccia di rete associate.

#### Risoluzione del nome host DNS interno
Tutte le VM di Azure vengono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution), se non specificato esplicitamente durante la creazione. Se si usano server DNS gestiti da Azure, viene automaticamente creato un record DNS che risolve il nome host della VM nell'indirizzo IP privato della VM. Se si usa una VM con più schede di interfaccia di rete, il nome host viene risolto nell'indirizzo IP privato della scheda di interfaccia di rete principale.

### Servizio di bilanciamento del carico interno
Un indirizzo IP privato può essere assegnato a un [servizio di bilanciamento del carico interno di Azure](load-balancer-internal-overview.md), che funge da indirizzo IP virtuale (indirizzo VIP) con bilanciamento del carico per le risorse nella rete virtuale. In questo modo sarà possibile usare il bilanciamento del carico senza esporre l'indirizzo IP a Internet.

### Gateway app
Un indirizzo IP privato può essere assegnato a un front-end del [gateway applicazione di Azure](application-gateway-introduction.md), per configurarlo con un endpoint interno non esposto a Internet, detto anche endpoint del servizio di bilanciamento del carico interno. Il comportamento e i metodi di allocazione sono simili a quelli del servizio di bilanciamento del carico interno descritto prima.

### Riepilogo
|Risorsa|Allocazione statica|Allocazione dinamica|Indirizzi IP multipli|
|---|---|---|---|
|Macchina virtuale (VM)/scheda di interfaccia di rete|Sì|Sì|Sì|
|Front-end del servizio di bilanciamento del carico|Sì|Sì|Sì|
|Front-end del gateway applicazione|Sì|Sì|Sì|

## Passaggi successivi


[Riferimento ad Azure PowerShell per le funzionalità di rete](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md)

[Rete di Azure: modelli di uso comune](https://github.com/Azure/azure-quickstart-templates)

[Provider di risorse di calcolo](../virtual-machines-azurerm-versus-azuresm)

[Panoramica di Gestione risorse di Azure](../resource-group-overview)

[Controllo degli accessi in base al ruolo in Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Uso dei tag in Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Distribuzioni modello](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->