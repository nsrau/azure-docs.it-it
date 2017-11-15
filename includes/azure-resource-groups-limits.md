| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Risorse per ogni [gruppo di risorse](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (per ogni tipo di risorsa) |800 |Varia in base al tipo di risorsa |
| Distribuzioni per gruppo di risorse nella cronologia della distribuzione |800 |800 |
| Risorse per distribuzione |800 |800 |
| Blocchi di gestione (per ambito univoco) |20 |20 |
| Numero di tag (per risorsa o gruppo di risorse) |15 |15 |
| Lunghezza della chiave dei tag |512 |512 |
| Lunghezza del valore dei tag |256 |256 |


#### <a name="template-limits"></a>Limiti del modello

| Valore | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Parametri |256 |256 |
| Variabili |256 |256 |
| Risorse (incluso il numero di copie) |800 |800 |
| Output |64 |64 |
| Espressione del modello |24.576 caratteri |24.576 caratteri |
| Risorse in modelli esportati |200 |200 | 
| Dimensione del modello |1 MB |1 MB |
| Dimensione del file di parametri |64 KB |64 KB |

È possibile superare alcuni limiti del modello usando un modello annidato. Per altre informazioni, vedere [Uso di modelli collegati nella distribuzione di risorse di Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Per ridurre il numero di parametri, variabili o output, è possibile combinare più valori in un oggetto. Per altre informazioni, vedere [Oggetti come parametri](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Se si raggiunge il limite di 800 distribuzioni per gruppo di risorse, eliminare dalla cronologia le distribuzioni che non sono più necessarie. È possibile eliminare le voci dalla cronologia usando [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) nell'interfaccia della riga di comando di Azure o [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) in PowerShell. L'eliminazione di una voce dalla cronologia della distribuzione non ha effetto sulle risorse distribuite. 