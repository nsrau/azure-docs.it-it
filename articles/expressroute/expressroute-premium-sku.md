<properties 
   pageTitle="Come abilitare o disabilitare il componente aggiuntivo ExpressRoute Premium | Microsoft Azure"
   description="Come abilitare o disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute. ExpressRoute Premium consente di aggiungere fino a 10.000 route per il peering pubblico e privato e fino a 10 reti virtuali per il circuito ExpressRoute. È inoltre possibile collegare una rete virtuale presente in un'area a un circuito ExpressRoute presente in un’altra area."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Configurare il componente aggiuntivo ExpressRoute Premium per il circuito ExpressRoute

ExpressRoute Premium include le funzionalità seguenti:

 - Aumento del limite delle tabelle di routing da 4000 a 10.000 route per il peering pubblico e il peering privato.
 - Incremento del numero di reti virtuali (VNet) che possono essere connesse al circuito ExpressRoute (il valore predefinito è 10). 
 - Connettività globale sulla rete di base Microsoft. È ora possibile collegare una VNet in un'area geopolitica a un circuito ExpressRoute in un'altra area. **Esempio:** è possibile collegare una VNet creata in Europa occidentale a un circuito ExpressRoute creato a Silicon Valley.

Per altre informazioni sul componente aggiuntivo ExpressRoute Premium, vedere la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md). Per informazioni sui costi, vedere la pagina [Dettagli prezzi](http://azure.microsoft.com/pricing/details/expressroute/).

Queste istruzioni consentono di eseguire le operazioni seguenti:

- Creare un circuito ExpressRoute con il componente aggiuntivo Premium abilitato.
- Aggiornare un circuito ExpressRoute esistente per abilitare il componente aggiuntivo Premium.
- Disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito.


## Per creare un circuito ExpressRoute con le funzionalità del componente aggiuntivo Premium abilitate

###  Prima di iniziare

Prima di iniziare la configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Una sottoscrizione di Azure
- La versione più recente di Azure PowerShell

###  1\. Importare il modulo PowerShell per ExpressRoute

Windows PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni, vedere la documentazione relativa a PowerShell in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Usare i cmdlet in basso per importare il modulo PowerShell per ExpressRoute:


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2\. Configurare un nuovo circuito ExpressRoute con le funzionalità del componente aggiuntivo Premium abilitate

È possibile creare un nuovo circuito ExpressRoute con il componente aggiuntivo Premium abilitato al momento della creazione. Seguire le istruzioni su come creare circuiti ExpressRoute con [Provider di servizi di rete (NSP)](expressroute-configuring-nsps.md) o [Provider di Exchange (EXP)](expressroute-configuring-exps.md). Un nuovo parametro facoltativo nel cmdlet New-AzureDedicatedCircuit consente di specificare la SKU. La SKU può essere Standard o Premium. Il valore predefinito è standard. Passando la SKU sul valore Premium verrà abilitato il circuito con le funzionalità del componente aggiuntivo Premium.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3\. Verificare che il componente aggiuntivo ExpressRoute Premium sia abilitato
È possibile controllare e verificare se il componente aggiuntivo ExpressRoute Premium è abilitato per il circuito. Nell'esempio seguente, per il circuito ExpressRoute non sono abilitate le funzionalità del componente aggiuntivo ExpressRoute Premium. La SKU verrà visualizzata come ***Premium*** se il componente aggiuntivo è abilitato.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## Per abilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute esistente
È possibile abilitare le funzionalità del componente aggiuntivo ExpressRoute Premium per qualsiasi circuito ExpressRoute già creato.


1. **Ottenere i dettagli del circuito ExpressRoute**

	È possibile ottenere i dettagli del circuito ExpressRoute usando il cmdlet PowerShell riportato di seguito:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Questo comando restituirà un elenco di tutti i circuiti creati nella sottoscrizione. È possibile usare il comando seguente per ottenere i dettagli di un circuito ExpressRoute specifico se si dispone della chiave di servizio

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Sostituire il <skey> con la chiave del servizio effettivo.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Abilitare il componente aggiuntivo ExpressRoute Premium per il circuito**


	È possibile abilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet PowerShell riportato di seguito:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	Il circuito avrà le funzionalità del componente aggiuntivo ExpressRoute Premium abilitate. La fatturazione della funzionalità del componente aggiuntivo Premium inizierà non appena il comando verrà eseguito correttamente.


## Per disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute

È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per un circuito ExpressRoute con il componente aggiuntivo Premium abilitato.

**Nota**: prima di procedere è necessario assicurarsi che l’uso delle funzionalità presenti nell'elenco delle funzionalità del componente aggiuntivo Premium sia stato interrotto. La richiesta di disabilitazione del componente aggiuntivo Premium avrà esito negativo se al circuito sono collegate più di 10 reti virtuali. Inoltre, le sessioni BGP verranno eliminate se si pubblicano più di 5000 route una volta disabilitato il componente aggiuntivo Premium.

1. **Ottenere i dettagli del circuito ExpressRoute**

	È possibile ottenere i dettagli del circuito ExpressRoute usando il cmdlet PowerShell riportato di seguito:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Questo comando restituirà un elenco di tutti i circuiti creati nella sottoscrizione. È possibile usare il comando seguente per ottenere i dettagli di un circuito ExpressRoute specifico se si dispone della chiave di servizio

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Sostituire il <skey> con la chiave del servizio effettivo.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito**


	È possibile disabilitare il componente aggiuntivo ExpressRoute Premium per il circuito esistente usando il cmdlet PowerShell riportato di seguito:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	Il circuito avrà il componente aggiuntivo ExpressRoute Premium disabilitato.


 

<!---HONumber=August15_HO6-->