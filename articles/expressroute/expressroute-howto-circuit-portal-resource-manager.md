<properties
   pageTitle="Creare e modificare un circuito ExpressRoute usando Resource Manager e il portale di Azure| Microsoft Azure"
   description="Questo articolo descrive le procedure di creazione, provisioning, verifica, aggiornamento, eliminazione e deprovisioning di un circuito ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="cherylmc"/>

# Creare e modificare un circuito ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

Questo articolo descrive la procedura di creazione di un circuito ExpressRoute di Azure usando il portale di Azure e il modello di distribuzione di Azure Resource Manager. I passaggi seguenti descrivono anche come controllare lo stato del circuito ed eseguirne l'aggiornamento, l'eliminazione e il deprovisioning.

**Informazioni sui modelli di distribuzione di Azure**About Azure deployment models

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Prima di iniziare


- Prima di iniziare la configurazione, leggere le pagine relative ai [prerequisiti](expressroute-prerequisites.md) e ai [flussi di lavoro](expressroute-workflows.md).
- Verificare di avere accesso al [portale di Azure](https://portal.azure.com).
- Verificare di avere le autorizzazioni necessarie per creare nuove risorse di rete. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account.

## Creare un circuito ExpressRoute ed eseguirne il provisioning

### 1\. Accedere al portale di Azure

In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

### 2\. Creare un nuovo circuito ExpressRoute

>[AZURE.IMPORTANT] Il circuito ExpressRoute viene addebitato dal momento in cui emessa una chiave di servizio. Verificare che operazione venga eseguita quando il provider di connettività è pronto a eseguire il provisioning del circuito.

- **Passaggio 1.** È possibile creare un circuito ExpressRoute selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **New** **>** **Networking** **>** **ExpressRoute**, come indicato nell'immagine che segue. 

	![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

- **Passaggio 2.** Dopo aver fatto clic su **ExpressRoute**, viene visualizzato il pannello **Create ExpressRoute circuit**. Quando si compila questo pannello verificare che siano specificati i valori corretti per il livello SKU e la misurazione dei dati.

	- Il **livello** determina se è abilitato un componente aggiuntivo ExpressRoute Standard o ExpressRoute Premium. È possibile specificare "Standard" per lo SKU standard o "Premium" per il componente aggiuntivo Premium.

	- La **misurazione dei dati** determina il tipo di fatturazione. È possibile specificare "Metered" per un piano dati a consumo e "Unlimited" per un piano dati illimitato. **Nota:** è possibile modificare il tipo di fatturazione da "Metered" a "Unlimited", ma non è possibile passare da "Unlimited" a "Metered".


		![](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)


### 3\. Visualizzare circuiti e proprietà

- **Per visualizzare tutti i circuiti** 
	
	È possibile visualizzare tutti i circuiti creati selezionando **All resources** dal menu a sinistra.

	![](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

- **Per visualizzare le proprietà**
	
	Selezionare il circuito per visualizzare le relative proprietà. In questo pannello è indicata la chiave di servizio per il circuito. È necessario copiare la chiave del circuito e passarla al provider del servizio per completare il processo di provisioning. La chiave del circuito è specifica per il circuito.

	![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### 4\. Inviare la chiave di servizio al provider di connettività per il provisioning

In questo pannello vengono visualizzate le informazioni del provider relative allo stato attuale del provisioning e le informazioni di Microsoft relative allo stato del circuito. Per altre informazioni sullo stato del provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:


Provider status : Not provisioned<BR> Circuit status : Enabled

![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)


Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

Provider status : Provisioning<BR> Circuit status : Enabled

Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

Provider status : Provisioned<BR> Circuit status : Enabled


### 5\. Controllare periodicamente lo stato e la condizione della chiave del circuito

Selezionare il circuito che interessa per visualizzare le relative proprietà. Controllare lo **stato del provider** e verificare che sia **Provisioned** prima di continuare.


![](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### 6\. Creare la configurazione di routing

Per istruzioni dettagliate, vedere la sezione relativa alla [configurazione di routing del circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare i peering del circuito.

>[AZURE.IMPORTANT] Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IPVPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

### 7\. Collegare una rete virtuale a un circuito ExpressRoute

Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegamento di reti virtuali a circuiti ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## Ottenere lo stato di un circuito ExpressRoute

Selezionare un circuito per visualizzarne lo stato.

![](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## Modifica di un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività. A questo punto, non è possibile modificare le proprietà del circuito ExpressRoute usando il portale di Azure. È tuttavia possibile usare PowerShell per modificare le proprietà del circuito. Vedere la sezione dell'articolo relativa alla [modifica di un circuito ExpressRoute con PowerShell](expressroute-howto-circuit-arm.md#modify).

È possibile eseguire le operazioni seguenti senza tempi di inattività:

- Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.

- Aumentare la larghezza di banda del circuito ExpressRoute. Si noti che il downgrade della larghezza di banda di un circuito non è supportato.

- Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Si noti che la modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.

-  È possibile abilitare e disabilitare l'opzione che consente le operazioni classiche

Per altre informazioni su limiti e limitazioni, vedere la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md).


## Eliminazione e deprovisioning di un circuito ExpressRoute

È possibile eliminare il circuito ExpressRoute selezionando l'icona di **eliminazione**. Tenere presente quanto segue:

- È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.

- Se lo stato di provisioning del provider di servizi del circuito ExpressRoute è abilitato, verrà modificato in "disabling". Collaborare con il provider di servizi per eseguire il deprovisioning del circuito su tale lato. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.

- Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider è impostato su "not provisioned", prima dell'esecuzione del cmdlet indicato sopra, Microsoft eseguirà il deprovisioning del circuito e interromperà la relativa fatturazione.

## Passaggi successivi

Dopo aver creato il circuito, verificare di eseguire le operazioni seguenti:

- [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
- [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0427_2016-->