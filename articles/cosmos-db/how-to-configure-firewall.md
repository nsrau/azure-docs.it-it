---
title: Configurare un firewall IP per l'account Azure Cosmos DB
description: Informazioni su come configurare i criteri di controllo di accesso agli indirizzi IP per il supporto del firewall negli account del database di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 24ebc7eb4c9abc72a89419611e4b4b3fa2db88b4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241971"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Configurare un firewall IP in Azure Cosmos DB

È possibile proteggere i dati archiviati nell'account Azure Cosmos db tramite i firewall IP. Azure Cosmos DB supporta i controlli di accesso IP per il supporto del firewall in ingresso. È possibile impostare un firewall IP per l'account Azure Cosmos DB in uno dei modi seguenti:

* Dal portale di Azure
* Creando in modo dichiarativo un modello di Azure Resource Manager
* A livello programmatico tramite l'interfaccia della riga di comando di Azure o Azure Powershell, aggiornando la proprietà **ipRangeFilter**

## <a id="configure-ip-policy"></a> Configurare un firewall IP tramite il portale di Azure

Per configurare i criteri di controllo dell'accesso agli indirizzi IP nel portale di Azure, passare alla pagina dell'account Azure Cosmos DB e selezionare **Firewall e reti virtuali** nel menu di navigazione. Modificare il valore **Consentire l'accesso da** su **Reti selezionate**e quindi selezionare **Salva**. 

![Screenshot che mostra come aprire la pagina Firewall nel portale di Azure](./media/how-to-configure-firewall/azure-portal-firewall.png)

Quando il controllo dell'accesso IP è attivato, il portale di Azure offre la possibilità di specificare gli indirizzi IP, gli intervalli degli indirizzi IP e le opzioni. Le opzioni consentono l'accesso al portale di Azure e ad altri servizi di Azure. Nelle sezioni seguenti vengono fornite informazioni dettagliate su queste opzioni.

> [!NOTE]
> Dopo aver abilitato i criteri di controllo dell'accesso a un indirizzo IP per l'account di Azure Cosmos DB, qualsiasi richiesta all'account di Azure Cosmos DB da computer non inclusi nell'elenco degli intervalli di indirizzi IP consentiti viene respinto. Anche l'esplorazione delle risorse di Azure Cosmos DB dal portale viene bloccata per garantire l'integrità del controllo di accesso.

### <a name="allow-requests-from-the-azure-portal"></a>Consentire le richieste dal portale di Azure

Quando si abilitano i criteri di controllo di accesso IP a livello di codice, è necessario aggiungere l'indirizzo IP per il portale di Azure alla proprietà **ipRangeFilter** per mantenere l'accesso. Gli indirizzi IP del portale sono i seguenti:

|Region|Indirizzo IP|
|------|----------|
|Germania|51.4.229.218|
|Cina|139.217.8.252|
|US Gov|52.244.48.71|
|Tutte le altre aree|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

È possibile abilitare l'accesso al portale di Azure selezionando l'opzione **Consenti l'accesso dal portale di Azure** come illustrato nello screenshot seguente: 

