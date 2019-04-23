---
title: Distribuire Azure Databricks in una rete virtuale (anteprima)
description: Questo articolo descrive come distribuire Azure Databricks per la rete virtuale, noto anche come inserimento di rete virtuale.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 2db588a0cf67d7826408139e8facb43a2e897951
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003446"
---
# <a name="deploy-azure-databricks-in-your-virtual-network-preview"></a>Distribuire Azure Databricks in una rete virtuale (anteprima)

La distribuzione predefinita di Azure Databricks è un servizio completamente gestito in Azure: tutte le risorse del piano dati, tra cui una rete virtuale (VNet), vengono distribuite a un gruppo di risorse bloccate. Se è necessaria la personalizzazione di rete, tuttavia, è possibile distribuire le risorse di Azure Databricks nella propria rete virtuale (anche denominato VNet injection), quando consente di:

* Connettere Azure Databricks per altri servizi di Azure (ad esempio archiviazione di Azure) in modo più sicuro tramite gli endpoint di servizio.
* Connettersi a dati locali origini per l'uso con Azure Databricks, sfruttando i vantaggi delle route definite dall'utente.
* Connettere Azure Databricks a un'appliance virtuale di rete per esaminare tutto il traffico in uscita e intraprendere azioni in base alla regole di negazione e assenso.
* Configurare Azure Databricks per l'uso di DNS personalizzato.
* Configurare regole di gruppo (NSG) di sicurezza di rete per specificare le restrizioni di traffico in uscita.
* Distribuire i cluster Azure Databricks in una rete virtuale esistente.

Distribuzione delle risorse di Azure Databricks per la propria rete virtuale consente inoltre di sfruttare i vantaggi degli intervalli CIDR flessibile (in un punto qualsiasi tra /16-/ 24 per la rete virtuale e tra /18-/ 26 per le subnet).

  > [!NOTE]
  > Non è possibile sostituire la rete virtuale per un'area di lavoro. Se l'area di lavoro corrente non è possibile gestire il numero necessario di nodi attivi del cluster, creare un'altra area di lavoro in una rete virtuale di dimensioni maggiori. Seguire [questi passaggi dettagliati per la migrazione](howto-regional-disaster-recovery.md#detailed-migration-steps) copiare le risorse (blocchi appunti, le configurazioni del cluster, i processi) dalla vecchia alla nuova area di lavoro.

## <a name="virtual-network-requirements"></a>Requisiti della rete virtuale

È possibile usare l'interfaccia di distribuzione dell'area di lavoro Azure Databricks nel portale di Azure per configurare automaticamente una rete virtuale esistente con le subnet obbligatorie, il gruppo di sicurezza di rete e le impostazioni elenco elementi consentiti oppure è possibile usare Azure Resource Manager modelli per configurare la rete virtuale e distribuire l'area di lavoro.

La rete virtuale che si distribuisce l'area di lavoro Azure Databricks deve soddisfare i requisiti seguenti:

### <a name="location"></a>Location

La rete virtuale deve trovarsi nella stessa località dell'area di lavoro Azure Databricks.

### <a name="subnets"></a>Subnet

La rete virtuale deve includere due subnet dedicata per Azure Databricks:

   1. Una subnet privata con un gruppo di sicurezza di rete configurate che consenta la comunicazione interna al cluster

   2. Una subnet pubblica a un gruppo di sicurezza di rete configurate che consente la comunicazione con il piano di controllo di Azure Databricks.

### <a name="address-space"></a>Spazio degli indirizzi

Un blocco CIDR tra /16-/ 24 per la rete virtuale e un blocco CIDR tra /18-/26 per le subnet pubbliche e private.

### <a name="whitelisting"></a>Inserimento nella whitelist

Tutto il traffico in ingresso e in uscita tra le subnet e il piano di controllo di Azure Databricks deve essere inserito nella Whitelist.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

Questa sezione descrive come creare un'area di lavoro Azure Databricks nel portale di Azure e distribuirlo nella propria rete virtuale esistente. Azure Databricks consente di aggiornare la rete virtuale con due nuove subnet e gruppi di sicurezza di rete utilizzando intervalli CIDR forniti dall'utente, degli elenchi elementi consentiti in ingresso e il traffico delle subnet in uscita e distribuisce l'area di lavoro nella rete virtuale aggiornata.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una rete virtuale a cui verrà distribuito l'area di lavoro Azure Databricks. È possibile usare una rete virtuale esistente o crearne uno nuovo, ma la rete virtuale deve trovarsi nella stessa area dell'area di lavoro Azure Databricks che si intende creare. Un intervallo CIDR tra /16-da/24 è necessario per la rete virtuale.

  > [!Warning]
  > Un'area di lavoro con una rete virtuale di dimensioni minori, vale a dire un'inferiore intervallo CIDR, possono esaurire gli indirizzi IP (spazio di rete) più rapidamente rispetto a un'area di lavoro con una rete virtuale di dimensioni maggiori. Ad esempio, un'area di lavoro con/24 rete virtuale e /26 subnet può avere un massimo di 64 nodi attivi alla volta, mentre un'area di lavoro con un /20 rete virtuale e /22 subnet può contenere un massimo di nodi di 1024.

  Le subnet verranno create automaticamente quando si configura l'area di lavoro, e si avrà la possibilità di specificare l'intervallo CIDR per le subnet durante la configurazione.

