---
title: Distribuire Azure Databricks nella rete virtuale
description: Questo articolo descrive come distribuire Azure Databricks alla rete virtuale, nota anche come VNet injection.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 0bb3221c201e6dd4dd17cca8ef7e3ed3331de228
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432653"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Distribuire Azure Databricks nella rete virtuale

La distribuzione predefinita di Azure Databricks è un servizio completamente gestito in Azure: tutte le risorse del piano dati, inclusa una rete virtuale (VNet), vengono distribuite in un gruppo di risorse bloccato. Se è necessaria la personalizzazione della rete, tuttavia, è possibile distribuire Azure Databricks risorse nella propria rete virtuale (detta anche VNet Injection) quando consente di:

* Connetti Azure Databricks ad altri servizi di Azure, ad esempio archiviazione di Azure, in modo più sicuro usando gli endpoint di servizio.
* Connettersi alle origini dati locali per l'uso con Azure Databricks, sfruttando le route definite dall'utente.
* Connettersi Azure Databricks a un'appliance virtuale di rete per ispezionare tutto il traffico in uscita e intraprendere le azioni in base alle regole Consenti e nega.
* Configurare Azure Databricks per l'uso di DNS personalizzato.
* Configurare le regole del gruppo di sicurezza di rete (NSG) per specificare le restrizioni del traffico in uscita.
* Distribuire Azure Databricks cluster nella rete virtuale esistente.

La distribuzione di Azure Databricks risorse alla rete virtuale consente anche di sfruttare i vantaggi degli intervalli CIDR flessibili (ovunque tra/16-/24 per la rete virtuale e tra/18-/26 per le subnet).

  > [!NOTE]
  > Non è possibile sostituire la rete virtuale per un'area di lavoro esistente. Se l'area di lavoro corrente non può contenere il numero necessario di nodi del cluster attivi, creare un'altra area di lavoro in una rete virtuale più grande. Seguire [questi passaggi dettagliati della migrazione](howto-regional-disaster-recovery.md#detailed-migration-steps) per copiare le risorse (notebook, configurazioni del cluster, processi) dall'area di lavoro precedente a quella nuova.

## <a name="virtual-network-requirements"></a>Requisiti della rete virtuale

È possibile usare l'interfaccia di distribuzione dell'area di lavoro Azure Databricks nel portale di Azure per configurare automaticamente una rete virtuale esistente con le subnet necessarie, il gruppo di sicurezza di rete e le impostazioni dell'elenco elementi consentiti oppure è possibile usare Azure Resource Manager modelli per la configurazione della rete virtuale e la distribuzione dell'area di lavoro.

La rete virtuale in cui si distribuisce l'area di lavoro di Azure Databricks deve soddisfare i requisiti seguenti:

### <a name="location"></a>Località

La rete virtuale deve risiedere nella stessa località dell'area di lavoro Azure Databricks.

### <a name="subnets"></a>Subnet

La rete virtuale deve includere due subnet dedicate a Azure Databricks:

   1. Una subnet privata con un gruppo di sicurezza di rete configurato che consente la comunicazione interna del cluster

   2. Una subnet pubblica con un gruppo di sicurezza di rete configurato che consente la comunicazione con il piano di controllo Azure Databricks.

### <a name="address-space"></a>Spazio degli indirizzi

Un blocco CIDR compreso tra/16-/24 per la rete virtuale e un blocco CIDR compreso tra/18-/26 per le subnet private e Public.

### <a name="whitelisting"></a>Inserimento nella whitelist

Tutto il traffico in uscita e in ingresso tra le subnet e il piano di controllo Azure Databricks deve essere inserito nell'elenco elementi consentiti.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

Questa sezione descrive come creare un'area di lavoro Azure Databricks nel portale di Azure e distribuirla nella propria rete virtuale esistente. Azure Databricks aggiorna la rete virtuale con due nuove subnet e gruppi di sicurezza di rete usando gli intervalli CIDR forniti dall'utente, gli elenchi di elementi consentiti e il traffico delle subnet in ingresso e in uscita e distribuisce l'area di lavoro nella rete virtuale aggiornata.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di una rete virtuale in cui distribuire l'area di lavoro Azure Databricks. È possibile usare una rete virtuale esistente o crearne una nuova, ma la rete virtuale deve trovarsi nella stessa area dell'area di lavoro Azure Databricks che si prevede di creare. Un intervallo CIDR compreso tra/16-/24 è obbligatorio per la rete virtuale.

  > [!Warning]
  > Un'area di lavoro con una rete virtuale più piccola, ovvero un intervallo CIDR inferiore, può esaurire gli indirizzi IP (spazio di rete) più rapidamente rispetto a un'area di lavoro con una rete virtuale più grande. Ad esempio, un'area di lavoro con una rete virtuale/24 e/26 subnet può avere un massimo di 64 nodi attivi alla volta, mentre un'area di lavoro con una rete virtuale/20 e/22 subnet può ospitare un massimo di 1024 nodi.

  Le subnet verranno create automaticamente quando si configura l'area di lavoro e si avrà la possibilità di fornire l'intervallo CIDR per le subnet durante la configurazione.

