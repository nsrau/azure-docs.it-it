<properties 
   pageTitle="Condivisione di un circuito ExpressRoute tra più sottoscrizioni | Microsoft Azure"
   description="Questo articolo descrive come condividere il circuito ExpressRoute tra più sottoscrizioni di Azure."
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
   ms.date="06/25/2015"
   ms.author="cherylmc" />

# Condivisione di un circuito ExpressRoute tra più sottoscrizioni

Un singolo circuito ExpressRoute può essere condiviso tra più sottoscrizioni. La **figura 1** illustra un semplice schema di come funziona la condivisione di circuiti ExpressRoute tra più sottoscrizioni.

Ognuno dei cloud più piccoli nel cloud di grandi dimensioni viene usato per rappresentare le sottoscrizioni appartenenti a reparti diversi di un'organizzazione. Ogni reparto dell'organizzazione può usare la propria sottoscrizione per distribuire i servizi, ma può condividere un singolo circuito ExpressRoute dedicato per la connessione alla rete aziendale. Un singolo reparto (in questo esempio, IT) può possedere il circuito ExpressRoute dedicato. I costi relativi a connettività e larghezza di banda per il circuito dedicato saranno addebitati al relativo proprietario. Altre sottoscrizioni all'interno dell'organizzazione possono usare il circuito ExpressRoute.

**Figura 1**

![Condivisione della sottoscrizione](./media/expressroute-share-circuit/IC766124.png)

## Amministrazione

Il *proprietario del circuito* è l'amministratore il coamministratore della sottoscrizione in cui viene creato il circuito ExpressRoute dedicato. Il proprietario del circuito può autorizzare gli amministratori o i coamministratori di altre sottoscrizioni (indicati come *utenti del circuito* nel diagramma del flusso di lavoro) a usare il proprio circuito dedicato. Gli utenti del circuito autorizzati a usare il circuito dedicato dell'organizzazione possono collegare la rete virtuale nella propria sottoscrizione al circuito dedicato dopo aver ricevuto l'autorizzazione.

Il proprietario del circuito ha la facoltà di modificare e revocare le autorizzazioni in qualsiasi momento. La revoca dell'autorizzazione comporterà l'eliminazione di tutti i collegamenti dalla sottoscrizione di cui è stato revocato l'accesso.

## Flusso di lavoro

1. Il proprietario del circuito autorizza gli amministratori di altre sottoscrizioni a usare il circuito specificato. Nell'esempio seguente l'amministratore del circuito (Contoso IT) abilita l'amministratore di un'altra sottoscrizione (Contoso Sales), specificando il relativo ID Microsoft (Live), per collegare fino a 2 reti virtuali al circuito. Il cmdlet non invia messaggi di posta elettronica all'ID di Microsoft specificato. È necessario che il proprietario del circuito informi in modo esplicito il proprietario dell'altra sottoscrizione che l'autorizzazione è stata completata.

		PS C:> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. Dopo aver notificato il proprietario del circuito, l'amministratore della sottoscrizione autorizzata può eseguire il cmdlet seguente per recuperare la chiave del servizio del circuito. In questo esempio, l'amministratore di Contoso Sales deve prima accedere con l'ID Microsoft specificato, salesadmin@contoso.com.

		PS C:> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. L'amministratore della sottoscrizione autorizzata esegue il cmdlet seguente per completare l'operazione di collegamento.

		PS C:> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

L'operazione è terminata. La rete virtuale di Contoso Sales in Azure è ora collegata a un circuito creato o di proprietà di ContosoIT.

## Gestione dell'autorizzazione

Il proprietario del circuito può condividere un circuito con un massimo di 10 sottoscrizioni di Azure. Il proprietario del circuito può vedere chi è stato autorizzato al circuito. Il proprietario può revocare l'autorizzazione in qualsiasi momento. Quando il proprietario del circuito revoca l'autorizzazione, identificata da LinkAuthorizationId, tutti i collegamenti consentiti dall'autorizzazione saranno eliminati immediatamente. Le reti virtuali collegate perderanno la connettività alla rete locale tramite il circuito ExpressRoute.

	PS C:> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


Il diagramma seguente illustra il flusso di lavoro di autorizzazione:

![Flusso di lavoro di condivisione della sottoscrizione](./media/expressroute-share-circuit/IC759525.png)

## Passaggi successivi

Per altre informazioni su ExpressRoute, vedere l'articolo [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md).

<!---HONumber=July15_HO2-->