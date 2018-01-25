---
title: Configurare i firewall e le reti virtuali di Archiviazione di Azure (anteprima) | Microsoft Docs
description: "Configurare la sicurezza di rete su più livelli per l'account di archiviazione."
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: d29f2d180df93f45202e881336e492c45587b276
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Configurare i firewall e le reti virtuali di Archiviazione di Azure (anteprima)
Archiviazione di Azure offre un modello di sicurezza a più livelli che consente di proteggere gli account di archiviazione per un set specifico di reti autorizzate.  Quando si configurano le regole di rete, solo le applicazioni delle reti consentite possono accedere a un account di archiviazione.  Quando le applicazioni eseguono chiamate da una rete autorizzata devono comunque avere l'autorizzazione necessaria (una chiave di accesso o un token di firma di accesso condiviso validi) per accedere all'account di archiviazione.

## <a name="preview-availability-and-support"></a>Anteprima della disponibilità e del supporto
I firewall e le reti virtuali di archiviazione sono funzionalità in anteprima.  Queste funzionalità sono attualmente disponibili per gli account di archiviazione nuovi o esistenti in tutte le aree del cloud pubblico di Azure.

> [!NOTE]
> I carichi di lavoro di produzione non sono supportati durante l'anteprima.
>

## <a name="scenarios"></a>Scenari
Gli account di archiviazione possono essere configurati per impedire l'accesso al traffico da qualsiasi rete (incluso il traffico Internet) per impostazione predefinita.  È possibile concedere l'accesso al traffico proveniente da reti virtuali specifiche di Azure, creando un limite di rete protetta per le applicazioni.  È anche possibile concedere l'accesso a intervalli di indirizzi IP della rete Internet pubblica, abilitando le connessioni da client Internet o client locali specifici.

Le regole di rete vengono applicate a tutti i protocolli di rete per l'archiviazione di Azure, tra cui REST e SMB.  Quando sono in vigore le regole di rete, l'accesso ai dati da strumenti come il portale di Azure, Storage Explorer e AZCopy richiede regole di rete esplicite di autorizzazione.

Le regole di rete possono essere applicate ad account di archiviazione esistenti oppure durante la creazione di nuovi account di archiviazione.

Dopo la loro applicazione, le regole di rete sono in vigore per tutte le richieste.  I token di firma di accesso condiviso che concedono l'accesso a un servizio Indirizzo IP specifico hanno lo scopo di **limitare** l'accesso del titolare del token, ma non concedono nuovi accessi oltre le regole di rete configurate. 

