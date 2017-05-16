Per applicare un tag a una risorsa durante la distribuzione, aggiungere l'elemento `tags` alla risorsa distribuita e specificare il nome e il valore del tag.

### <a name="apply-literal-value-to-tag-name"></a>Applicare un valore letterale al nome del tag
L'esempio seguente illustra un account di archiviazione con due tag (`Dept` e `Environment`) impostati su valori letterali:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

### <a name="apply-object-to-tag-element"></a>Applicare un oggetto a un elemento tag
È possibile definire un parametro oggetto in cui vengono memorizzati diversi tag e applicare tale oggetto all'elemento tag. Ogni proprietà nell'oggetto diventa un tag separato per la risorsa. L'esempio seguente include un parametro denominato `tagValues` che viene applicato all'elemento tag.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-json-string-to-tag-name"></a>Applicare una stringa JSON al nome del tag

Per memorizzare più valori in un singolo tag, è possibile applicare una stringa JSON che rappresenta tali valori. L'intera stringa JSON viene memorizzata come un unico tag che non può superare i 256 caratteri. L'esempio seguente include un tag singolo denominato `CostCenter` che contiene più valori da una stringa JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```