<properties
   pageTitle="Convenzioni di denominazione consigliate per le risorse di Azure | Linee guida | Microsoft Azure"
   description="Convenzioni di denominazione consigliate per le risorse di Azure. Come denominare macchine virtuali, account di archiviazione, reti, reti virtuali, subnet e altre entità di Azure"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Convenzioni di denominazione consigliate per le risorse di Azure

La scelta di un nome per qualsiasi risorsa in Microsoft Azure è importante per i motivi seguenti:

- È difficile modificare un nome in un secondo momento.
- I nomi devono soddisfare i requisiti del tipo di risorsa specifica.

Analogamente, le convenzioni di denominazione coerenti ne facilitano l'individuazione. Sono utili anche per comprendere il ruolo di una risorsa in una soluzione.

Questo articolo fornisce un riepilogo delle regole di denominazione e delle restrizioni per le risorse di Azure e offre anche un set di indicazioni di base per le convenzioni di denominazione. È possibile usare queste indicazioni come punto di partenza per le convenzioni specifiche per esigenze particolari.

La chiave per usare correttamente le convenzioni di denominazione consiste nel definirle e seguirle a livello di applicazioni e organizzazioni, adattandole man mano che si distribuiscono più applicazioni e servizi sulla piattaforma Azure.

## Denominazione delle sottoscrizioni

Nel caso di denominazione delle sottoscrizioni di Azure, i nomi dettagliati rendono più chiaro il contesto e lo scopo di ogni sottoscrizione. Quando si lavora in un ambiente che può contenere numerose sottoscrizioni, seguendo una convenzione di denominazione condivisa è possibile migliorarne notevolmente la chiarezza.

Ecco un modello consigliato per la denominazione delle sottoscrizioni:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Società (Company), nella maggior parte dei casi sarà la stessa per ogni sottoscrizione. Tuttavia, alcune aziende possono avere società affiliate all'interno della struttura organizzativa. Queste società possono essere gestite da un gruppo IT centrale, nel qual caso è possibile differenziarle usando sia il nome della società madre (*Contoso*) che il nome della società affiliata (*Northwind*).

- Reparto (Department) è un nome all'interno dell'organizzazione in cui lavora un gruppo di utenti. Questo elemento nello spazio dei nomi è facoltativo. Infatti, alcune società non hanno l'esigenza di un tale livello di dettaglio per via delle loro dimensioni.

- Linea di prodotti (Product line) è un nome specifico per un prodotto o una funzione eseguita all'interno del reparto. Anche se è generalmente facoltativa per le applicazioni e i servizi interni, è consigliabile usarla per i servizi pubblici che dovranno essere facilmente separati e identificati, ad esempio per avere una netta separazione dei record di fatturazione.

- Ambiente (Environment) è il nome che descrive il ciclo di vita della distribuzione di applicazioni o servizi, ad esempio Sviluppo, Controllo di qualità o Produzione.

| Company | Department | Linea di prodotti o servizio | Environment | Nome completo |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Produzione | Contoso SocialGaming AwesomeService Produzione |
| Contoso | SocialGaming | AwesomeService | Sviluppo | Contoso SocialGaming AwesomeService Sviluppo |
| Contoso | IT | InternalApps | Produzione | Contoso IT InternalApps Produzione |
| Contoso | IT | InternalApps | Sviluppo | Contoso IT InternalApps Sviluppo |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Usare affissi per evitare ambiguità

Nella fase di denominazione delle risorse in Azure è consigliabile usare prefissi o suffissi comuni per identificare il tipo e il contesto della risorsa. Anche se tutte le informazioni su tipo, metadati e contesto sono disponibili a livello di codice, l'uso di affissi comuni semplifica l'identificazione visiva. Quando si incorporano gli affissi nella convenzione di denominazione, è importante specificare chiaramente se l'affisso si troverà all'inizio del nome (prefisso) o alla fine (suffisso).

Ecco ad esempio due nomi possibili per un servizio cloud che ospita un motore di calcolo:

- SvcCalculationEngine (prefisso)
- CalculationEngineSvc (suffisso)

