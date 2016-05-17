<properties 
	pageTitle="Esportare il modello di Azure Resource Manager | Microsoft Azure" 
	description="Usare Azure Resource Manager per esportare un modello da un gruppo di risorse esistente." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="tomfitz"/>

# Esportare il modello di Azure Resource Manager dalle risorse esistenti

La procedura per la creazione di modelli di Azure Resource Manager può sembrare complessa, ma Resource Manager semplifica questa attività consentendo di esportare un modello da risorse esistenti della sottoscrizione. È possibile usare tale modello generato per ottenere informazioni sulla sintassi del modello o per automatizzare la ridistribuzione della soluzione, in base alla necessità.

In questa esercitazione verrà creato un account di archiviazione tramite il portale e verrà esportato il modello per l'account di archiviazione. Verrà quindi modificato il gruppo di risorse mediante l'aggiunta di una rete virtuale al gruppo e verrà esportato un nuovo modello che ne rappresenta lo stato corrente. Anche se questo argomento è incentrato su un'infrastruttura semplificata, è possibile usare la stessa procedura per esportare un modello per una soluzione più complicata.

## Create storage account

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Nuovo**, **Dati e archiviazione** e **Account di archiviazione**.

      ![creare la risorsa di archiviazione](./media/resource-manager-export-template/create-storage.png)

2. Creare un account di archiviazione con nome **storage**, le proprie iniziali e la data. Il nome dell'account di archiviazione deve essere univoco in Azure, quindi se il nome è già in uso, provare una variazione. Per il gruppo di risorse specificare il valore **ExportGroup**. È possibile usare i valori predefiniti per le altre proprietà. Selezionare **Create**.

      ![specificare i valori per la risorsa di archiviazione](./media/resource-manager-export-template/provide-storage-values.png)

Al termine della distribuzione, la sottoscrizione contiene l'account di archiviazione.

## Esportare il modello per la distribuzione
   
1. Passare al pannello Gruppo di risorse per il nuovo gruppo di risorse. Si noterà che il risultato dell'ultima distribuzione è incluso nell'elenco. Selezionare questo collegamento.

      ![pannello Gruppo di risorse](./media/resource-manager-export-template/resource-group-blade.png)
   
2. Verrà visualizzata la cronologia delle distribuzioni per il gruppo. In questo caso viene probabilmente elencata solo una distribuzione. Selezionare questa distribuzione.

     ![ultima distribuzione](./media/resource-manager-export-template/last-deployment.png)

3. Viene visualizzato un riepilogo della distribuzione. Il riepilogo include lo stato della distribuzione e le relative operazioni e i valori specificati per i parametri. Per visualizzare il modello usato per la distribuzione, selezionare **Visualizza modello**.

     ![visualizzare il riepilogo della distribuzione](./media/resource-manager-export-template/deployment-summary.png)

4. Resource Manager recupera automaticamente 5 file. Sono:

   1. Modello che definisce l'infrastruttura per la soluzione. Quando è stato creato l'account di archiviazione tramite il portale, Resource Manager ha usato un modello per distribuirlo e ha salvato tale modello come riferimento futuro.

   2. File dei parametri che può essere usato per passare i valori durante la distribuzione. Contiene i valori specificati durante la prima distribuzione, ma è possibile modificare qualsiasi valore durante la ridistribuzione del modello.

   3. File di script di Azure PowerShell che può essere usato per distribuire il modello.

   4. File di script dell'interfaccia della riga di comando di Azure che può essere usato per distribuire il modello.
   
   5. Classe .NET che può essere usato per distribuire il modello.

     I file sono disponibili mediante collegamenti nel pannello. Per impostazione predefinita, il modello è selezionato.
     
       ![visualizzare il modello](./media/resource-manager-export-template/view-template.png)
     
     Occorre prestare particolare attenzione al modello. Il modello deve avere un aspetto analogo al seguente:
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            } 
          ]
        }
   
     Si noti che definisce parametri per l'account di archiviazione, il tipo, la posizione e l'abilitazione o meno della crittografia, il cui valore predefinito è **false**. Nella sezione **resources** verrà visualizzata la definizione per l'account di archiviazione da distribuire.
     
