# <a name="persist-files-in-azure-cloud-shell"></a>Rendere persistenti i file in Azure Cloud Shell
Cloud Shell utilizza l'archiviazione dei file di Azure per mantenere i file in più sessioni.

## <a name="set-up-a-clouddrive-file-share"></a>Configurare una condivisione file clouddrive
Al primo avvio Cloud Shell richiede di associare una condivisione file nuova o esistente per mantenere i file in più sessioni.

> [!NOTE]
> La condivisione Bash e PowerShell sono la stessa condivisione file. Solo una condivisione file può essere associata con il montaggio automatico in Cloud Shell.

### <a name="create-new-storage"></a>Creare una nuova risorsa di archiviazione

Quando si usano le impostazioni di base e si seleziona una sola sottoscrizione, Cloud Shell crea tre risorse per conto dell'utente nell'area di Azure supportata più vicina all'utente:
* Gruppo di risorse: `cloud-shell-storage-<region>`
* Account di archiviazione: `cs<uniqueGuid>`
* Condivisione file: `cs-<user>-<domain>-com-<uniqueGuid>`

![Impostazione della sottoscrizione](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

La condivisione viene montata come `clouddrive` nella directory `$Home`. Questa azione viene eseguita una sola volta e la condivisione file viene ripetuta automaticamente nelle sessioni successive. 

In Bash la condivisione file contiene anche un'immagine da 5 GB creata per l'utente che rende automaticamente persistenti i dati nella directory `$Home`. 

### <a name="use-existing-resources"></a>Usare le risorse esistenti

Con l'opzione Avanzate è possibile associare risorse esistenti. Al prompt di impostazione dell'archiviazione, selezionare **Mostra impostazioni avanzate** per visualizzare le opzioni aggiuntive. I menu a discesa vengono filtrati in base all'area Cloud Shell assegnata e agli account di archiviazione ridondanti a livello locale o globale.

In Bash le condivisioni file esistenti ricevono un'immagine da 5 GB creata automaticamente per mantenere persistente la directory `$Home`.

![Impostazione del gruppo di risorse](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Limitare la creazione di risorse con i criteri delle risorse di Azure
Gli account di archiviazione che si creano in Cloud Shell sono contrassegnati con `ms-resource-usage:azure-cloud-shell`. Se si desidera impedire agli utenti di creare account di archiviazione con Cloud Shell, creare [criteri di risorse di Azure per tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) che vengono attivati dal tag specificato.

## <a name="supported-storage-regions"></a>Aree di archiviazione supportate
Gli account di archiviazione di Azure associati devono risiedere nella stessa area del computer Cloud Shell in cui vengono montati.

Per trovare l'area assegnata, è possibile:
* Visualizzare la nota nella finestra di dialogo "Advanced storage settings" (Impostazioni di archiviazione avanzate)
* Fare riferimento al nome dell'account di archiviazione creato automaticamente (ad esempio: `cloud-shell-storage-westus`)
* Eseguire `env` e individuare la variabile `ACC_LOCATION`

I computer Cloud Shell esistono nelle aree seguenti:
|Area|Region|
|---|---|
|Americhe|Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti occidentali|
|Europa|Europa settentrionale, Europa occidentale|
|Asia/Pacifico|India centrale, Asia sud-orientale|