Gli affissi possono fare riferimento ai diversi aspetti che descrivono le specifiche risorse. La tabella seguente illustra alcuni esempi usati comunemente.

| Aspetto | Esempio | Note |
| ------ | ------- | ----- |
| Environment | svil, prod, qa | Identifica l'ambiente per la risorsa. |
| Percorso | usOcc (Stati Uniti occidentali), usOr (Stati Uniti orientali) | Identifica l'area in cui la risorsa viene distribuita. |
| Istanza | 01, 02 | Per le risorse che hanno più di un'istanza denominata, come server Web e così via. |
| Prodotto o servizio | service | Identifica il prodotto, l'applicazione o il servizio supportato dalla risorsa. |
| Ruolo | sql, web, messaggistica | Identifica il ruolo della risorsa associata. |

Quando si sviluppa una convenzione di denominazione specifica per la società o i progetti, è importante scegliere un set comune di affissi, nonché la loro posizione (prefisso o suffisso).

## Regole di denominazione e restrizioni

Ogni tipo di risorsa o di servizio in Azure applica un set di restrizioni di denominazione e un ambito. Qualsiasi convenzione di denominazione o modello deve rispettare le regole di denominazione e l'ambito richiesti. Ad esempio, mentre il nome di una VM è mappato a un nome DNS, e quindi deve essere univoco in Azure, l'ambito del nome di una rete virtuale è associato al gruppo di risorse in cui viene creato.

In generale, evitare caratteri speciali (`-` o `_`) come primo o ultimo carattere in un nome qualsiasi, perché causeranno errori nella maggior parte delle regole di convalida.

| Categoria | Servizio o entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Gruppo di risorse | Gruppo di risorse | Globale | 1-64 | Non fa distinzione tra maiuscole e minuscole | Alfanumerico, carattere di sottolineatura e trattino | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Gruppo di risorse | Set di disponibilità | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole | Alfanumerico, carattere di sottolineatura e trattino | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Generale | Tag | Entità associata | 512 (nome), 256 (valore) | Non fa distinzione tra maiuscole e minuscole | Alfanumerico | `"key" : "value"` | `"department" : "Central IT"` |
| Calcolo | Macchina virtuale | Gruppo di risorse | 1-15 | Non fa distinzione tra maiuscole e minuscole | Alfanumerico, carattere di sottolineatura e trattino | `<name>-<role>-<instance>` | `profx-sql-001` |
| Archiviazione | Nome account di archiviazione (dati) | Globale | 3-24 | Minuscolo | Alfanumerico | `<service short name><type><number>` | `profxdata001` |
| Archiviazione | Nome account di archiviazione (dischi) | Globale | 3-24 | Minuscolo | Alfanumerico | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Archiviazione | Nome del contenitore | Account di archiviazione | 3-63 |	Minuscolo | Alfanumerico e trattino | `<context>` | `logs` |
| Archiviazione | Nome del BLOB | Contenitore: | 1-1024 | Fa distinzione tra maiuscole e minuscole. | Qualsiasi carattere URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Archiviazione | Nome della coda | Account di archiviazione | 3-63 | Minuscolo | Alfanumerico e trattino | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Archiviazione | Nome tabella | Account di archiviazione | 3-63 |Non fa distinzione tra maiuscole e minuscole | Alfanumerico | `<service short name>-<context>` | `awesomeservice-logs` |
| Archiviazione | Nome file | Account di archiviazione | 3-63 | Minuscolo | Alfanumerico | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Rete | Rete virtuale | Gruppo di risorse | 2-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<service short name>-[section]-vnet` | `profx-vnet` |
| Rete | Subnet | Rete virtuale padre | 2-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, carattere di sottolineatura, trattino e punto | `<role>-subnet` | `gateway-subnet` |
| Rete | Interfaccia di rete | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Rete | Gruppo di sicurezza di rete | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Rete | Regola del gruppo di sicurezza di rete | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<descriptive context>` | `sql-allow` |
| Rete | Indirizzo IP pubblico | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<vm or service name>-pip` | `profx-sql1-pip` |
| Rete | Bilanciamento del carico | Gruppo di risorse | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `<service or role>-lb` | `profx-lb` |
| Rete | Configurazione di regole del servizio di bilanciamento del carico | Bilanciamento del carico | 1-80 | Non fa distinzione tra maiuscole e minuscole. | Alfanumerico, trattino, carattere di sottolineatura e punto | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Organizzazione delle risorse con tag

Azure Resource Manager supporta l'aggiunta di tag alle entità con stringhe di testo arbitrario per identificare il contesto e semplificare l'automazione. Ad esempio, un tag come `"sqlVersion: "sql2014ee"` può identificare tutte le macchine virtuali in una distribuzione che esegue SQL Server 2014 Enterprise Edition ai fini dell'esecuzione di uno script automatizzato su di esse. I tag devono essere usati per ottimizzare e migliorare il contesto unitamente alle convenzioni di denominazione scelte.