## <a name="configure-the-virtual-network"></a>Configurare la rete virtuale

1. Nel portale di Azure, selezionare **+ crea una risorsa > Analitica > Azure Databricks** per aprire la finestra di dialogo del servizio Azure Databricks.

2. Seguire i passaggi di configurazione descritti nel passaggio 2: Creare un'area di lavoro Azure Databricks in Guida introduttiva e selezionare l'area di lavoro di distribuzione di Azure Databricks nelle opzioni di rete virtuale.

   ![Creare il servizio Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Selezionare la rete virtuale da usare.

   ![Opzioni di rete virtuale](./media/vnet-injection/select-vnet.png)

4. Specificare gli intervalli CIDR in un blocco tra /18-/26 per due subnet, dedicata ad Azure Databricks:

   * Una subnet pubblica verrà creata con un gruppo di sicurezza di rete associati che consente la comunicazione con il piano di controllo di Azure Databricks.
   * Una subnet privata verrà creata con un gruppo di sicurezza di rete associati che consente la comunicazione interna al cluster.

5. Fare clic su **Create** per distribuire l'area di lavoro Azure Databricks per la rete virtuale.

## <a name="advanced-resource-manager-configurations"></a>Configurazioni di gestione risorse avanzate

Se si desidera un maggiore controllo sulla configurazione della rete virtuale, ad esempio, si desidera usare le subnet esistenti, usare gruppi di sicurezza di rete esistente o creare proprie regole di sicurezza: è possibile usare i modelli di Azure Resource Manager seguente anziché il distribuzione dell'area di lavoro e configurazione di rete virtuale del portale.

### <a name="all-in-one"></a>Tutto in uno

Per creare una rete virtuale, i gruppi di sicurezza di rete e dell'area di lavoro Azure Databricks tutto in uno, usare il [modello All-in-one per Databricks VNet inserito le aree di lavoro](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Quando si usa questo modello, non devi effettuare qualsiasi manuale nell'elenco elementi consentiti del traffico della subnet.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Per creare i gruppi di sicurezza di rete con le regole necessarie per una rete virtuale esistente, usare il [modello di gruppo di sicurezza di rete per l'inserimento di rete virtuale di Databricks](https://azure.microsoft.com/resources/templates/101-databricks-nsg-for-vnet-injection).

Quando si usa questo modello, non devi effettuare qualsiasi manuale nell'elenco elementi consentiti del traffico della subnet.

### <a name="virtual-network"></a>Rete virtuale

Per creare una rete virtuale con subnet pubblica e privata corretta, usare il [modello di rete virtuale per l'inserimento di rete virtuale di Databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Se si usa questo modello senza usare anche il modello di gruppi di sicurezza di rete, è necessario aggiungere manualmente le regole nell'elenco elementi consentiti per i gruppi di sicurezza di rete che è utilizzare con la rete virtuale.

### <a name="azure-databricks-workspace"></a>Area di lavoro di Azure Databricks

Per distribuire un'area di lavoro Azure Databricks in una rete virtuale esistente con subnet pubblica e privata e i gruppi di sicurezza di rete configurata correttamente già configurati, usare il [modello di area di lavoro per l'inserimento di rete virtuale di Databricks](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Se si usa questo modello senza usare anche il modello di gruppi di sicurezza di rete, è necessario aggiungere manualmente le regole nell'elenco elementi consentiti per i gruppi di sicurezza di rete che è utilizzare con la rete virtuale.

## <a name="whitelisting-subnet-traffic"></a>Traffico delle subnet nell'elenco elementi consentiti

Se non si usa la [portale di Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) oppure [modelli di Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) per creare gruppi di sicurezza di rete, è necessario consentire il traffico seguenti nelle subnet.

|Direction|Protocollo|Source (Sorgente)|Porta di origine|Destination|Porta di destinazione|
|---------|--------|------|-----------|-----------|----------------|
|In ingresso|\*|VirtualNetwork|\*|\*|\*|
|In ingresso|\*|Indirizzi IP NAT piano di controllo|\*|\*|22|
|In ingresso|\*|Indirizzi IP NAT piano di controllo|\*|\*|5557|
|In uscita|\*|\*|\*|IP di App Web|\*|
|In uscita|\*|\*|\*|SQL (tag di servizio)|\*|
|In uscita|\*|\*|\*|Archiviazione (tag di servizio)|\*|
|In uscita|\*|\*|\*|VirtualNetwork|\*|

Elenco elementi consentiti il traffico delle subnet usando l'indirizzo IP seguente consente di risolvere. Per SQL (metastore) e (archiviazione di artefatti e log), è consigliabile usare Sql e archiviazione [tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

|Area di Azure Databricks|Service|IP pubblico|
|-----------------------|-------|---------|
|Stati Uniti orientali|Piano di controllo NAT </br></br>App Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti orientali 2|Piano di controllo NAT </br></br>App Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centro-settentrionali|Piano di controllo NAT </br></br>App Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centrali|Piano di controllo NAT </br></br>App Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centro-meridionali|Piano di controllo NAT </br></br>App Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Stati Uniti occidentali|Piano di controllo NAT </br></br>App Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Stati Uniti occidentali 2|Piano di controllo NAT </br></br>App Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canada centrale|Piano di controllo NAT </br></br>App Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Canada orientale|Piano di controllo NAT </br></br>App Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Regno Unito occidentale|Piano di controllo NAT </br></br>App Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Regno Unito meridionale|Piano di controllo NAT </br></br>App Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa occidentale|Piano di controllo NAT </br></br>App Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa settentrionale|Piano di controllo NAT </br></br>App Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|India centrale|Piano di controllo NAT </br></br>App Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|India meridionale|Piano di controllo NAT </br></br>App Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|India occidentale|Piano di controllo NAT </br></br>App Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Asia sudorientale|Piano di controllo NAT </br></br>App Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Asia orientale|Piano di controllo NAT </br></br>App Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australia orientale|Piano di controllo NAT </br></br>App Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia sud-orientale|Piano di controllo NAT </br></br>App Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia centrale|Piano di controllo NAT </br></br>App Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia centrale 2|Piano di controllo NAT </br></br>App Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Giappone orientale|Piano di controllo NAT </br></br>App Web|13.78.19.235/32 </br></br>52.246.160.72/32|
|Giappone occidentale|Piano di controllo NAT </br></br>App Web|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="workspace-launch-errors"></a>Errori di avvio dell'area di lavoro

L'avvio di un'area di lavoro in una rete virtuale personalizzata ha esito negativo in Azure Databricks con l'errore seguente schermata di accesso: **"Si è verificato un errore durante la creazione dell'area di lavoro. Assicurarsi che la configurazione di rete personalizzata sia corretta e ripetere l'operazione".**

Questo errore è causato da una configurazione di rete requisiti non soddisfatti. Verificare che siano seguite le istruzioni riportate in questo argomento quando è stato creato l'area di lavoro.

### <a name="cluster-creation-errors"></a>Errori di creazione del cluster

**Istanze non raggiungibile: Le risorse non sono raggiungibili tramite SSH.**

Possibile causa: il traffico dal piano di controllo per i ruoli di lavoro è bloccato. Risolvere, garantendo che le regole di sicurezza in ingresso soddisfino i requisiti. Se distribuisce in una rete virtuale esistente collegata alla rete locale, esaminare la configurazione utilizzando le informazioni fornite durante la connessione all'area di lavoro di Databricks Azure alla rete locale.

**Errore di avvio imprevisto: Si è verificato un errore imprevisto durante la configurazione del cluster. Riprovare e, se il problema persiste, contattare Azure Databricks. Messaggio di errore interno: Timeout durante l'inserimento di nodo.**

Possibile causa: il traffico da processi di lavoro agli endpoint di archiviazione di Azure è bloccato. Risolvere, garantendo che le regole di sicurezza in uscita soddisfino i requisiti. Se si usano server DNS personalizzato, verificare anche lo stato dei server DNS nella rete virtuale.

**Errore di avvio del Provider cloud: Si è verificato un errore del provider cloud durante la configurazione del cluster. Vedere la Guida di Azure Databricks per altre informazioni. Codice di errore di Azure: AuthorizationFailed/InvalidResourceReference.**

Possibile causa: la rete virtuale o le subnet non esiste più. Assicurarsi che la rete virtuale e subnet esista.

**Cluster terminata. Motivo: Errore di avvio di Spark: Spark non è stato possibile avviare nel tempo. Questo problema può essere causato da un metastore Hive non funziona correttamente, le configurazioni di Spark non è valide o non funziona correttamente gli script di inizializzazione. Vedere i log del driver Spark per risolvere questo problema e se il problema persiste, contattare Databricks. Messaggio di errore interno: Spark non è stato possibile avviare: Driver non è stato possibile avviare nel tempo.**

Causa possibile: Contenitore non può comunicare con account di archiviazione DBFS o istanza host. Risolvere aggiungendo una route personalizzata per le subnet per l'account di archiviazione DBFS con hop successivo da Internet.

### <a name="notebook-command-errors"></a>Errori dei notebook di comando

**Comando non risponde**

Possibile causa: la comunicazione di ruolo di lavoro al ruolo di lavoro è bloccata. Risolvere, assicurandosi che le regole di sicurezza in ingresso soddisfano i requisiti.

**Flusso di lavoro di notebook non riesce con eccezione: com.databricks.WorkflowException: org.apache.http.conn.ConnectTimeoutException**

Possibile causa: il traffico da processi di lavoro per App Web di Azure Databricks è bloccato. Risolvere, assicurandosi che le regole di sicurezza in uscita soddisfano i requisiti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare dati tramite Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