Le parentesi quadre contengono un'espressione che viene valutata durante la distribuzione. Le espressioni tra parentesi mostrate sopra vengono usate per ottenere i valori dei parametri durante la distribuzione. È possibile usare molte altre espressioni ed esempi di altre espressioni sono disponibili più avanti in questo argomento. Per un elenco completo, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
   
Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).

## Aggiungere la rete virtuale

Il modello scaricato nella sezione precedente rappresentava l'infrastruttura per tale distribuzione originale, ma non terrà in considerazione eventuali modifiche apportate dopo la distribuzione. Per illustrare questo problema, modificare il gruppo di risorse aggiungendo una rete virtuale tramite il portale.

1. Nel pannello Gruppo di risorse selezionare **Aggiungi** e scegliere la **rete virtuale** dalle risorse disponibili.
   
2. Specificare il nome **VNET** per la rete virtuale e usare i valori predefiniti per le altre proprietà. Selezionare **Crea**.

      ![impostare l'avviso](./media/resource-manager-export-template/create-vnet.png)
   
3. Al termine della distribuzione della rete virtuale nel gruppo di risorse, esaminare ancora la cronologia di distribuzione. Verranno visualizzate due distribuzioni. Selezionare la distribuzione più recente.

      ![cronologia della distribuzione](./media/resource-manager-export-template/deployment-history.png)
   
4. Esaminare il modello per tale distribuzione. Si noti che definisce solo le modifiche apportate per aggiungere la rete virtuale.

È in genere consigliabile usare un modello che distribuisce tutta l'infrastruttura per la soluzione in una singola operazione, invece di dovere ricordare molti modelli diversi da distribuire.


## Esportare il modello per un gruppo di risorse

Anche se ogni distribuzione mostra solo le modifiche apportate al gruppo di risorse, è possibile esportare in qualsiasi momento un modello per mostrare gli attributi dell'intero gruppo di risorse.

1. Per visualizzare il modello per un gruppo di risorse, selezionare **Esporta modello**.

      ![esportare un gruppo di risorse](./media/resource-manager-export-template/export-resource-group.png)

2. Verranno visualizzati di nuovo i cinque file che è possibile usare per ridistribuire la soluzione, ma questa volta il modello è leggermente diverso. Questo modello ha solo due parametri, uno per il nome dell'account di archiviazione e uno per il nome della rete virtuale.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },
        
     Resource Manager non ha recuperato i modelli usati durante la distribuzione. Ha generato invece un nuovo modello basato sulla configurazione corrente delle risorse. Resource Manager non sa quali valori si vogliono passare come parametri, quindi la maggior parte dei valori è hard-coded in base al valore nel gruppo di risorse. Ad esempio, la posizione dell'account di archiviazione e il valore di replica sono impostati su:
     
        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Scaricare il modello, in modo da poterlo modificare localmente.

      ![scaricare il modello](./media/resource-manager-export-template/download-template.png)

4. Trovare il file con estensione zip scaricato ed estrarne i contenuti. È possibile usare questo modello scaricato per ridistribuire l'infrastruttura.

## Passaggi successivi

Congratulazioni. Si è appreso come esportare un modello da risorse create nel portale.

- Nella seconda parte di questa esercitazione verrà personalizzato il modello appena scaricato mediante l'aggiunta di altri parametri e il modello verrà ridistribuito tramite uno script. Vedere [Personalizzare e ridistribuire il modello esportato](resource-manager-customize-template.md).
- Per informazioni su come esportare un modello mediante PowerShell, vedere [Uso di Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Per informazioni su come esportare un modello mediante l'interfaccia della riga di comando di Azure, vedere [Usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md). 

<!---HONumber=AcomDC_0511_2016-->