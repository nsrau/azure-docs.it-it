<properties 
   pageTitle="Aggiornamento dinamico della larghezza di banda di ExpressRoute | Microsoft Azure"
   description="Come aumentare in modo dinamico le dimensioni della larghezza di banda di un circuito ExpressRoute senza tempi di inattività."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# Aggiornare la larghezza di banda del circuito ExpressRoute dinamicamente senza tempi di inattività

È possibile aumentare le dimensioni di un circuito ExpressRoute senza alcun tempo di inattività. Queste istruzioni consentono di aggiornare la larghezza di banda di un circuito ExpressRoute tramite PowerShell. Per altre informazioni su limiti e limitazioni, fare riferimento alla pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md).

##  Prerequisiti di configurazione

Prima di iniziare la configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Una sottoscrizione di Azure
- La versione più recente di Azure PowerShell
- Un circuito ExpressRoute attivo configurato e in esecuzione


##  Configurazione delle impostazioni mediante PowerShell

Windows PowerShell è un ambiente di scripting potente che può essere usato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Per altre informazioni, vedere la documentazione relativa a PowerShell in [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importare il modulo PowerShell per ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Ottenere i dettagli del circuito ExpressRoute**

	È possibile ottenere i dettagli del circuito ExpressRoute usando il Commandlet PowerShell riportato di seguito:
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Questo comando restituirà un elenco di tutti i circuiti creati nella sottoscrizione. È possibile usare il comando seguente per ottenere i dettagli di un circuito ExpressRoute specifico se si dispone della chiave di servizio:

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Sostituire il <skey> con la chiave del servizio effettivo.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


3. **Aumentare la larghezza di banda del circuito ExpressRoute sul lato Microsoft**
	
	Consultare la pagina delle [Domande frequenti su ExpressRoute](expressroute-faqs.md) per larghezze di banda supportate per il provider. È possibile scegliere qualsiasi dimensione maggiore della dimensione del circuito esistente. Una volta stabilite le dimensioni necessarie, è possibile utilizzare il comando seguente per ridimensionare il circuito.

		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	Il circuito verrà ridimensionato sul lato di Microsoft. Si noti che inizierà la fatturazione per la larghezza di banda aggiornata da questo punto in poi.

4. **Aumentare la larghezza di banda del circuito ExpressRoute sul lato del provider di servizio**

	Rivolgersi al provider di connettività (NSP/EXP) e fornire informazioni sulla larghezza di banda aggiornata. Seguire il processo di aggiornamento ordine previsto dal provider del servizio per completare l'attività.

 

<!---HONumber=62-->