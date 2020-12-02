---
title: Spostare Azure Analysis Services in un'area diversa | Microsoft Docs
description: Viene descritto come spostare una risorsa Azure Analysis Services in un'area diversa.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497929"
---
# <a name="move-analysis-services-to-a-different-region"></a>Spostare Analysis Services in un'area diversa

Questo articolo descrive come spostare una risorsa di Analysis Services server in un'area di Azure diversa. È possibile spostare il server in un'altra area per diversi motivi, ad esempio per sfruttare un'area di Azure più vicina agli utenti, per usare piani di servizio supportati solo in aree specifiche o per soddisfare i requisiti di governance e criteri interni. 

In questo e negli articoli collegati associati si apprenderà come:

> [!div class="checklist"]
> 
> * Eseguire il backup di un database modello del server di origine nell' [Archivio BLOB](../storage/blobs/storage-blobs-introduction.md).
> * Esportare un modello di [risorsa](../azure-resource-manager/templates/overview.md)del server di origine.
> * Ottenere una [firma di accesso condiviso (SAS)](../storage/common/storage-sas-overview.md)di archiviazione.
> * Modificare il modello di risorsa.
> * Distribuire il modello per creare un nuovo server di destinazione.
> * Ripristinare un database modello nel nuovo server di destinazione.
> * Verificare il nuovo server e il nuovo database di destinazione.
> * Eliminare il server di origine.

Questo articolo descrive l'uso di un modello di risorsa per eseguire la migrazione di un singolo server di Analysis Services con una **configurazione di base** a un'area *e* a un gruppo di risorse differenti nella stessa sottoscrizione. L'uso di un modello consente di mantenere le proprietà del server configurate verificando che il server di destinazione sia configurato con le stesse proprietà, ad eccezione dell'area e del gruppo di risorse, come server di origine Questo articolo non descrive come trasferire le risorse associate che possono far parte dello stesso gruppo di risorse, ad esempio l'origine dati, l'archiviazione e le risorse del gateway. 

Prima di trasferire un server in un'area diversa, è consigliabile creare un piano dettagliato. Prendere in considerazione risorse aggiuntive, ad esempio gateway e archiviazione, che potrebbero anche essere spostate. Con qualsiasi piano è importante completare una o più operazioni di spostamento della versione di valutazione utilizzando server di prova prima di spostare un server di produzione.

> [!IMPORTANT]
> Le applicazioni client e le stringhe di connessione si connettono a Analysis Services usando il nome completo del server, ovvero un URI che include l'area in cui si trova il server. Ad esempio: `asazure://westcentralus.asazure.windows.net/advworks01`. Quando si trasferisce un server in un'area diversa, si crea effettivamente una nuova risorsa server in un'area diversa, che avrà un'area diversa nell'URI del nome del server. Le applicazioni client e le stringhe di connessione utilizzate negli script devono connettersi al nuovo server utilizzando l'URI del nuovo nome del server. L'uso di un [alias del nome del server](analysis-services-server-alias.md) può attenuare il numero di posizioni in cui l'URI del nome del server deve essere modificato, ma deve essere implementato prima dello spostamento di un'area.

> [!IMPORTANT]
> Le aree di Azure usano intervalli di indirizzi IP diversi. Se sono state configurate eccezioni del firewall per l'area in cui si trova il server e/o l'account di archiviazione, potrebbe essere necessario configurare un intervallo di indirizzi IP diverso. Per altre informazioni, vedere [domande frequenti su Analysis Services la connettività di rete](analysis-services-network-faq.md).

> [!NOTE]
> Questo articolo descrive come ripristinare un backup del database in un server di destinazione da un contenitore di archiviazione nell'area del server di origine. In alcuni casi, il ripristino dei backup da un'area diversa può comportare una riduzione delle prestazioni, soprattutto per database di grandi dimensioni. Per prestazioni ottimali durante il ripristino del database, eseguire la migrazione o creare un nuovo contenitore di archiviazione nell'area del server di destinazione. Copiare i file di backup con estensione abf dal contenitore di archiviazione dell'area di origine al contenitore di archiviazione dell'area di destinazione prima di ripristinare il database nel server di destinazione. Nell'ambito di questo articolo, in alcuni casi, in particolare con database di dimensioni molto grandi, lo scripting di un database dal server di origine, la ricreazione e l'elaborazione del server di destinazione per caricare i dati del database possono risultare più convenienti rispetto all'utilizzo di backup/ripristino.