Le regole di rete **non** influiscono sul traffico del disco della macchina virtuale (incluse le operazioni di mount e unmount e l'I/O del disco).  L'accesso REST ai BLOB di pagine è protetto dalle regole di rete.

> [!NOTE]
> Il backup e il ripristino di macchine virtuali tramite dischi non gestiti negli account di archiviazione con regole di rete applicate non sono attualmente supportati.  Per altre informazioni, vedere [Limitazioni durante il backup e il ripristino di una VM](/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm).
>

Gli account di archiviazione classici **non** supportano i firewall e le reti virtuali.

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete
Per impostazione predefinita, gli account di archiviazione accettano connessioni da client di qualsiasi rete.  Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> La modifica delle regole di rete può influire sulla capacità di connessione delle applicazioni al servizio Archiviazione di Azure.  Se si imposta la regola di rete predefinita **nega** viene bloccato l'accesso a tutti i dati, salvo se vengono applicate anche regole di rete specifiche che *concedono* l'accesso.  Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete.
>

#### <a name="azure-portal"></a>Portale di Azure
1. Passare all'account di archiviazione che si vuole proteggere.  
> [!NOTE]
> Verificare che l'account di archiviazione si trovi in una delle aree supportate per l'anteprima pubblica.
>

2. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.
3. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da "Reti selezionate".  Per consentire il traffico da tutte le reti, scegliere di consentire l'accesso da "Tutte le reti".
4. Fare clic su *Salva* per applicare le modifiche.

#### <a name="powershell"></a>PowerShell
1. Installare la versione più aggiornata di [Azure PowerShell](/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](/powershell/azure/authenticate-azureps).

2. Visualizzare lo stato della regola predefinita per l'account di archiviazione.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>Interfaccia della riga di comando v2
1. [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) ed [effettuare l'accesso](/cli/azure/authenticate-azure-cli).
2. Visualizzare lo stato della regola predefinita per l'account di archiviazione.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Concedere l'accesso da una rete virtuale
Gli account di archiviazione possono essere configurati per consentire l'accesso solo da reti virtuali di Azure specifiche. 

Quando si abilita un [endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview) per l'archiviazione di Azure nella rete virtuale si definisce un percorso ottimale per il traffico destinato al servizio Archiviazione di Azure. Con ogni richiesta vengono anche trasmesse le identità della rete virtuale e della subnet.  Gli amministratori possono configurare in seguito regole di rete per l'account di archiviazione che consentono la ricezione di richieste da subnet specifiche nella rete virtuale di Microsoft Azure.  Per accedere ai dati, i client ai quali viene garantito l'accesso con queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione dell'account di archiviazione.

Ogni account di archiviazione può supportare fino a 100 regole della rete virtuale di Microsoft Azure, che possono essere combinate con [regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Aree della rete virtuale di Microsoft Azure disponibili
In generale gli endpoint di servizio funzionano tra reti virtuali e istanze del servizio incluse nella stessa area di Azure.  Quando gli endpoint di servizio vengono usati con Archiviazione di Azure, questo ambito viene espanso per includere le [aree abbinate](/azure/best-practices-availability-paired-regions).  In questo modo si garantisce la continuità durante un failover di area, nonché l'accesso diretto alle istanze RA-GRS (Read-Only Geo-Redundant Storage).  Le regole di rete che concedono l'accesso da una rete virtuale a un account di archiviazione concedono anche l'accesso a qualsiasi istanza RA-GRS.

Quando si pianifica il ripristino di emergenza durante un'interruzione a livello di area, è consigliabile garantire in anticipo il provisioning delle reti virtuali nell'area abbinata. Gli endpoint di servizio per Archiviazione di Azure devono essere abilitati e le regole di rete che concedono l'accesso da queste reti virtuali alternative devono essere applicate per gli account di archiviazione con ridondanza geografica.

> [!NOTE]
> Gli endpoint di servizio non si applicano al traffico esterno all'area della rete virtuale di Microsoft Azure e alla coppia di aree designata.  Le regole di rete che concedono l'accesso dalle reti virtuali agli account di archiviazione possono essere applicate solo per le reti virtuali nell'area primaria di un account di archiviazione o nell'area abbinata designata.
>

### <a name="required-permissions"></a>Autorizzazioni necessarie
Per applicare una regola della rete virtuale di Microsoft Azure a un account di archiviazione, l'utente deve avere l'autorizzazione *Join Service to a Subnet* (Aggiungi servizio a una subnet) per le subnet aggiunte.  Questa autorizzazione è inclusa nel ruolo incorporato *Collaboratore Account di archiviazione* e può essere aggiunta alle definizioni del ruolo personalizzate.

Gli account di archiviazione e le reti virtuali alle quali è stato garantito l'accesso **possono** trovarsi in sottoscrizioni diverse, ma tali sottoscrizioni devono appartenere allo stesso tenant di Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole della rete virtuale di Microsoft Azure
Le regole della rete virtuale di Microsoft Azure per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure
1. Passare all'account di archiviazione che si vuole proteggere.  
2. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.
3. Verificare di aver scelto di consentire l'accesso da "Reti selezionate".
4. Per concedere l'accesso a una rete virtuale con una nuova regola di rete, in "Reti virtuali" fare clic su "Aggiungi esistente" per selezionare una rete virtuale esistente e le subnet, quindi fare clic su *Aggiungi*.  Per creare una nuova rete virtuale e concedere l'accesso a tale rete fare clic su *Aggiungi nuova*, specificare le informazioni necessarie per creare la nuova rete virtuale e quindi fare clic su *Crea*.

> [!NOTE]
> Se un endpoint di servizio per Archiviazione di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo in questa operazione.
>

5. Per rimuovere una regola di rete virtuale o subnet, fare clic su "…" per aprire il menu di scelta rapida per la rete virtuale o la subnet,quindi fare clic su "Rimuovi".
6. Fare clic su *Salva* per applicare le modifiche.

#### <a name="powershell"></a>PowerShell
1. Installare la versione più aggiornata di [Azure PowerShell](/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](/powershell/azure/authenticate-azureps).
2. Elenco delle regole della rete virtuale di Microsoft Azure
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Aggiungere una regola di rete per una rete virtuale di Microsoft Azure e una subnet.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Rimuovere una regola di rete per una rete virtuale di Microsoft Azure e una subnet.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario le regole di rete non avranno alcun effetto.
>

#### <a name="cliv2"></a>Interfaccia della riga di comando v2
1. [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) ed [effettuare l'accesso](/cli/azure/authenticate-azure-cli).
2. Elenco delle regole della rete virtuale di Microsoft Azure
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Abilitare l'endpoint di servizio di Archiviazione di Azure in una rete virtuale e una subnet esistenti
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Aggiungere una regola di rete per una rete virtuale di Microsoft Azure e una subnet.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Rimuovere una regola di rete per una rete virtuale di Microsoft Azure e una subnet. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario le regole di rete non avranno alcun effetto.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Concedere l'accesso da un intervallo IP di Internet
È possibile configurare gli account di archiviazione in modo che consentano l'accesso da intervalli specifici di indirizzi IP Internet pubblici.  Questa configurazione consente ai servizi specifici basati su Internet e alle reti locali di ottenere l'accesso quando il traffico Internet generico è bloccato.

Gli intervalli di indirizzi Internet consentiti possono essere specificati con la [notazione CIDR](https://tools.ietf.org/html/rfc4632) nel formato *16.17.18.0/24* o come indirizzi IP singoli, ad esempio *16.17.18.19* .

> [!NOTE]
> Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati.  Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.
>

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**.  Gli intervalli di indirizzi IP riservati per le reti private (come da definizione in RFC 1918) non sono consentiti nelle regole IP.  Le reti private includono gli indirizzi che iniziano con *10.\**, *172.16.\** e *192.168.\**

Attualmente sono supportati solo gli indirizzi IPV4.

Ogni account di archiviazione può supportare fino a 100 regole di rete IP, che possono essere combinate con le [regole della rete virtuale di Microsoft Azure](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali
Per garantire l'accesso all'account di archiviazione dalle reti locali con una regola di rete IP è necessario identificare gli indirizzi IP esposti a Internet usati dalla rete.  Per assistenza contattare l'amministratore di rete.

Se la rete è connessa alla rete di Azure con [ExpressRoute](/azure/expressroute/expressroute-introduction), ogni circuito è configurato in Microsoft Edge con due indirizzi IP pubblici che consentono di connettersi a servizi Microsoft come Archiviazione di Azure usando il [peering pubblico di Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Per consentire la comunicazione tra il circuito e Archiviazione di Azure, è necessario creare regole di rete IP per gli indirizzi IP pubblici dei circuiti.  Per trovare gli indirizzi IP pubblici del circuito ExpressRoute, [aprire un ticket di supporto in ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure.


### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP
Le regole di rete IP per gli account di archiviazione possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando v2.

#### <a name="azure-portal"></a>Portale di Azure
1. Passare all'account di archiviazione che si vuole proteggere.  
2. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.
3. Verificare di aver scelto di consentire l'accesso da "Reti selezionate".
4. Per concedere l'accesso a un intervallo IP di Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in formato CIDR) in Firewall, Intervalli di indirizzi IP.
5. Per rimuovere una regola di rete IP fare clic su "…" per aprire il menu di scelta rapida della regola, quindi fare clic su "Rimuovi".
6. Fare clic su *Salva* per applicare le modifiche.

#### <a name="powershell"></a>PowerShell
1. Installare la versione più aggiornata di [Azure PowerShell](/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](/powershell/azure/authenticate-azureps).
2. Elencare le regole di rete IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Aggiungere una regola di rete per un singolo indirizzo IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Aggiungere una regola di rete per un intervallo di indirizzi IP.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Rimuovere una regola di rete per un singolo indirizzo IP. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Rimuovere una regola di rete per un intervallo di indirizzi IP.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario le regole di rete non avranno alcun effetto.
>

#### <a name="cliv2"></a>Interfaccia della riga di comando v2
1. [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) ed [effettuare l'accesso](/cli/azure/authenticate-azure-cli).
2. Elencare le regole di rete IP
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Aggiungere una regola di rete per un singolo indirizzo IP.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Aggiungere una regola di rete per un intervallo di indirizzi IP.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Rimuovere una regola di rete per un singolo indirizzo IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Rimuovere una regola di rete per un intervallo di indirizzi IP.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario le regole di rete non avranno alcun effetto.
>

## <a name="exceptions"></a>Eccezioni
Le regole di rete consentono di abilitare una configurazione di rete protetta per la maggior parte degli scenari. In determinati casi, per garantire la funzionalità completa è tuttavia necessario configurare eccezioni.  Gli account di archiviazione possono essere configurati con eccezioni per i servizi Microsoft attendibili e per l'accesso ai dati di Analisi archiviazione.

### <a name="trusted-microsoft-services"></a>Servizi Microsoft attendibili
Alcuni servizi Microsoft che interagiscono con gli account di archiviazione vengono eseguiti da reti alle quali non è possibile concedere l'accesso tramite le regole di rete. 

Per far sì che questo tipo di servizi funzioni come previsto, è possibile consentire che il set di servizi Microsoft attendibili ignori le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione.

Quando è abilitata l'eccezione "Servizi Microsoft attendibili" i servizi seguenti (se registrati nella sottoscrizione) dispongono dell'accesso all'account di archiviazione:

|Service|Nome provider di risorse|Scopo|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Creazione di immagini personalizzate e installazione di artefatti.  [Altre informazioni](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Griglia di eventi di Azure|Microsoft.EventGrid|Abilitare la pubblicazione di eventi di Archiviazione BLOB.  [Altre informazioni](https://docs.microsoft.com/azure/event-grid/overview).|
|Hub eventi di Azure|Microsoft.EventHub|Archiviare dati con Acquisizione di Hub eventi.  [Altre informazioni](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Rete di Azure|Microsoft.Networking|Archiviare e analizzare i log di traffico di rete.  [Altre informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Accesso ai dati di Analisi archiviazione
In alcuni casi l'accesso per la lettura di log diagnostici e metrica viene richiesto dall'esterno dei limiti di rete.  È possibile autorizzare eccezioni alle regole di rete per consentire l'accesso in lettura ai file di log, alle tabelle di metrica dell'account di archiviazione o a entrambi gli elementi. [Altre informazioni sull'uso dell'analisi archiviazione.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Gestione delle eccezioni
Le eccezioni alle regole di rete possono essere gestite tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure v2.

#### <a name="azure-portal"></a>Portale di Azure
1. Passare all'account di archiviazione che si vuole proteggere.  
2. Fare clic sul menu di impostazioni **Firewall e reti virtuali**.
3. Verificare di aver scelto di consentire l'accesso da "Reti selezionate".
4. In Eccezioni selezionare le eccezioni da autorizzare.
5. Fare clic su *Salva* per applicare le modifiche.

#### <a name="powershell"></a>PowerShell
1. Installare la versione più aggiornata di [Azure PowerShell](/powershell/azure/install-azurerm-ps) ed [effettuare l'accesso](/powershell/azure/authenticate-azureps).
2. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Configurare le eccezioni alle regole di rete dell'account di archiviazione.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario la rimozione delle eccezioni non avrà alcun effetto.
>

#### <a name="cliv2"></a>Interfaccia della riga di comando v2
1. [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli) ed [effettuare l'accesso](/cli/azure/authenticate-azure-cli).
2. Visualizzare le eccezioni alle regole di rete dell'account di archiviazione.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Configurare le eccezioni alle regole di rete dell'account di archiviazione.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Rimuovere le eccezioni alle regole di rete dell'account di archiviazione.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Verificare di [impostare la regola predefinita](#change-the-default-network-access-rule) su Nega. In caso contrario la rimozione delle eccezioni non avrà alcun effetto.
>

## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sugli endpoint del servizio Rete di Azure in [Endpoint di servizio](/azure/virtual-network/virtual-network-service-endpoints-overview).

Approfondire gli argomenti relativi alla sicurezza di Archiviazione di Azure in [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).