## <a name="configure-the-virtual-network"></a>Configurare la rete virtuale

1. Nella portale di Azure selezionare **+ Crea una risorsa > Analytics > Azure Databricks** per aprire la finestra di dialogo del servizio Azure Databricks.

2. Seguire i passaggi di configurazione descritti in passaggio 2: creare un'area di lavoro Azure Databricks nella guida Introduzione e selezionare l'area di lavoro Distribuisci Azure Databricks nell'opzione rete virtuale.

   ![Crea servizio Azure Databricks](./media/vnet-injection/create-databricks-service.png)

3. Selezionare la rete virtuale che si vuole usare.

   ![Opzioni rete virtuale](./media/vnet-injection/select-vnet.png)

4. Fornire intervalli CIDR in un blocco compreso tra/18-/26 per due subnet, dedicato a Azure Databricks:

   * Verrà creata una subnet pubblica con un gruppo di sicurezza di rete associato che consente la comunicazione con il piano di controllo Azure Databricks.
   * Verrà creata una subnet privata con un gruppo di sicurezza di rete associato che consente la comunicazione interna del cluster.

5. Fare clic su **Crea** per distribuire l'area di lavoro Azure Databricks alla rete virtuale.

## <a name="advanced-resource-manager-configurations"></a>Configurazioni avanzate di Resource Manager

Se si desidera un maggiore controllo sulla configurazione della rete virtuale, ad esempio se si desidera utilizzare le subnet esistenti, utilizzare i gruppi di sicurezza di rete esistenti o creare regole di sicurezza personalizzate, è possibile utilizzare i modelli di Azure Resource Manager seguenti anziché il configurazione della rete virtuale del portale e distribuzione dell'area di lavoro.

### <a name="all-in-one"></a>Tutto in uno

