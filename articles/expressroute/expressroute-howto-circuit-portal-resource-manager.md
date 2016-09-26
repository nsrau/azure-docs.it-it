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
   ms.date="08/29/2016"
   ms.author="cherylmc"/>  

# Creare e modificare un circuito ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

Questo articolo descrive la procedura di creazione di un circuito ExpressRoute di Azure usando il portale di Azure e il modello di distribuzione di Azure Resource Manager. I passaggi seguenti descrivono anche come controllare lo stato del circuito ed eseguirne l'aggiornamento, l'eliminazione e il deprovisioning.

**Informazioni sui modelli di distribuzione di AzureAbout Azure deployment models**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Prima di iniziare


- Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).
- Verificare di avere accesso al [portale di Azure](https://portal.azure.com).
- Verificare di avere le autorizzazioni necessarie per creare nuove risorse di rete. Se non si hanno le autorizzazioni appropriate, contattare l'amministratore dell'account.

## Creare un circuito ExpressRoute ed eseguirne il provisioning

### 1\. Accedere al portale di Azure

In un browser passare al [portale di Azure](http://portal.azure.com) e accedere con l'account Azure.

### 2\. Creare un nuovo circuito ExpressRoute

>[AZURE.IMPORTANT] Il circuito ExpressRoute viene addebitato dal momento in cui emessa una chiave di servizio. Verificare che l'operazione venga eseguita quando il provider di connettività è pronto a effettuare il provisioning del circuito.

1. È possibile creare un circuito ExpressRoute selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **Nuovo** > **Rete** > **ExpressRoute**, come indicato nell'immagine seguente:

	![Creare un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

2. Dopo aver fatto clic su **ExpressRoute**, verrà visualizzato il pannello **Create ExpressRoute circuit** (Crea circuito ExpressRoute). Quando si compila questo pannello, verificare che siano specificati i valori corretti per il livello SKU e la misurazione dei dati.

	- Il **livello** determina se è abilitato un componente aggiuntivo ExpressRoute Standard o ExpressRoute Premium. È possibile specificare **Standard** per ottenere lo SKU Standard o **Premium** per abilitare il componente aggiuntivo Premium.

	- La **misurazione dei dati** determina il tipo di fatturazione. È possibile specificare **Metered** (A consumo) per un piano dati a consumo e **Unlimited** (Senza limiti) per un piano dati illimitato. Si noti che è possibile modificare il tipo di fatturazione da **Metered** (A consumo) a **Unlimited** (Senza limiti), ma non è possibile effettuare il passaggio inverso.

	![Configurazione del livello SKU e misurazione dei dati](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)  

>[AZURE.IMPORTANT] Tenere presente che il percorso di Peering indica la [posizione fisica](expressroute-locations.md) in cui si esegue il peering con Microsoft. Questo percorso **non** è collegato alla proprietà "Location", ovvero all'area geografica in cui si trova il provider di risorse di rete di Azure. Dal momento che non sono collegati, è consigliabile scegliere un provider di risorse di rete geograficamente vicino alla posizione di peering del circuito.

### 3\. Visualizzare circuiti e proprietà

**Visualizzare tutti i circuiti**

È possibile visualizzare tutti i circuiti creati selezionando **Tutte le risorse** dal menu a sinistra.
	
![Visualizzazione dei circuiti](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visualizzare le proprietà**

	You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Visualizza proprietà](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### 4\. Inviare la chiave di servizio al provider di connettività per il provisioning

In questo pannello **Stato provider** offre informazioni sullo stato di provisioning corrente sul lato provider del servizio. **Stato circuito** indica lo stato sul lato Microsoft. Per altre informazioni sullo stato di provisioning dei circuiti, vedere l'articolo relativo ai [flussi di lavoro](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quando si crea un nuovo circuito ExpressRoute, il circuito ha lo stato seguente:

Stato provider: Senza provisioning<BR> Stato circuito: Abilitato

![Avvio del processo di provisioning](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Il circuito passa allo stato seguente quando è in corso l'abilitazione da parte del provider di connettività:

Stato provider: Provisioning in corso<BR> Stato circuito: Abilitato

Per poterlo usare, un circuito ExpressRoute deve avere lo stato seguente:

Stato provider: Provisioning eseguito<BR> Stato circuito: Abilitato


### 5\. Controllare periodicamente lo stato e la condizione della chiave del circuito

Selezionare il circuito desiderato per visualizzare le relative proprietà. Controllare **Stato provider** e verificare che sia impostato su **Provisioning eseguito** prima di continuare.


![Stato del circuito e del provider](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### 6\. Creare la configurazione di routing

Per istruzioni dettagliate, vedere l'articolo relativo alla [configurazione del routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) per creare e modificare i peering del circuito.

>[AZURE.IMPORTANT] Queste istruzioni si applicano solo ai circuiti creati con provider di servizi che offrono servizi di connettività di livello 2. Se si usa un provider di servizi che offre servizi gestiti di livello 3 (di solito un IP VPN, come MPLS), il provider di connettività configurerà e gestirà il routing per conto dell'utente.

### 7\. Collegare una rete virtuale a un circuito ExpressRoute

Collegare quindi una rete virtuale al circuito ExpressRoute. Fare riferimento all'articolo [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md) quando si usa il modello di distribuzione di Resource Manager.

## Ottenere lo stato di un circuito ExpressRoute

Selezionare un circuito per visualizzarne lo stato.

![Stato di un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## Modifica di un circuito ExpressRoute

È possibile modificare determinate proprietà di un circuito ExpressRoute senza conseguenze per la connettività. A questo punto, non è possibile modificare le proprietà del circuito ExpressRoute usando il portale di Azure. È tuttavia possibile usare PowerShell per modificare le proprietà del circuito. Per altre informazioni, vedere la sezione dell'articolo relativa alla [modifica di un circuito ExpressRoute tramite PowerShell](expressroute-howto-circuit-arm.md#modify).

È possibile eseguire le operazioni seguenti senza tempi di inattività:

- Abilitare o disabilitare un componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute.

- Aumentare la larghezza di banda del circuito ExpressRoute. Si noti che il downgrade della larghezza di banda di un circuito non è supportato.

- Modificare il piano di misurazione da Dati a consumo a Dati senza limiti. Si noti che la modifica del piano di misurazione da Dati senza limiti a Dati a consumo non è supportata.

-  È possibile abilitare e disabilitare l'opzione **Allow Classic Operations** (Consenti operazioni classiche).

Per altre informazioni su limiti e limitazioni, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).


## Deprovisioning ed eliminazione di un circuito ExpressRoute

È possibile eliminare il circuito ExpressRoute selezionando l'icona di **eliminazione**. Tenere presente quanto segue:

- È necessario scollegare tutte le reti virtuali dal circuito ExpressRoute. Se l'operazione non riesce, controllare se sono presenti reti virtuali collegate al circuito.

- Se lo stato di provisioning del provider del servizio del circuito ExpressRoute è **Provisioning in corso** o **Provisioning eseguito**, è necessario collaborare con il provider di servizi per eseguire il deprovisioning del circuito su tale lato. Le risorse continueranno a essere riservate e la fatturazione continuerà a essere applicata finché il provider di servizi non avrà completato il deprovisioning del circuito e inviato una notifica a Microsoft.

- Se il provider di servizi ha eseguito il deprovisioning del circuito, ovvero lo stato di provisioning del provider di servizi è impostato su **Senza provisioning**, è possibile eliminare il circuito. Non verrà più applicata la fatturazione corrispondente.

## Passaggi successivi

Dopo aver creato il circuito, verificare di eseguire le operazioni seguenti:

- [Creare e modificare il routing per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
- [Collegare la rete virtuale al circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0914_2016-->