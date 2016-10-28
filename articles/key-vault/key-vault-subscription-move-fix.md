<properties
	pageTitle="Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione | Microsoft Azure"
	description="Informazioni su come modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione a un altro tenant"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento della sottoscrizione
### D: la sottoscrizione è stata spostata dal tenant A al tenant B. Come è possibile modificare l'ID tenant per l'insieme di credenziali delle chiavi esistente e impostare gli ACL corretti per le entità nel tenant B?

Quando si crea un nuovo insieme di credenziali delle chiavi in una sottoscrizione, questo viene automaticamente associato all'ID tenant di Azure Active Directory predefinito per la sottoscrizione. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Quando si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità (utenti e applicazioni) nel tenant di B. Per risolvere questo problema è necessario

- modificare in tenant B l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione
- rimuovere tutte le voci dei criteri di accesso esistenti
- aggiungere nuove voci di criteri di accesso associate al tenant B.

Se ad esempio è presente un insieme di credenziali delle chiavi 'myvault' in una sottoscrizione che è stata spostata dal tenant A al tenant B, sarà possibile modificare l'ID tenant per questo insieme di credenziali delle chiavi e rimuovere i criteri di accesso precedenti come segue.

<pre>
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Poiché questo insieme di credenziali si trovava in precedenza nel tenant A, il valore originale di **$vault.Properties.TenantId** è il tenant A, mentre **(Get-AzureRmContext).Tenant.TenantId** è il tenant B.

Ora che l'insieme di credenziali è associato all'ID tenant corretto e le voci dei criteri di accesso precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso con [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## Passaggi successivi

- In caso di domande sull'insieme di credenziali delle chiavi, visitare i [forum sull'insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->