<properties 
	pageTitle="Creare avvisi per gli eventi di Data factory di Azure" 
	description="Informazioni su come sia possibile creare avvisi per gli eventi generati da Azure per le operazioni di Data factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="spelluru"/>

# Creazione di avvisi per gli eventi di Azure
Gli eventi di Azure forniscono utili informazioni su quanto accade alle risorse di Azure. Azure registra eventi utente quando una risorsa di Azure (ad esempio, una data factory) viene creata, aggiornata o eliminata. Quando si usa Data factory di Azure, vengono generati eventi quando:
 
1.	Data factory di Azure viene creata/aggiornata/eliminata.
2.	L'elaborazione dati (esecuzione) è stata avviata/completata.
3.	Quando un cluster HDInsight su richiesta viene creato e rimosso.

È possibile creare avvisi per questi eventi utente e configurarli per l'invio di notifiche tramite posta elettronica all'amministratore e ai coamministratori della sottoscrizione. Inoltre, è possibile specificare altri indirizzi di posta elettronica di utenti che devono ricevere notifiche tramite posta elettronica quando vengono soddisfatte le condizioni.

## Specifica di una definizione di avviso
Per specificare una definizione di avviso, creare un file JSON che descrive le operazioni per cui si desidera essere avvisati. Nell'esempio seguente, l'avviso invierà una notifica tramite posta elettronica per l'operazione **RunFinished**. In particolare, viene inviata una notifica tramite posta elettronica quando un'esecuzione nella data factory viene completata, ma l'esito è negativo (stato = FailedExecution).

	{
    	"contentVersion": "1.0.0.0",
   		 "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    	"parameters": {},
    	"resources": 
		[
        	{
            	"name": "ADFAlertsSlice",
            	"type": "microsoft.insights/alertrules",
            	"apiVersion": "2014-04-01",
            	"location": "East US",
            	"properties": 
				{
                	"name": "ADFAlertsSlice",
                	"description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                	"isEnabled": true,
                	"condition": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    	"dataSource": 
						{
                        	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        	"operationName": "RunFinished",
			         		"status": "Failed",
                        		"subStatus": "FailedExecution"   
                    	}
                	},
                	"action": 
					{
                    	"odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    	"customEmails": [ "<your alias>@contoso.com" ]
                	}
            	}
        	}
    	]
	}

**subStatus** può essere rimosso dalla definizione JSON precedente se non si desidera ricevere un avviso in caso di errore specifico.

Vedere [Operazioni e stati disponibili](#AvailableOperationsStatuses) per l'elenco di operazioni e di stati (e degli stati secondari).

## Distribuzione dell'avviso
Per distribuire l'avviso, usare il cmdlet di Azure PowerShell **New-AzureResourceGroupDeployment**, come mostrato nell'esempio seguente:

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

StorageAccountName specifica l'account di archiviazione per archiviare il file JSON dell'avviso distribuito.

Una volta completata la distribuzione del gruppo di risorse, si noteranno i messaggi seguenti:
	
	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## Recupero dell'elenco di distribuzioni del gruppo di risorse Azure
Per recuperare l'elenco di distribuzioni del gruppo di risorse di Azure distribuite, usare il cmdlet **Get-AzureResourceGroupDeployment**, come mostrato nell'esempio seguente:
	
	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

## <a name="AvailableOperationsStatuses"></a>Nomi di operazione e valori di stato disponibili

<table>
<th align="left">Nome operazione</th>
<th align="left">Stato</th>
<th align="left">Stato secondario</th>

<tr>
<td>RunStarted</td>
<td>Avviato</td>
<td>Avvio in corso</td>
</tr>

<tr>
<td>RunFinished</td>
<td>Failed/Succeeded</td>
<td>
	<p>FailedResourceAllocation </p>
	<p>Succeeded</p>
	<p>FailedExecution</p>
	<p>TimedOut</p>
	<p>Canceled</p>
	<p>FailedValidation</p>
	<p>Abandoned</p>
</td>
</tr>

<tr>
<td>SliceOnTime</td>
<td>In Progress</td>
<td>Ontime</td>
</tr>

<tr>
<td>SliceDelayed</td>
<td>In Progress</td>
<td>Late</td>
</tr>

<tr>
<td>OnDemandClusterCreateStarted</td>
<td>Avviato</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateSuccessful</td>
<td>Succeeded</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterDeleted</td>
<td>Succeeded</td>
<td></td>
</tr>

</table>

## Risoluzione dei problemi relativi agli eventi utente
Eseguire il comando seguente per visualizzare gli eventi generati:

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"
 

<!---HONumber=62-->