> [!NOTE]
> Se si usa un gateway dati locale per connettersi alle origini dati, è necessario spostare anche la risorsa del gateway nell'area del server di destinazione. Per altre informazioni, vedere [installare e configurare un gateway dati locale](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Prerequisiti

- **Account di archiviazione di Azure**: obbligatorio per archiviare un file di backup con estensione abf.
- **SQL Server Management Studio (SSMS)**: obbligatorio per il backup e il ripristino dei database modello.
- **Azure PowerShell**. Obbligatorio solo se si sceglie di completare questa attività usando PowerShell.

## <a name="prepare"></a>Preparare

### <a name="backup-model-databases"></a>Database modello di backup

Se **le impostazioni di archiviazione** non sono già configurate per il server di origine, seguire i passaggi in [configurare le impostazioni di archiviazione](analysis-services-backup.md#configure-storage-settings).

Quando vengono configurate le impostazioni di archiviazione, seguire la procedura descritta in [backup](analysis-services-backup.md#backup) per creare un backup del database modello con estensione abf nel contenitore di archiviazione. Successivamente, il backup con estensione abf viene ripristinato nel nuovo server di destinazione.


### <a name="export-template"></a>Esportare il modello

Il modello contiene le proprietà di configurazione del server di origine.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per esportare un modello con il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare il server Analysis Services.

3. Selezionare > **Impostazioni**  >  **Esporta modello**.

4. Scegliere **Scarica** nel pannello **Esporta modello** .

5. Individuare il file zip scaricato dal portale e quindi decomprimere il file in una cartella.

   Il file zip contiene i file con estensione JSON che comprendono il modello e i parametri necessari per distribuire un nuovo server.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per esportare un modello usando PowerShell:

1. Accedere alla propria sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e seguire le istruzioni visualizzate:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione della risorsa server che si desidera spostare.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Esportare il modello del server di origine. Questi comandi salvano un modello JSON con ResourceGroupName come filename nella directory corrente.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Ottenere la firma di accesso condiviso (SAS) di archiviazione

Quando si distribuisce un server di destinazione da un modello, è necessario un token SAS di delega utente (come URI) per specificare il contenitore di archiviazione che contiene il backup del database.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ottenere una firma di accesso condiviso tramite il portale:

1. Nel portale selezionare l'account di archiviazione usato per il backup del database del server.

2. Selezionare **Storage Explorer**, quindi espandere **contenitori BLOB**. 

3. Fare clic con il pulsante destro del mouse sul contenitore di archiviazione e quindi scegliere **Ottieni firma di accesso condiviso**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Ottenere una firma di accesso condiviso":::

4. In **firma di accesso condiviso** selezionare **Crea**. Per impostazione predefinita, la firma di accesso condiviso scadrà tra 24 ore.

5. Copiare e salvare l' **URI**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere una firma di accesso condiviso usando PowerShell, seguire la procedura descritta in creare una firma di accesso condiviso di [delega utente per un contenitore o un BLOB con PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Modificare il modello

Usare un editor di testo per modificare la template.jsnel file esportato, modificando le proprietà del contenitore dell'area e del BLOB. 

Per modificare il modello:

1. In un editor di testo, nella proprietà **location** , specificare la nuova area di destinazione. Nella proprietà **backupBlobContainerUri** incollare l'URI del contenitore di archiviazione con la chiave SAS. 

    Nell'esempio seguente l'area di destinazione per il server advworks1 viene impostata su `South Central US` e viene specificato l'URI del contenitore di archiviazione con la firma di accesso condiviso: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Salvare il modello.

#### <a name="regions"></a>Regioni

Per ottenere le aree di Azure, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Per ottenere le aree usando PowerShell, eseguire il comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Spostamento

Per distribuire una nuova risorsa server in un'area diversa, si userà il **template.js** nel file esportato e modificato nelle sezioni precedenti.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel portale selezionare **Crea una risorsa**.

2. In **Cerca nel Marketplace** Digitare **distribuzione modello**, quindi premere **invio**.

3. Selezionare **distribuzione modelli**.

4. Selezionare **Create** (Crea).

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file** e quindi seguire le istruzioni per caricare il **template.jsnel** file esportato e modificato.

7. Verificare che nell'editor dei modelli siano visualizzate le proprietà corrette per il nuovo server di destinazione.

8. Selezionare **Salva**.

9. Immettere o selezionare i valori delle proprietà:

    - **Sottoscrizione**: selezionare la sottoscrizione di Azure.
    
    - **Gruppo di risorse**: selezionare **Crea nuovo** e quindi immettere un nome per il gruppo di risorse. È possibile selezionare un gruppo di risorse esistente a condizione che non contenga già un server di Analysis Services con lo stesso nome.
    
    - **Località**: selezionare la stessa area specificata nel modello.

10. Selezionare **revisione e creazione**.

11. Esaminare i termini e le nozioni di base e quindi selezionare **Crea**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare questi comandi per distribuire il modello:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Ottenere l'URI del server di destinazione

Per connettersi al nuovo server di destinazione da SSMS per ripristinare il database modello, è necessario ottenere il nuovo URI del server di destinazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ottenere l'URI del server nel portale:

1. Nel portale passare alla nuova risorsa server di destinazione.

2. Nella pagina **Panoramica** copiare l'URI del **nome del server** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ottenere l'URI del server usando PowerShell, usare i comandi seguenti:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Copiare **ServerFullName** dall'output.

---

### <a name="restore-model-database"></a>Ripristina database modello

Seguire i passaggi descritti in [ripristinare](analysis-services-backup.md#restore) per ripristinare il backup del database model. abf nel nuovo server di destinazione.

Facoltativo: dopo il ripristino del database modello, elaborare il modello e le tabelle per aggiornare i dati dalle origini dati. Per elaborare il modello e la tabella tramite SSMS:

1. In SSMS fare clic con il pulsante destro del mouse sul database modello > **elabora database**.

2. Espandere **tabelle**, fare clic con il pulsante destro del mouse su una tabella. In **tabella**/e di elaborazione selezionare tutte le tabelle e quindi fare clic su **OK**.

## <a name="verify"></a>Verifica

1. Nel portale passare al nuovo server di destinazione.

2. Nella pagina Panoramica, in **modelli nel server Analysis Services**, verificare che vengano visualizzati i modelli ripristinati.

3. Utilizzare un'applicazione client come Power BI o Excel per connettersi al modello nel nuovo server. Verificare che vengano visualizzati oggetti modello, ad esempio tabelle, misure e gerarchie. 

4. Eseguire gli script di automazione. Verificare che siano stati eseguiti correttamente.

Facoltativo: [Alm Toolkit](http://alm-toolkit.com/) è uno strumento *Open Source* per il confronto e la gestione di set di dati Power bi *e* Analysis Services database modello tabulare. Usare il Toolkit per connettersi ai database del server di origine e di destinazione e confrontare. Se la migrazione del database ha esito positivo, gli oggetti modello utilizzeranno la stessa definizione. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo la verifica, le applicazioni client possono connettersi al nuovo server e gli script di automazione sono in esecuzione correttamente, eliminare il server di origine. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per eliminare il server di origine dal portale:

Nella pagina **Panoramica** del server di origine selezionare **Elimina**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per eliminare il server di origine tramite PowerShell, utilizzare il comando Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> Dopo aver completato lo spostamento di un'area, è consigliabile che il nuovo server di destinazione usi un contenitore di archiviazione nella stessa area per i backup, anziché il contenitore di archiviazione nell'area del server di origine.