> [AZURE.TIP] Un altro vantaggio di tag è che si estendono ai gruppi di risorse e quindi consentono di collegare e mettere in correlazione le entità tra le diverse distribuzioni.

Ogni risorsa o gruppo di risorse può avere un massimo di **15** tag. Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri.

Per altre informazioni sull'aggiunta di tag alle risorse, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

Ecco alcuni casi d'uso dei tag comuni:

- **Fatturazione**: raggruppamento di risorse e relativa associazione a codici di fatturazione o chargeback.
- **Identificazione del contesto del servizio**: identificazione dei gruppi di risorse tra di essi per raggruppamento e operazioni comuni.
- **Controllo di accesso e contesto di sicurezza**: identificazione di ruoli amministrativi in base a portfolio, sistema, servizio, app, istanza, e così via. 

> [AZURE.TIP] Aggiungere tag preventivamente, aggiungerli spesso. È preferibile avere uno schema di base per l'aggiunta di tag e apportarvi modifiche nel tempo, piuttosto che doverlo fare in seguito.

Ecco un esempio di alcuni approcci comuni all'aggiunta di tag:

| Nome del tag | Chiave | Esempio | Commento |
| -------- | --- | ------- | ------- |
| Fattura a/ID chargeback interno | billTo | `IT-Chargeback-1234` | Codice di fatturazione o I/O interno |
| Operatore o utente direttamente responsabile | managedBy | `joe@contoso.com` | Alias o indirizzo di posta elettronica |
| Nome del progetto | project-name | `myproject` | Nome della linea di prodotti o del progetto |
| Versione del progetto | project-version | `3.4` | Versione della linea di prodotti o del progetto |
| Environment | environment | `<Production, Staging, QA >` | Identificatore dell'ambiente | 
| Livello | tier | `Front End, Back End, Data` | Identificazione di livello o del ruolo/contesto |
| Profilo dei dati | dataProfile | `Public, Confidential, Restricted, Internal` | Riservatezza dei dati archiviati nella risorsa |
 
## Suggerimenti e consigli

A seconda del tipo di applicazione, è possibile che determinati tipi di risorse richiedano maggiore attenzione a livello di denominazione e convenzioni. Di seguito sono elencati dettagli aggiuntivi e il relativo contesto.

### Macchine virtuali

In particolare nelle topologie di grandi dimensioni, la denominazione corretta delle macchine virtuali faciliterà significativamente l'identificazione del ruolo e dello scopo di ogni computer, consentendo anche la creazione di script più prevedibili.

> [AZURE.WARNING] Si noti che ogni macchina virtuale in Azure ha un nome di risorsa di Azure e un nome host del sistema operativo. Se il nome della risorsa e il nome host sono diversi, la gestione di queste VM può risultare difficile e deve essere evitata, ad esempio, se la macchina virtuale viene creata da un file VHD che già contiene un sistema operativo configurato con un nome host.

