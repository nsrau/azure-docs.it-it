Per aggiungere un tag a un gruppo di risorse, usare **azure group set**. Se il gruppo di risorse non dispone di tag, passare il tag.

```azurecli
azure group set -n tag-demo-group -t Dept=Finance
```

I tag vengono aggiornati nel loro complesso. Se si vuole aggiungere un tag a un gruppo di risorse che dispone di tag, è possibile passare tutti i tag. 

```azurecli
azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
```

I tag non vengono ereditati dalle risorse in un gruppo di risorse. Per aggiungere un tag a una risorsa, usare **azure resource set**. Passare il numero di versione API per il tipo di risorsa a cui si sta aggiungendo il tag. Se è necessario recuperare la versione dell'API, usare il comando seguente con il provider di risorse per il tipo che si sta impostando:

```azurecli
azure provider show -n Microsoft.Storage --json
```

Nei risultati, cercare il tipo di risorsa desiderato.

```azurecli
"resourceTypes": [
{
  "resourceType": "storageAccounts",
  ...
  "apiVersions": [
    "2016-01-01",
    "2015-06-15",
    "2015-05-01-preview"
  ]
}
...
```

A questo punto, specificare tale versione dell'API, il nome gruppo di risorse, il nome della risorsa, il tipo di risorsa e il valore del tag come parametri.

```azurecli
azure resource set -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -t Dept=Finance -o 2016-01-01
```

I tag risultano direttamente sulle risorse e sui gruppi di risorse. Per visualizzare i tag esistenti, recuperare un gruppo di risorse e le relative risorse con **azure group show**.

```azurecli
azure group show -n tag-demo-group --json
```

Verranno restituiti i metadati relativi al gruppo di risorse, inclusi gli eventuali tag applicati.

```azurecli
{
  "id": "/subscriptions/4705409c-9372-42f0-914c-64a504530837/resourceGroups/tag-demo-group",
  "name": "tag-demo-group",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "location": "southcentralus",
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Project": "Upgrade"
  },
  ...
}
```

È possibile visualizzare i tag per una particolare risorsa usando **azure resource show**.

```azurecli
azure resource show -g tag-demo-group -n storagetagdemo -r Microsoft.Storage/storageAccounts -o 2016-01-01 --json
```

Per recuperare tutte le risorse con un valore di tag, usare:

```azurecli
azure resource list -t Dept=Finance --json
```

Per recuperare tutti i gruppi di risorse con un valore di tag, usare:

```azurecli
azure group list -t Dept=Finance
```

È possibile visualizzare i tag esistenti nella sottoscrizione con il comando seguente:

```azurecli
azure tag list
```