Per creare una rete virtuale, gruppi di sicurezza di rete e Azure Databricks area di lavoro in uno, usare il [modello all-in-One per le aree di lavoro inserite in VNet di databricks](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Quando si usa questo modello, non è necessario eseguire alcuna operazione di inserimento manuale nell'elenco elementi consentiti del traffico della subnet.

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Per creare gruppi di sicurezza di rete con le regole necessarie per una rete virtuale esistente, usare il [modello di gruppo di sicurezza di rete per databricks VNet Injection](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Quando si usa questo modello, non è necessario eseguire alcuna operazione di inserimento manuale nell'elenco elementi consentiti del traffico della subnet.

### <a name="virtual-network"></a>Rete virtuale

Per creare una rete virtuale con le subnet pubbliche e private appropriate, usare il [modello di rete virtuale per VNet injection di databricks](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Se si usa questo modello senza usare anche il modello gruppi di sicurezza di rete, è necessario aggiungere manualmente le regole di elenco elementi consentiti ai gruppi di sicurezza di rete usati con la rete virtuale.

### <a name="azure-databricks-workspace"></a>Area di lavoro Azure Databricks

Per distribuire un'area di lavoro di Azure Databricks a una rete virtuale esistente che contiene subnet pubbliche e private e che i gruppi di sicurezza di rete sono già configurati correttamente, usare il [modello di area di lavoro per databricks VNet Injection](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Se si usa questo modello senza usare anche il modello gruppi di sicurezza di rete, è necessario aggiungere manualmente le regole di elenco elementi consentiti ai gruppi di sicurezza di rete usati con la rete virtuale.

## <a name="whitelisting-subnet-traffic"></a>Elenco elementi consentiti traffico subnet

Se non si usa il [portale di Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-portal) o i [modelli di Azure Resource Manager](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) per creare i gruppi di sicurezza di rete, è necessario includere manualmente il traffico seguente nelle subnet.

|Direzione|Protocol|Source (Sorgente)|Porta di origine|Destinazione|Porta di destinazione|
|---------|--------|------|-----------|-----------|----------------|
|In ingresso|\*|VirtualNetwork|\*|\*|\*|
|In ingresso|\*|IP NAT del piano di controllo|\*|\*|22|
|In ingresso|\*|IP NAT del piano di controllo|\*|\*|5557|
|In uscita|\*|\*|\*|IP webapp|\*|
|In uscita|\*|\*|\*|SQL (tag del servizio)|\*|
|In uscita|\*|\*|\*|Archiviazione (tag del servizio)|\*|
|In uscita|\*|\*|\*|VirtualNetwork|\*|

Traffico della subnet whitelist con i seguenti indirizzi IP. Per SQL (Metastore) e archiviazione (artefatto e archiviazione dei log), è necessario usare i [tag del servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)SQL e di archiviazione.

|Area Azure Databricks|Servizio|IP pubblico|
|-----------------------|-------|---------|
|Stati Uniti Orientali|NAT del piano di controllo </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti orientali 2|NAT del piano di controllo </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centro-settentrionali|NAT del piano di controllo </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centrali|NAT del piano di controllo </br></br>WebApp|23.101.152.95/32 </br></br>40.70.58.221/32|
|Stati Uniti centro-meridionali|NAT del piano di controllo </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Stati Uniti occidentali|NAT del piano di controllo </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Stati Uniti occidentali 2|NAT del piano di controllo </br></br>WebApp|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canada centrale|NAT del piano di controllo </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Canada orientale|NAT del piano di controllo </br></br>WebApp|40.85.223.25/32 </br></br>13.71.184.74/32|
|Regno Unito occidentale|NAT del piano di controllo </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Regno Unito meridionale|NAT del piano di controllo </br></br>WebApp|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa occidentale|NAT del piano di controllo </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa settentrionale|NAT del piano di controllo </br></br>WebApp|23.100.0.135/32 </br></br>52.232.19.246/32|
|India centrale|NAT del piano di controllo </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|India meridionale|NAT del piano di controllo </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|India occidentale|NAT del piano di controllo </br></br>WebApp|104.211.89.81/32 </br></br>104.211.101.14/32|
|Asia sudorientale|NAT del piano di controllo </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Asia orientale|NAT del piano di controllo </br></br>WebApp|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australia orientale|NAT del piano di controllo </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia sud-orientale|NAT del piano di controllo </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia centrale|NAT del piano di controllo </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australia centrale 2|NAT del piano di controllo </br></br>WebApp|13.70.105.50/32 </br></br>13.75.218.172/32|
|Giappone orientale|NAT del piano di controllo </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|
|Giappone occidentale|NAT del piano di controllo </br></br>WebApp|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="workspace-launch-errors"></a>Errori di avvio dell'area di lavoro

L'avvio di un'area di lavoro in una rete virtuale personalizzata ha esito negativo nella schermata di accesso Azure Databricks con l'errore seguente: **"si è verificato un errore durante la creazione dell'area di lavoro. Verificare che la configurazione di rete personalizzata sia corretta e riprovare ".**

Questo errore è causato da una configurazione di rete che non soddisfa i requisiti. Verificare di aver seguito le istruzioni riportate in questo argomento quando è stata creata l'area di lavoro.

### <a name="cluster-creation-errors"></a>Errori di creazione del cluster

**Istanze non raggiungibili: le risorse non sono raggiungibili tramite SSH.**

Possibili cause: il traffico dal piano di controllo ai thread di lavoro è bloccato. Correzione assicurando che le regole di sicurezza in ingresso soddisfino i requisiti. Se si esegue la distribuzione in una rete virtuale esistente connessa alla rete locale, verificare la configurazione usando le informazioni fornite in connessione dell'area di lavoro Azure Databricks alla rete locale.

**Errore di avvio imprevisto: si è verificato un errore imprevisto durante la configurazione del cluster. Riprovare e contattare Azure Databricks se il problema persiste. Messaggio di errore interno: timeout durante l'inserimento del nodo.**

Possibili cause: il traffico da ruoli di lavoro ad endpoint di archiviazione di Azure è bloccato. Correzione assicurando che le regole di sicurezza in uscita soddisfino i requisiti. Se si usano server DNS personalizzati, controllare anche lo stato dei server DNS nella rete virtuale.

**Errore di avvio del provider di servizi cloud: si è verificato un errore del provider di servizi cloud durante la configurazione del cluster. Per ulteriori informazioni, vedere la guida Azure Databricks. Codice di errore di Azure: AuthorizationFailed/InvalidResourceReference.**

Possibili cause: la rete virtuale o le subnet non esistono più. Verificare che la rete virtuale e le subnet esistano.

**Cluster terminato. Motivo: errore di avvio di Spark: non è stato possibile avviare Spark nel tempo. Questo problema può essere causato da un metastore Hive non funzionante, da configurazioni Spark non valide o da script di inizializzazione non funzionante. Per risolvere il problema, vedere i log del driver Spark e contattare databricks se il problema persiste. Messaggio di errore interno: non è stato possibile avviare Spark: il driver non è stato avviato nel tempo.**

Causa possibile: il contenitore non può comunicare con l'istanza di hosting o con l'account di archiviazione DBFS. Correzione mediante l'aggiunta di una route personalizzata alle subnet per l'account di archiviazione DBFS con l'hop successivo su Internet.

### <a name="notebook-command-errors"></a>Errori del comando del notebook

**Il comando non risponde**

Possibili cause: la comunicazione tra i thread di lavoro è bloccata. Correzione assicurandosi che le regole di sicurezza in ingresso soddisfino i requisiti.

**Il flusso di lavoro del notebook ha esito negativo con l'eccezione: com. databricks. Workflowexception: org. Apache. http. conn. ConnectTimeoutException**

Possibili cause: il traffico da Workers a Azure Databricks webapp è bloccato. Correzione assicurandosi che le regole di sicurezza in uscita soddisfino i requisiti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Estrarre, trasformare e caricare dati tramite Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