- [Convenzioni di denominazione per le macchine virtuali di Windows Server](https://support.microsoft.com/it-IT/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	Account ed entità di archiviazione

Sono disponibili due casi d'uso principali per gli account di archiviazione, ovvero il backup dei dischi per le VM e l'archiviazione dei dati in BLOB, code e tabelle. Gli account di archiviazione usati per i dischi delle VM devono seguire la convenzione di denominazione basata sull'associazione al nome della VM padre, con la potenziale necessità di avere più account di archiviazione per gli SKU delle VM di fascia alta e di sfruttare anche un suffisso numerico.

> [AZURE.TIP] Gli account di archiviazione, per dati o dischi, devono seguire una convenzione di denominazione che consenta di sfruttare più account di archiviazione, ovvero usando sempre un suffisso numerico.

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati del BLOB nell'account di archiviazione di Azure. L'endpoint predefinito per il servizio BLOB è`https://mystorage.blob.core.windows.net`.

Se si esegue il mapping di un dominio personalizzato, ad esempio www.contoso.com, all'endpoint BLOB per l'account di archiviazione, sarà possibile accedere anche ai dati BLOB dell'account di archiviazione usando tale dominio. Ad esempio, con un nome di dominio personalizzato, è possibile accedere a `http://mystorage.blob.core.windows.net/mycontainer/myblob` come `http://www.contoso.com/mycontainer/myblob`.

Per altre informazioni sulla configurazione di questa funzionalità, vedere [Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB](../storage/storage-custom-domain-name.md).

Per altre informazioni sulla denominazione di tabelle, contenitori e BLOB:

- [Denominazione e riferimento a contenitori, BLOB e metadati](https://msdn.microsoft.com/library/dd135715.aspx)
- [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx)
- [Denominazione di tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nome di BLOB può contenere qualsiasi combinazione di caratteri, ma i caratteri URL riservati devono essere correttamente preceduti da un carattere di escape. Evitare i nomi di BLOB che terminano con un punto (.), una barra (/) o una sequenza o una combinazione di entrambi. Per convenzione, la barra è il separatore di directory **virtuale**. Non usare una barra rovesciata () nel nome di un BLOB. Le API client possono consentirne l'uso, ma non eseguono l'hashing correttamente e le firme non corrisponderanno.

Non è possibile modificare il nome del contenitore o dell'account di archiviazione dopo che è stato creato. È necessario eliminarlo e crearne uno nuovo se si vuole usare un nuovo nome.

> [AZURE.TIP] È consigliabile definire una convenzione di denominazione per tutti gli account di archiviazione e i tipi prima di avventurarsi nello sviluppo di un nuovo servizio o una nuova applicazione.

## Esempio: distribuzione di un servizio a più livelli

In questo esempio verrà definita una configurazione del servizio a più livelli, costituita da server IIS front-end (ospitati in VM di Windows Server), con SQL Server (ospitato in due VM di Windows Server), un cluster ElasticSearch (ospitato in 6 VM Linux) e gli account di archiviazione, le reti virtuali, il gruppo di risorse e il servizio di bilanciamento del carico associati.

Si inizierà con la definizione delle convenzioni contestuali per questa applicazione:

| Entità | Convenzione | Descrizione |
| ------ | ---------- | ------------ |  
| Nome servizio | `profx` | Nome breve dell'applicazione o del servizio distribuito. |
| Environment | `prod` | Riguarda la distribuzione di produzione (a differenza di controllo di qualità, test e così via) |

Da questi dati di riferimento è quindi possibile mappare le convenzioni per ogni tipo di risorsa:

| Tipo di risorsa | Convenzione di base | Esempio |
| ------------- | --------------- | ------- |
| Sottoscrizione | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Gruppo di risorse | `servicename-rg` | `profx-rg` |
| Rete virtuale | `servicename-vnet` | `profx-vnet` |
| Subnet | `role-subnet` | `sql-vnet` |
| Bilanciamento del carico | `servicename-lb` | `profx-lb` |
| Macchina virtuale | `servicename-role[number]` | `profx-sql0` |
| Account di archiviazione | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Come illustrato nel diagramma seguente:

![diagramma della topologia dell'applicazione](media/guidance-naming-conventions/guidance-naming-convention-example.png "Topologia dell'applicazione di esempio")

## Esempio: script dell'interfaccia della riga di comando di Azure per la distribuzione dell'esempio precedente

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->