![Screenshot che mostra come abilitare l'accesso al portale di Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Consentire le richieste dai Datacenter globali di Azure o da altre origini all'interno di Azure

Se si accede all'account Azure Cosmos DB dai servizi che non forniscono un indirizzo IP statico (ad esempio Analisi di flusso di Azure, Funzioni di Azure) è comunque possibile usare il firewall IP per limitare l'accesso. Per consentire l'accesso all'account Azure Cosmos DB da questi servizi, aggiungere l'indirizzo IP 0.0.0.0 all'elenco di indirizzi IP consentiti. L'indirizzo 0.0.0.0 limita le richieste all'account Azure Cosmos DB dall'intervallo IP di datacenter di Azure. Questa impostazione non consente l'accesso ad altri intervalli IP all'account Azure Cosmos DB.

> [!NOTE]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le richieste da Azure, incluse le richieste dalle sottoscrizioni di altri clienti distribuite in Azure. L'elenco di indirizzi IP consentiti da questa opzione è ampia perciò limita l'efficacia di un criterio firewall. Usare questa opzione solo se le richieste non hanno origine da indirizzi IP statici o subnet nelle reti virtuali. Scegliendo automaticamente questa opzione viene consentito l'accesso dal portale di Azure poiché il portale di Azure viene distribuito in Azure.

È possibile abilitare l'accesso al portale di Azure selezionando l'opzione **Accetta connessioni da datacenter pubblici di Azure** come illustrato nello screenshot seguente: 

![Screenshot che mostra come aprire la pagina Firewall nel portale di Azure](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Richieste dall'IP corrente

Per semplificare lo sviluppo, il portale di Azure consente di identificare e aggiungere l'indirizzo IP del computer client all'elenco di indirizzi consentiti. Le app in esecuzione nel computer possono quindi accedere all'account Azure Cosmos DB. 

L'indirizzo IP del client viene rilevato automaticamente dal portale. Potrebbe trattarsi dell'indirizzo IP del client del computer oppure dell'indirizzo IP del gateway di rete. Assicurarsi di rimuovere questo indirizzo IP prima di eseguire i carichi di lavoro nell'ambiente di produzione. 

Per aggiungere l'indirizzo IP corrente all'elenco di indirizzi IP, selezionare **Aggiungi indirizzo IP corrente**. Selezionare quindi **Salva**.

![Screenshot che mostra come configurare le impostazioni del firewall per l'IP corrente](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Richieste da servizi cloud

In Azure, i servizi cloud sono uno strumento comune per ospitare la logica del servizio di livello intermedio usando Azure Cosmos DB. Per consentire l'accesso a un account di Azure Cosmos DB da un servizio cloud, è necessario aggiungere l'indirizzo IP pubblico del servizio cloud all'elenco di indirizzi IP consentiti associato all'account di Azure Cosmos DB [configurando il criterio di controllo dell'accesso agli indirizzi IP](#configure-ip-policy). In questo modo, tutte le istanze del ruolo dei servizi cloud hanno accesso all'account di Azure Cosmos DB. 

È possibile recuperare gli indirizzi IP per i servizi cloud nel portale di Azure, come mostrato nello screenshot seguente:

![Screenshot che mostra l'indirizzo IP pubblico per un servizio cloud visualizzato nel portale di Azure](./media/how-to-configure-firewall/public-ip-addresses.png)

Quando si aumenta il numero di istanze del servizio cloud aggiungendo istanze del ruolo, le nuove istanze hanno automaticamente accesso all'account di Azure Cosmos DB dal momento che fanno parte dello stesso servizio cloud.

### <a name="requests-from-virtual-machines"></a>Richieste da macchine virtuali

Per ospitare servizi di livello intermedio con Azure Cosmos DB è possibile usare anche [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/) o [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Per configurare l'account Cosmos DB in modo che consenta l'accesso da macchine virtuali, è necessario configurare gli indirizzi IP pubblici della macchina virtuale e/o del set di scalabilità di macchine virtuali come uno degli indirizzi IP consentiti per l'account di Azure Cosmos DB [configurando il criterio di controllo dell'accesso agli indirizzi IP](#configure-ip-policy). 

È possibile recuperare gli indirizzi IP per le macchine virtuali nel portale di Azure, come mostrato nello screenshot seguente:

![Screenshot che mostra un indirizzo IP pubblico per una macchina virtuale visualizzata nel portale di Azure](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Quando si aggiungono istanze di macchina virtuale al gruppo, queste ricevono automaticamente l'accesso all'account di Azure Cosmo DB.

### <a name="requests-from-the-internet"></a>Richieste da Internet

Quando si accede a un account di Azure Cosmos DB da un computer in Internet, l'indirizzo IP o l'intervallo di indirizzi IP client del computer deve essere aggiunto all'elenco di indirizzi IP consentiti per l'account di Azure Cosmos.

## <a id="configure-ip-firewall-arm"></a>Configurare un firewall IP usando un modello di Resource Manager

Per configurare il controllo di accesso al proprio account Azure Cosmos DB, assicurarsi che il modello di Resource Manager specifichi l'attributo **ipRangeFilter** con un elenco di intervalli IP consentiti. Ad esempio, aggiungere il codice JSON seguente al modello:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Configurare un criterio di controllo di accesso IP usando l'interfaccia della riga di comando di Azure

Il comando seguente illustra come creare un account Azure Cosmos DB con controllo di accesso IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Per aggiornare le impostazioni del firewall per un account esistente, eseguire il comando seguente:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>Configurare un criterio di controllo di accesso IP usando PowerShell

Lo script seguente illustra come creare un account Azure Cosmos DB con controllo di accesso IP:

```azurepowershell-interactive

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

# Add local machine's IP address to firewall, InterfaceAlias is your Network Adapter's name
$ipRangeFilter = Get-NetIPConfiguration | Where-Object InterfaceAlias -eq "Ethernet 2" | Select-Object IPv4Address

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "ipRangeFilter"=$ipRangeFilter
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>Risolvere i problemi con un criterio di controllo di accesso IP

È possibile risolvere i problemi con un criterio di controllo accesso IP usando le opzioni seguenti: 

### <a name="azure-portal"></a>Portale di Azure 
Abilitando i criteri di controllo dell'accesso agli indirizzi IP per l'account di Azure Cosmos DB, viene bloccata qualsiasi richiesta all'account da computer non inclusi nell'elenco degli intervalli di indirizzi IP consentiti. Per abilitare operazioni sul piano dati del portale, ad esempio l'esplorazione di contenitori e le query nei documenti, è quindi necessario consentire esplicitamente l'accesso al portale di Azure usando il riquadro **Firewall** nel portale.

### <a name="sdks"></a>SDK 
Quando si accede a risorse di Azure Cosmos DB usando gli SDK da computer che non sono presenti nell'elenco consentito, viene restituita una risposta generica di tipo **403 Accesso negato** senza altri dettagli. Verificare l'elenco di IP consentiti per l'account e assicurarsi che la configurazione corretta dei criteri venga applicata all'account Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>IP di origine nelle richieste bloccate
Abilitare la registrazione diagnostica nell'account Azure Cosmos DB. Questi log mostrano ogni richiesta e risposta. I messaggi relativi al firewall vengono registrati con un codice di errore 403. Filtrando questi messaggi, è possibile visualizzare gli IP di origine per le richieste bloccate. Vedere [Registrazione diagnostica di Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Richieste provenienti da una subnet con endpoint di servizio per Azure Cosmos DB abilitato
Le richieste provenienti da una subnet nella rete virtuale che dispone di un endpoint del servizio per Azure Cosmos DB abilitato invia la rete virtuale e l'identità del subnet per gli account Azure Cosmos DB. Queste richieste non dispongono dell'indirizzo IP pubblico dell'origine perciò vengono rifiutati dai filtri IP. Per consentire l'accesso da subnet specifiche nelle reti virtuali, aggiungere un elenco di controllo di accesso della rete virtuale descritta in [Come configurare la rete virtuale e l'accesso basato su subnet per l'account Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). L'applicazione delle regole del firewall può richiedere fino a 15 minuti.


## <a name="next-steps"></a>Passaggi successivi

Per configurare un endpoint servizio di rete virtuale per l'account Azure Cosmos DB, vedere gli articoli seguenti:

* [Controllo di accesso di rete virtuale e subnet per l'account Azure Cosmos DB](vnet-service-endpoint.md)
* [Configurare l'accesso basato su rete virtuale e subnet per l'account di Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)

