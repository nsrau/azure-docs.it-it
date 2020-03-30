---
title: Sviluppare un'applicazione Web sicura Documenti Microsoft
description: Questa semplice app di esempio implementa procedure consigliate per la sicurezza che migliorano l'applicazione e la sicurezza dell'organizzazione durante lo sviluppo in Azure.This simple sample app implements security best practices that improve your application and your organization's security posture when you develop on Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 75890efebc42b74c56fb95ed1803152b516588b9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385215"
---
# <a name="develop-a-secure-web-app"></a>Sviluppare un'app Web sicura

Questo esempio è una semplice app Python che visualizza una pagina Web contenente collegamenti alle risorse di sicurezza per lo sviluppo di app in Azure.This sample is a simple Python app that displays a web page containing links to security resources for developing apps on Azure. L'app implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e la sicurezza dell'organizzazione quando si sviluppano app in Azure.The app implements security best practices that can help improve your application and your organization's security posture when you develop apps on Azure.

È necessario seguire i passaggi descritti in questo articolo in sequenza per assicurarsi che i componenti dell'applicazione siano configurati correttamente. Il database, il servizio app di Azure, l'istanza dell'insieme di credenziali delle chiavi di Azure e l'istanza del gateway applicazione di Azure dipendono l'uno dall'altro.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare i componenti e i servizi.

L'app di esempio è destinata ai principianti che sviluppano applicazioni in Azure che desiderano implementare misure di sicurezza nelle applicazioni.

Nello sviluppo e nella distribuzione di questa app, imparerai come:

- Creare un'istanza dell'insieme di credenziali delle chiavi di Azure, archiviare e recuperare segreti da essa.
- Distribuire il database di Azure per PostgreSQL, configurare password sicure e autorizzare l'accesso.
- Eseguire un contenitore Linux alpino in App Web di Azure per Linux e abilitare le identità gestite per le risorse di Azure.Run an Alpine Linux container on Azure Web Apps for Linux and enable managed identities for Azure resources.
- Creare e configurare un'istanza del gateway applicazione di Azure con un firewall che usa Set di regole [OWASP Top 10](https://coreruleset.org/).
- Abilitare la crittografia dei dati in transito e inattivi usando i servizi di Azure.Enable encryption of data in transit and at rest by using Azure services.

Dopo aver sviluppato e distribuito questa app, sarà stata configurata l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

![App Web di esempio](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architecture

L'app è una tipica applicazione a più livelli con tre livelli. Il livello front-end, back-end e database con componenti di monitoraggio e gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-web-app/architecture.png)

L'architettura è costituita dai seguenti componenti:

- [Gateway applicazione di Azure](../../application-gateway/index.yml). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [App Web di Azure in Linux](../../app-service/containers/app-service-linux-intro.md). Fornisce il runtime del contenitore per eseguire l'app Python in un ambiente Linux.
- [Archivio chiavi di Azure](../../key-vault/index.yml). Archivia e crittografa i segreti della nostra app e gestisce la creazione di criteri di accesso che li circondano.
- [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql/). Archivia in modo sicuro i dati della nostra app.
- [Centro sicurezza di Azure](../../security-center/index.yml) e [Azure Application Insights.](../../azure-monitor/app/app-insights-overview.md) Fornisce monitoraggio e avvisi sul funzionamento della nostra app.

## <a name="threat-model"></a>Modello di minaccia

La modellazione delle minacce è il processo di identificazione di potenziali minacce alla sicurezza per l'azienda e l'applicazione e quindi garantire che sia in atto un piano di attenuazione appropriato.

In questo esempio è stato usato [Microsoft Threat Modeling Tool](threat-modeling-tool.md) per implementare la modellazione delle minacce per l'app di esempio sicura. Con la creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle prime fasi del processo di sviluppo. Ciò consente di risparmiare tempo e denaro in un secondo momento.

Questo è il modello di minaccia per l'app di esempio:This is the threat model for the sample app:

![Modello di minaccia](./media/secure-web-app/threat-model.png)

Alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce sono illustrate nella schermata seguente. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e richiede agli sviluppatori di pensare a come mitigare i problemi.

![Output del modello di minaccia](./media/secure-web-app/threat-model-output.png)

For example, SQL injection in the preceding threat model output is mitigated by sanitizing user inputs and by using stored functions in Azure Database for PostgreSQL. Questa attenuazione impedisce l'esecuzione arbitraria di query durante le operazioni di lettura e scrittura dei dati.

Gli sviluppatori migliorano la sicurezza complessiva del sistema attenuando ciascuna delle minacce nell'output del modello di minaccia.

## <a name="deployment"></a>Distribuzione

Le opzioni seguenti consentono di eseguire Linux nel servizio app di Azure:The following options let you run Linux on Azure App Service:

- Scegliere un contenitore dall'elenco dei contenitori Microsoft predefiniti in Azure creati con tecnologie di supporto (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Utilizzare un contenitore personalizzato. Selezionare i propri registri contenitori come origine dell'immagine e basarsi sulle numerose tecnologie disponibili che supportano HTTP.

In questo esempio verrà eseguito lo script di distribuzione che distribuirà l'app Web nel servizio app e verranno create le risorse.

L'app può usare i diversi modelli di distribuzione illustrati di seguito:The app can use the different deployment models shown below:

![Diagramma del flusso di dati di distribuzione](./media/secure-web-app/deployment.png)

Esistono molti modi per distribuire le app in Azure, tra cui:There are many ways to deploy apps on Azure, including:

- Modelli di Gestione risorse di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- Portale di Azure
- Azure DevOps

Questa applicazione utilizzato:

- [Docker](https://docs.docker.com/) per creare e compilare le immagini del contenitore.
- [Interfaccia di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) per la distribuzione.
- [Docker Hub](https://hub.docker.com/) come registro contenitori.

## <a name="security-considerations"></a>Considerazioni sulla sicurezza

### <a name="network"></a>Rete

L'app di esempio usa la crittografia SSL end-to-end per i dati in transito che scorrono dentro e fuori la rete. Il gateway è configurato con un certificato autofirmato.
> [!IMPORTANT]
> In questa dimostrazione viene utilizzato un certificato autofirmato. In un ambiente di produzione, è necessario ottenere certificati da un'autorità di certificazione (CA) verificata.

Il firewall dell'applicazione controlla anche il traffico in ingresso e avvisa gli amministratori quando viene rilevato traffico dannoso nel traffico di rete.
Gateway applicazione riduce la possibilità per le minacce DDoS e SQL injection individuate nel modello di minaccia.

### <a name="identity"></a>Identità

Per accedere al portale, l'app di esempio usa Multi-Factor Authentication per gli amministratori di Azure Active Directory (Azure AD) a cui è assegnato l'accesso alle risorse.
L'app di esempio usa le identità gestite per ottenere le autorizzazioni per leggere e recuperare segreti dall'insieme di credenziali delle chiavi di Azure, assicurandoche che l'app non debba codificare le credenziali e i token per leggere i segreti. Azure AD crea automaticamente le entità servizio necessarie per leggere e modifica i segreti quando vengono usate identità gestite.

Le identità gestite per le risorse di Azure e l'autenticazione a più fattori rendono più difficile per gli avversari ottenere privilegi e aumentare i privilegi nel sistema. Questa minaccia è stata sottolineata nel modello di minaccia.
L'app usa OAuth, che consente agli utenti registrati nell'applicazione OAuth di accedere all'app.

### <a name="storage"></a>Archiviazione

I dati nel database PostgreSQL vengono crittografati automaticamente dal database di Azure per PostgreSQL.Data in the PostgreSQL database is encrypted at restonautomatically by Azure Database for PostgreSQL. Il database autorizza gli indirizzi IP del servizio app in modo che solo l'app Web del servizio app distribuita possa accedere alle risorse del database con le credenziali di autenticazione corrette.

### <a name="logging-and-auditing"></a>Registrazione e controllo

L'app implementa la registrazione usando Application Insights per tenere traccia di metriche, log ed eccezioni che si verificano. Questa registrazione fornisce metadati sufficienti per informare gli sviluppatori e i membri del team operativo sullo stato dell'app. Fornisce inoltre dati sufficienti per eseguire il backtracking in caso di incidenti di sicurezza.

## <a name="cost-considerations"></a>Considerazioni sul costo

Se non si dispone già di un account Azure, è possibile crearne uno gratuito. Vai alla [pagina dell'account gratuito](https://azure.microsoft.com/free/) per iniziare, scopri cosa puoi fare con un account Azure gratuito e scopri quali prodotti sono gratuiti per 12 mesi.

Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, devi pagare alcune funzionalità premium. Man mano che l'app viene ridimensionata e i livelli e le versioni di valutazione gratuiti offerti da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="deploy-the-solution"></a>Distribuire la soluzione

### <a name="prerequisites"></a>Prerequisiti

Per mettere in funzione l'applicazione, è necessario installare questi strumenti:

- Editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia della riga di comando](/cli/azure/install-azure-cli) di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) sul vostro sistema. Git viene utilizzato per clonare il codice sorgente in locale.
- [jq](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice.

È necessaria una sottoscrizione di Azure per distribuire le risorse dell'app di esempio. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/) per testare l'app di esempio.

Dopo aver installato questi strumenti, è possibile distribuire l'app in Azure.After installing these tools, you're ready to deploy the app on Azure.

### <a name="environment-setup"></a>Configurazione dell'ambiente

Eseguire gli script di distribuzione per configurare l'ambiente e la sottoscrizione:

1. Per clonare il repository del codice sorgente, utilizzare questo comando Git:To clone the source code repository, use this Git command:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Per spostarsi nella directory, utilizzare questo comando:

   ```shell
   cd tutorial-project/scripts
   ```

3. Ci sono file nella cartella degli script che sono specifici per la piattaforma che si sta utilizzando (Windows o Linux). Poiché l'interfaccia della riga di comando di Azure è già stata installata, accedere all'account di Azure al prompt dei comandi eseguendo questo comando dell'interfaccia della riga di comando di Azure:As the Azure CLI has already been installed, sign in to the Azure account at the command prompt by running this Azure CLI command:

   ```azurecli-interactive
   az login
   ```

Il browser si aprirà, accedi con le tue credenziali. Dopo l'accesso, è possibile iniziare a distribuire le risorse dal prompt dei comandi.

Gli script `deploy-powershell.ps1` `deploy-bash.sh` di distribuzione e contengono codice che distribuisce l'intera applicazione.
Per distribuire la soluzione:

1. Se si è in `deploy-powershell.ps1` PowerShell `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` eseguire il file digitando sostituendo il nome dell'area e del gruppo di risorse con le aree di Azure adatte e un nome per il gruppo di risorse
2. Se si è su `deploy-bash.sh` Linux `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`eseguire il file digitando , potrebbe essere necessario rendere il file eseguibile digitando`chmod +x deploy-bash.sh`

Gli esempi seguenti illustrano i frammenti dei componenti chiave. È possibile distribuire gli esempi singolarmente o con il resto dei componenti eseguendo i file di distribuzione.

### <a name="implementation-guidance"></a>Indicazioni relative all'implementazione

Lo script di distribuzione è uno script che può essere suddiviso in quattro fasi. Ogni fase distribuisce e configura una risorsa di Azure presente nel [diagramma dell'architettura.](#architecture)

Le quattro fasi sono:

- Distribuire l'insieme di credenziali delle chiavi di Azure.Deploy Azure Key Vault.
- Distribuire il database di Azure per PostgreSQL.Deploy Azure Database for PostgreSQL.
- Distribuire app Web di Azure in Linux.Deploy Azure Web Apps on Linux.
- Distribuire il gateway applicazione con il firewall dell'applicazione Web.

Ogni fase si basa su quella precedente utilizzando la configurazione delle risorse distribuite in precedenza.

Per completare la procedura di implementazione, assicurarsi di aver installato gli strumenti elencati in [Prerequisiti](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Distribuire l'insieme di credenziali delle chiavi di AzureDeploy Azure Key Vault

In questa sezione viene creata e distribuita un'istanza di Archiviazione delle chiavi di Azure usata per archiviare segreti e certificati.

Dopo aver completato la distribuzione, si dispone di un'istanza di Archiviazione delle chiavi di Azure distribuita in Azure.After you complete the deployment, you have an Azure Key Vault instance deployed on Azure.

Per distribuire l'insieme di credenziali delle chiavi di Azure tramite l'interfaccia della riga di comando di Azure:To deploy Azure Key Vault by using Azure CLI

1. Dichiarare le variabili per L'insieme di credenziali delle chiavi di Azure.Declare the variables for Azure Key Vault.
2. Registrare il provider dell'insieme di credenziali delle chiavi di Azure.Register the Azure Key Vault provider.
3. Creare il gruppo di risorse per l'istanza.
4. Creare l'istanza di Azure Key Vault nel gruppo di risorse creato nel passaggio 3.Create the Azure Key Vault instance in the resource group created in step 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

È consigliabile usare le identità gestite per le risorse di Azure nelle app che usano l'insieme di credenziali delle chiavi per accedere alle risorse. La sicurezza aumenta quando le chiavi di accesso all'insieme di credenziali delle chiavi non vengono archiviate nel codice o nella configurazione.

#### <a name="deploy-azure-database-for-postgresql"></a>Distribuire il database di Azure per PostgreSQLDeploy Azure Database for PostgreSQL

Database di Azure per PostgreSQL funziona nel modo seguente, creare innanzitutto il server di database, quindi creare il database in cui archiviare lo schema e i dati.

Dopo aver completato la distribuzione, si dispone di un server PostgreSQL e un database in esecuzione in Azure.After you complete the deployment, you have a PostgreSQL server and database running on Azure.

Per distribuire il database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure:To deploy Azure Database for PostgreSQL by using Azure CLI:

1. Aprire un terminale con l'interfaccia della riga di comando di Azure e la configurazione della sottoscrizione di Azure.Open a terminal with Azure CLI and your Azure subscription setup.
2. Generare una combinazione sicura di nome utente e password utilizzata per accedere al database. Questi devono essere archiviati nell'insieme di credenziali delle chiavi di Azure per le app che li usano.
3. Creare l'istanza del server PostgreSQL.
4. Creare un database nell'istanza del server creata nel passaggio 3.
5. Eseguire gli script PostgreSQL nell'istanza PostgreSQL.

Il codice seguente si basa sui segreti PGUSERNAME e PGPASSWORD archiviati in Azure KeyVault dal passaggio KeyVault di distribuzione precedente.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Dopo aver distribuito il database, è necessario archiviare le credenziali e la stringa di connessione nell'insieme di credenziali delle chiavi di Azure.After you deploy the database, you need to store its credentials and connection string in Azure Key Vault.
Nella cartella degli script è `functions.sql` presente un file che contiene il codice PL/pgSQL che crea le funzioni memorizzate quando viene eseguito. L'esecuzione di questo file parametrizza gli input per limitare l'inserimento SQL.

PostgreSQL è incluso in `psql` un pacchetto con uno strumento chiamato che viene utilizzato per connettersi al database. Per `functions.sql`eseguire , è necessario connettersi all'istanza del database di Azure per PostgreSQL dal computer locale ed eseguirla da tale istanza. L'installazione dello strumento psql è inclusa nell'installazione predefinita per PostgreSQL in ogni sistema operativo.
Per ulteriori informazioni, vedere la documentazione di [psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell `psql` include anche lo strumento. È possibile usare Cloud Shell direttamente dal portale di Azure selezionando l'icona di Cloud Shell.You can use Cloud Shell directly from the Azure portal by selecting the Cloud Shell Icon.

Per abilitare l'accesso remoto all'istanza PostgreSQL, è necessario autorizzare l'indirizzo IP in PostgreSQL.
Per abilitare questo accesso, passare alla scheda **Protezione connessione,** selezionando **Aggiungi IP client**e salvando le nuove impostazioni.

![Autorizzare l'IP clientAuthorize client IP](./media/secure-web-app/add-client-ip-postgres.png)

Se si usa Cloud Shell anziché lo strumento psql locale, selezionare **Consenti accesso ai servizi** di Azure e modificarne il valore in **ON** per consentire l'accesso a Cloud Shell.

Eseguire quindi l'istanza seguente con i parametri della stringa di connessione dalla scheda Stringhe di connessione dell'istanza PostgreSQL nel portale di Azure.Then connect to the instance by running the below psql command with connection string parameters from the **Connection strings** tab of the PostgreSQL instance on the Azure portal.
Sostituire le parentesi graffe vuote con i parametri del pannello Stringa di connessione del database e la password con la password dell'insieme di chiavi di Azure.Replace the empty braces with parameters from the Connection String blade of the database and the password with the password from Azure Key Vault.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Eseguire lo script PL/pgSQL seguente dopo aver verificato di essere connessi al database. Lo script crea le funzioni memorizzate utilizzate per inserire i dati nel database.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Per altre informazioni su come configurare la verifica SSL e autorità di certificazione (CA) per PostgreSQL, vedere Configurare la connettività SSL nel database di [Azure per PostgreSQL.](/azure/postgresql/concepts-ssl-connection-security)

Nel contenitore è incluso un certificato radice. Le operazioni adottate per ottenere il certificato sono:

1. Scaricare il file del certificato [dall'autorità di certificazione](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Scaricare e installare OpenSSL sulla macchina](/azure/postgresql/concepts-ssl-connection-security).
3. Decodificare il file del certificato:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Ulteriori informazioni su come configurare la sicurezza SSL per PostgreSQL sono disponibili qui [Configurare la protezione](/azure/postgresql/concepts-ssl-connection-security)della connessione SSL.

#### <a name="deploy-azure-web-apps-on-linux"></a>Distribuire app Web di Azure in LinuxDeploy Azure Web Apps on Linux

È possibile creare facilmente servizi Linux nel servizio app di Azure, poiché Azure offre un set di contenitori e immagini predefiniti per linguaggi ampiamente usati, ad esempio Python, Ruby, C' e Java. Azure supporta anche contenitori personalizzati, che consentono praticamente l'esecuzione di tutti i linguaggi di programmazione nella piattaforma del servizio app di Azure.Azure also supports custom containers, which can allow virtually all programming languages to run on the Azure App Service platform.

L'app distribuita è una semplice app Python che viene eseguita sull'ultima distribuzione di Ubuntu Linux. Si connette alle istanze di Azure Key Vault e PostgreSQL create nelle sezioni precedenti rispettivamente per la gestione delle credenziali e l'archiviazione dei dati.

Il seguente file Docker è disponibile nella cartella radice dell'app:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Il Dockerfile precedente viene usato per compilare il contenitore `mcr.microsoft.com/samples/basic-linux-app`ospitato nel Registro di sistema del contenitore di Azure in .

Il codice seguente:

1. Dichiara le variabili e i nomi per l'istanza del servizio app.
2. Crea il gruppo di risorse per il piano di servizio app.
3. Esegue il provisioning di un'istanza di app Web di Azure in contenitori Linux.Provisions an Azure Web Apps on Linux containers instance.
4. Abilita la registrazione per il contenitore di app Web.
5. Imposta alcune configurazioni dell'app nelle impostazioni dell'app del contenitore.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Questo script crea un'identità assegnata per l'istanza del servizio app che può essere usata con MSI per interagire con l'insieme di credenziali delle chiavi di Azure senza segreti di codifica rigida nel codice o nella configurazione.

Passare all'istanza dell'insieme di **credenziali**delle chiavi di Azure nel portale per autorizzare l'identità assegnata nella scheda dei criteri di accesso. In **Seleziona entità**cercare il nome dell'applicazione simile al nome dell'istanza del servizio app creata.
Un'entità servizio collegata all'applicazione deve essere visibile. Selezionarlo e salvare la pagina dei criteri di accesso, come illustrato nella schermata seguente.

Poiché l'applicazione deve solo recuperare le chiavi, selezionare l'autorizzazione **Get** nelle opzioni dei segreti, consentendo l'accesso riducendo i privilegi concessi.

![Criteri di accesso insieme di credenziali delle chiavi](./media/secure-web-app/kv-access-policy.png)

*Creare un criterio di accesso dell'insieme di credenziali delle chiaviCreate a Key Vault access policy*

Salvare i criteri di accesso e quindi salvare la nuova modifica nella scheda Criteri di **accesso** per aggiornare i criteri.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuire il gateway applicazione con il web application firewall abilitato

Nelle app Web, non è consigliabile esporre i servizi direttamente al mondo esterno su Internet.
Il bilanciamento del carico e le regole del firewall forniscono maggiore sicurezza e controllo sul traffico in ingresso e consentono di gestirlo.

Per distribuire un'istanza del gateway applicazione:

1. Creare il gruppo di risorse per ospitare il gateway applicazione.
2. Effettuare il provisioning di una rete virtuale da collegare al gateway.
3. Creare una subnet per il gateway nella rete virtuale.
4. Effettuare il provisioning di un indirizzo IP pubblico.
5. Effettuare il provisioning del gateway applicazione.
6. Abilitare il firewall dell'applicazione Web sul gateway.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Lo script precedente:

1. Crea un nuovo certificato autofirmato in Azure.
2. Scarica il certificato autofirmato come file con codifica base64.
3. Genera una password per il certificato autofirmato.
4. Esporta il certificato come file PFX firmato con la password.
5. Archivia la password del certificato nell'insieme di credenziali delle chiavi di Azure.Stores the certificate's password in Azure Key Vault.

In questa sezione viene distribuito il gateway applicazione:This section deploys the application gateway:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Dopo aver completato la distribuzione, è possibile avere un gateway applicazione con il firewall dell'applicazione Web abilitato.

L'istanza del gateway espone la porta 443 per HTTPS. Questa configurazione garantisce che l'app sia accessibile solo sulla porta 443 tramite HTTPS.

Bloccare le porte inutilizzate e limitare l'esposizione alla superficie di attacco è una procedura consigliata per la sicurezza.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Aggiungere gruppi di sicurezza di rete all'istanza del servizio appAdd network security groups to the App Service instance

Le istanze del servizio app possono essere integrate con reti virtuali. Questa integrazione consente di configurarli con criteri di gruppo di sicurezza di rete che gestiscono il traffico in ingresso e in uscita dell'app.

1. Per abilitare questa funzionalità, nel pannello dell'istanza del servizio app di Azure, in **Impostazioni,** selezionare **Rete**. Nel riquadro destro, in **Integrazione rete virtuale**, selezionare **Fare clic qui per configurare**.

   ![Nuova integrazione di rete virtuale](./media/secure-web-app/app-vnet-menu.png)

    *Nuova integrazione di rete virtuale per il servizio app*

1. Nella pagina successiva selezionare **Aggiungi vNET (anteprima)**.

1. Nel menu successivo selezionare la rete virtuale creata `hello-vnet`nella distribuzione che inizia con . È possibile creare una nuova subnet o selezionarne una esistente.
   In questo caso, creare una nuova subnet. Impostare **l'intervallo** di indirizzi su **10.0.3.0/24** e denominare la subnet **dell'app**subnet della subnet.

   ![Configurazione della rete virtuale del servizio app](./media/secure-web-app/app-vnet-config.png)

    *Configurazione della rete virtuale per il servizio appVirtual network configuration for App Service*

Dopo aver abilitato l'integrazione della rete virtuale, è possibile aggiungere gruppi di sicurezza di rete all'app.

1. Utilizzare la casella di ricerca , cercare i gruppi di sicurezza di **rete**. Selezionare Gruppi di sicurezza di **rete** nei risultati.

    ![Cercare gruppi di sicurezza di reteSearch for network security groups](./media/secure-web-app/nsg-search-menu.png)

    *Cercare gruppi di sicurezza di reteSearch for network security groups*

2. Nel menu successivo selezionare **Aggiungi**. Immettere il **nome** del gruppo di sicurezza di rete e il **gruppo di** risorse in cui deve essere posizionato. Questo gruppo di sicurezza di rete verrà applicato alla subnet del gateway applicazione.

    ![Creare un gruppo di sicurezza di baseCreate an NSG](./media/secure-web-app/nsg-create-new.png)

    *Creare un gruppo di sicurezza di baseCreate an NSG*

3. Dopo aver creato il gruppo di sicurezza di gruppo, selezionarlo. Nel pannello selezionare Regole di **sicurezza in ingresso**in **Impostazioni**. Configurare queste impostazioni per consentire le connessioni in ingresso nel gateway applicazione tramite la porta 443.Configure these settings to allow connections in the application gateway over port 443.

   ![Configurare il gruppo di sicurezza di gruppo](./media/secure-web-app/nsg-gateway-config.png)

   *Configurare il gruppo di sicurezza di gruppo*

4. Nelle regole in uscita per il gruppo di sicurezza di rete del gateway aggiungere una regola `AppService`che consenta le connessioni in uscita all'istanza del servizio app creando una regola destinata al tag del servizio:

   ![Aggiungere regole in uscita per il gruppo di sicurezza di gruppoAdd outbound rules for the NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Aggiungere regole in uscita per il gruppo di sicurezza di gruppoAdd outbound rules for the NSG*

    Aggiungere un'altra regola in uscita per consentire al gateway di inviare regole in uscita a una rete virtuale.

   ![Aggiungere un'altra regola in uscita](./media/secure-web-app/nsg-outbound-vnet.png)

    *Aggiungere un'altra regola in uscita*

5. Nel pannello subnet del gruppo di sicurezza di rete selezionare **Associa**, selezionare la rete virtuale creata nella distribuzione e selezionare la subnet del gateway denominata **gw-subnet**. Il gruppo di sicurezza di rete viene applicato alla subnet.

6. Creare un altro gruppo di sicurezza di rete come nel passaggio precedente, questa volta per l'istanza del servizio app. Dagli un nome. Aggiungere la regola in ingresso per la porta 443 come per il gruppo di sicurezza di rete del gateway applicazione.

   Se si dispone di un'istanza del servizio app distribuita in un'istanza dell'ambiente del servizio app, che non è il caso di questa app, è possibile aggiungere regole in ingresso per consentire i probe di integrità del servizio di Azure aprendo le porte 454-455 nei gruppi di sicurezza in ingresso del gruppo di sicurezza del servizio app. Di seguito è riportata la configurazione:

   ![Aggiungere regole per i probe di Integrità dei servizi di AzureAdd rules for Azure Service Health probes](./media/secure-web-app/nsg-create-healthprobes.png)

    *Aggiungere regole per i probe di integrità del servizio di Azure (solo ambiente del servizio app)Add rules for Azure Service Health probes (App Service Environment only)*

7. Nelle regole di sicurezza in uscita creare una nuova regola di sicurezza in uscita che consenta all'istanza del servizio app di comunicare con il database PostgreSQL. Configurarlo in questo modo:

   ![Regola per consentire le connessioni PostgreSQL in uscita](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Aggiungere una regola per consentire le connessioni PostgreSQL in uscitaAdd a rule to allow outbound PostgreSQL connections*

Per limitare la superficie di attacco, modificare le impostazioni di rete del servizio app per consentire solo al gateway applicazione di accedere all'applicazione.
A tale scopo, passare alla scheda Rete del servizio app, selezionare la scheda **Restrizioni IP** e creare una regola di autorizzazione che consenta solo all'IP del gateway applicazione di accedere direttamente al servizio.

È possibile recuperare l'indirizzo IP del gateway dalla relativa pagina di panoramica. Nella scheda **CIDR Indirizzo IP** immettere l'indirizzo IP nel seguente formato: `<GATEWAY_IP_ADDRESS>/32`.

![Consenti solo il gateway](./media/secure-web-app/app-allow-gw-only.png)

*Consentire solo all'IP del gateway di accedere al servizio appAllow only the gateway IP to access the App Service*

#### <a name="implement-azure-active-directory-oauth"></a>Implementare OAuth di Azure Active Directory

I documenti di Azure distribuiti nella pagina dell'app Web di esempio sono risorse dell'app che potrebbero richiedere protezione. È possibile usare Azure Active Directory (Azure AD) per implementare l'autenticazione per le app Web, desktop e per dispositivi mobili usando flussi di autenticazione diversi.
L'app usa **Login With Microsoft**, che consente all'app di leggere i profili degli utenti aggiunti all'elenco degli utenti di Azure AD single-tenant.

Nel portale di Azure configurare l'app per usare le credenziali necessarie:In the Azure portal, configure the app to use the required credentials:

1. Selezionare **Azure Active Directory**o cercarlo utilizzando la casella di ricerca.

2. Selezionare **Nuova registrazione**:

   ![Creare una registrazione](./media/secure-web-app/ad-auth-create.png)

   *Creare una registrazione dell'app Azure ADCreate an Azure AD app registration*

3. Nella pagina successiva immettere il nome dell'app. In **Tipi di account supportati**selezionare Account solo nella directory **dell'organizzazione.**
    In URI di **reindirizzamento**immettere il dominio di base in cui verrà eseguita l'app più uno con l'endpoint del token. Ad esempio: *GATEWAY_HASH*.cloudapp.net/token.

   ![Configurare la registrazione dell'app Azure ADConfigure Azure AD app registration](./media/secure-web-app/ad-auth-type.png)

   *Configurare la registrazione dell'app Azure ADConfigure Azure AD app registration*

4. Viene visualizzata una schermata che mostra l'app registrata e le relative informazioni. È necessario aggiungere queste informazioni nell'istanza dell'insieme di credenziali delle chiavi di Azure.You need to add this information into the Azure Key Vault instance.
   1. Copiare l'ID dell'applicazione (client) `CLIENTID`e salvarlo in Key Vault come .
   2. Copiare l'URI di reindirizzamento immesso `REDIRECTURI`nel passaggio precedente e salvarlo come .
   3. Copiare il nome della directory predefinita di Azure AD, con il `TENANT` *nome*di formato microsoftonline.com, e salvarlo nell'insieme di credenziali delle chiavi come .
   4. Passare alla scheda **Certificati & segreti** dell'app azure AD creata in precedenza e selezionare Nuovo **segreto client**, come illustrato nella schermata seguente. Impostare una data di scadenza, quindi copiare il `CLIENTSECRET`valore generato e salvarlo in Key Vault come .

      ![Segreto di autorizzazione di Azure ADAzure AD authorization secret](./media/secure-web-app/ad-auth-secrets.png)

      *Segreto di autorizzazione di Azure ADAzure AD authorization secret*

   5. Generare una chiave segreta casuale sicura utilizzando qualsiasi riga di comando/strumento online. Salvarlo nell'insieme `FLASKSECRETKEY`di credenziali delle chiavi come . Il framework dell'applicazione utilizza questa chiave per creare sessioni.
        Per informazioni su come generare una chiave segreta, consultate [Sessioni Flask](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Dopo aver configurato l'accesso, è necessario aggiungere utenti al collegamento Azure AD per consentire loro di accedere alla risorsa. Per aggiungerli, passare alla scheda **Utenti** in Azure AD, selezionare **Tutti gli utenti**e quindi Nuovo utente o Nuovo **utente** **guest**. Per il test, è possibile aggiungere un utente guest e invitare l'utente nella directory. In alternativa, puoi aggiungere un nuovo utente se il dominio su cui è in esecuzione l'app è stato convalidato. In questo esempio, solo gli utenti registrati nel tenant di Azure AD possono essere registrati per l'accesso. Per informazioni sull'accesso multi-tenant, vedere la documentazione.

   ![Aggiungere utenti al dominio predefinito](./media/secure-web-app/ad-auth-add-user.png)

   *Aggiungere utenti al dominio predefinito di Azure Active DirectoryAdd users to the default Azure Active Directory domain*

Dopo aver aggiunto la configurazione e i segreti di Azure AD all'insieme di credenziali delle chiavi, gli utenti possono essere autenticati nell'app usando l'autenticazione OAuth di Azure.After you add the Azure AD configuration and secrets to Key Vault, users can be authenticated into the app by using Azure OAuth authentication.
Nel codice dell'app questa operazione viene gestita dalla libreria di autenticazione di Azure Active Directory (ADAL).

Dopo che i segreti si trovano nell'insieme di credenziali delle chiavi e l'applicazione ha accessohttps://GATEWAY_HASH.cloudapp.net)ai segreti e al database, il servizio dell'applicazione può essere raggiunto tramite l'URL dell'applicazione del gateway ( , che è possibile ottenere dal relativo blade.

Se, quando si accede ad Azure AD, viene visualizzato il messaggio di errore "L'utente non è registrato nella directory a cui si sta tentando di accedere", è necessario aggiungere l'utente. Per aggiungere l'utente, passare alla scheda **Utenti** di Azure AD e aggiungere manualmente l'utente immettendo i dettagli o invitare l'utente immettendo l'indirizzo di posta elettronica come utente guest in Azure AD nel pannello **Invita guest.**

#### <a name="deploy-application-insights"></a>Distribuire Application Insights
Ora che l'app è distribuita e funzionante, è necessario gestire gli errori che si verificano all'interno dell'app insieme alla registrazione e alla raccolta dei dati di traccia.
La registrazione e la raccolta dei dati di traccia fornisce una visualizzazione degli eventi di controllo che si verificano nell'app.

Application Insights è un servizio che raccoglie log che possono essere generati dagli utenti o dal sistema.

Per creare un'istanza di Application Insights:To create an Application Insights instance:

1. Cercare **Application Insights** usando la casella di ricerca nel portale di Azure.Search for Application Insights by using the search box in the Azure portal.
2. Selezionare **Application Insights**. Fornire i dettagli riportati di seguito per creare un'istanza.

   ![Creare un'istanza di Application InsightsCreate an Application Insights instance](./media/secure-web-app/app-insights-data.png)

Al termine della distribuzione, si dispone di un'istanza di Application Insights.After the deployment is complete, you have an Application Insights instance.

Dopo aver creato l'istanza di Applications Insights, è necessario che l'app sia consapevole della chiave di strumentazione che consente di inviare log al cloud. A tale scopo, recuperare la chiave di Application Insights e usarla all'interno delle librerie di applicazioni fornite da Azure per Application Insights.You do this by retrieving the Application Insights key and using it within the application libraries that Azure provides for Application Insights. La procedura consigliata consiste nell'archiviare chiavi e segreti nell'insieme di credenziali delle chiavi di Azure per mantenerli sicuri.

Per l'app di esempio di base, dopo aver creato l'istanza di Applications Insights, è necessario rendere l'app consapevole della chiave di strumentazione che consente di inviare log al cloud.
In Key Vault, `APPINSIGHTSKEY` impostare un segreto e impostarne il valore come chiave di strumentazione. Questa operazione consente all'app di inviare log e metriche ad Application Insights.Doing so allows the app to send logs and metrics to Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementare Multi-Factor Authentication per Azure Active Directory

Gli amministratori devono assicurarsi che gli account di sottoscrizione nel portale siano protetti. La sottoscrizione è vulnerabile agli attacchi perché gestisce le risorse create. Per proteggere la sottoscrizione, abilitare Multi-Factor Authentication nella scheda **Azure Active Directory** della sottoscrizione.

Azure AD funziona in base ai criteri applicati a uno o a gruppi di utenti che soddisfano determinati criteri.
Azure crea un criterio predefinito che specifica che gli amministratori devono utilizzare l'autenticazione a due fattori per accedere al portale.
Dopo aver abilitato questo criterio, potrebbe essere richiesto di disconnettersi e accedere di nuovo al portale di Azure.After enabling this policy, you might be prompted to sign out and sign back into the Azure portal.

Per abilitare l'autenticazione a più fattori per gli accessi amministrativi:To enable MFA for admin sign-ins:

1. Passare alla scheda Azure Active Directory nel portale di AzureGo to the **Azure Active Directory** tab in the Azure portal
2. Nella categoria di sicurezza selezionare accesso condizionale. Verrà visualizzata la schermata seguente:

   ![Accesso condizionale - Criteri](./media/secure-web-app/ad-mfa-conditional-add.png)

Se non riesci a creare un nuovo criterio:

1. Passare alla scheda **MFA.**
2. Selezionare il collegamento di versione di **valutazione gratuita** di Azure AD Premium per sottoscrivere la versione di valutazione gratuita.

   ![Versione di valutazione gratuita di Azure AD PremiumAzure AD Premium free trial](./media/secure-web-app/ad-trial-premium.png)

Tornare alla schermata di accesso condizionale.

1. Selezionare la nuova scheda dei criteri.
2. Immettere il nome del criterio.
3. Selezionare gli utenti o i gruppi per cui si desidera abilitare l'autenticazione a più fattori.
4. In **Controlli di accesso**selezionare la scheda **Concedi** e quindi selezionare **Richiedi autenticazione** a più fattori (e altre impostazioni, se lo si desidera).

   ![Richiedere l'autenticazione MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

È possibile abilitare il criterio selezionando la casella di controllo nella parte superiore dello schermo o eseguire questa operazione nella scheda **Accesso condizionale.** Quando il criterio è abilitato, gli utenti devono eseguire l'autenticazione a più fattori per accedere al portale.

Esiste un criterio di base che richiede l'autenticazione a più fattori per tutti gli amministratori di Azure.There's a baseline policy that requires MFA for all Azure administrators. È possibile abilitarlo immediatamente nel portale. L'abilitazione di questo criterio potrebbe invalidare la sessione corrente e forzare l'accesso.

Se il criterio di base non è abilitato:

1. Selezionare **Richiedi autenticazione a più fattori per gli amministratori**.
2. Selezionare **Usa criterio immediatamente**.

   ![Selezionare Usa criterio immediatamente](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usare Azure Sentinel per monitorare app e risorseUse Azure Sentinel to monitor apps and resources

Man mano che un'applicazione cresce, diventa difficile aggregare tutti i segnali di sicurezza e le metriche ricevute dalle risorse e renderli utili in modo orientato all'azione.

Azure Sentinel è progettato per raccogliere dati, rilevare i tipi di minacce possibili e fornire visibilità sugli incidenti di sicurezza.
Durante l'attesa dell'intervento manuale, Azure Sentinel può fare affidamento su playbook prescritti per avviare avvisi e processi di gestione degli incidenti.

L'app di esempio è composta da diverse risorse che Azure Sentinel può monitorare.
Per configurare Azure Sentinel, è innanzitutto necessario creare un'area di lavoro di Log Analytics in cui sono archiviati tutti i dati raccolti dalle varie risorse.

Per creare questa area di lavoro:

1. Nella casella di ricerca nel portale di Azure cercare **Log Analytics**. Selezionare **Aree di lavoro di Log Analytics**.

   ![Cercare aree di lavoro di Log AnalyticsSearch for Log Analytics workspaces](./media/secure-web-app/sentinel-log-analytics.png)

    *Cercare aree di lavoro di Log AnalyticsSearch for Log Analytics workspaces*

2. Nella pagina successiva selezionare **Aggiungi** e quindi specificare un nome, un gruppo di risorse e un percorso per l'area di lavoro.
   ![Creare un'area di lavoro Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Creare un'area di lavoro di Log AnalyticsCreate a Log Analytics workspace*

3. Usare la casella di ricerca per cercare **Azure Sentinel.**

   ![Cercare Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Cercare Azure SentinelSearch for Azure Sentinel*

4. Selezionare **Aggiungi** e quindi selezionare l'area di lavoro di Log Analytics creata in precedenza.

   ![Aggiungere un'area di lavoro di Log AnalyticsAdd a Log Analytics workspace](./media/secure-web-app/sentinel-workspace-add.png)

    *Aggiungere un'area di lavoro di Log AnalyticsAdd a Log Analytics workspace*

5. Nella pagina **Azure Sentinel - Connettori dati,** in **Configurazione**, selezionare **Connettori dati**. You see an array of Azure services that you can link to the Log Analytics storage instance for analysis in Azure Sentinel.

   ![Connettori dati di Log AnalyticsLog Analytics data connectors](./media/secure-web-app/sentinel-connectors.png)

    *Aggiungere un connettore dati ad Azure SentinelAdd a data connector to Azure Sentinel*

   Ad esempio, per connettere il gateway applicazione, eseguire la procedura seguente:For example, to connect the application gateway, take these steps:

   1. Aprire il pannello dell'istanza del gateway applicazione di Azure.Open the Azure Application Gateway instance blade.
   2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
   3. Selezionare **Aggiungi impostazione diagnostica**.

      ![Aggiungere la diagnostica del gateway applicazioneAdd Application Gateway diagnostics](./media/secure-web-app/sentinel-gateway-connector.png)

      *Aggiungere la diagnostica del gateway applicazioneAdd Application Gateway diagnostics*

   4. Nella pagina Impostazioni di diagnostica selezionare l'area di lavoro Log Analytics creata e quindi selezionare tutte le metriche che si desidera raccogliere e inviare ad Azure Sentinel.On the **Diagnostic settings** page, select the Log Analytics workspace that you created and then select all the metrics that you want to collect and send to Azure Sentinel. Selezionare **Salva**.

        ![Impostazioni del connettore di Azure SentinelAzure Sentinel connector settings](./media/secure-web-app/sentinel-connector-settings.png)

        *Impostazioni del connettore di Azure SentinelAzure Sentinel connector settings*

  Le metriche della risorsa si trovano in Azure Sentinel, in cui è possibile eseguire query e analizzarle.

   Aggiungere le stesse metriche nelle impostazioni di diagnostica per L'insieme di credenziali delle chiavi di Azure, l'indirizzo IP pubblico, il database di Azure per PostgreSQL e tutti i servizi che supportano i log di diagnostica nell'account.

Dopo aver configurato le metriche, Azure Sentinel contiene dati da analizzare.

## <a name="evaluate-and-verify"></a>Valutare e verificare

Dopo aver sviluppato e distribuito l'architettura, è necessario assicurarsi che il codice e i servizi distribuiti soddisfino gli standard di sicurezza. Questi sono alcuni passaggi che è possibile eseguire per verificare il software:

- Analisi del codice statico
- Analisi delle vulnerabilità
- Individuazione e correzione delle vulnerabilità nelle dipendenze delle applicazioni

Questi sono gli elementi fondamentali per le procedure consigliate nello sviluppo sicuro.

### <a name="static-code-analysis"></a>Analisi del codice statico

Per l'app di esempio, la verifica con strumenti di analisi statica implica la ricerca di vulnerabilità nel codice dell'app usando tecniche come il controllo taint e l'analisi del flusso di dati. Gli strumenti di analisi statica Python offrono maggiore sicurezza che l'app sia sicura.

**Rilevamento di errori con Lint**

PyFlakes, una libreria di linting Python, ti aiuta a rimuovere il codice morto e le funzioni inutilizzate dalle app, come mostrato qui:

![Fiocchi di Paffi](./media/secure-web-app/pyflakes.png)

Linting fornisce suggerimenti e possibili modifiche che possono rendere il codice più pulito e meno soggetto a errori durante il runtime.

**PyLint**

PyLint ha fornito il massimo valore per questo progetto. Esegue controlli standard del codice, il controllo degli errori e i suggerimenti per il refactoring per garantire che il codice in esecuzione sul server sia sicuro. Utilizzando PyLint per aggiornare il codice, è possibile eliminare i bug e migliorare la classificazione PyLint, come illustrato nelle immagini seguenti.

![Prima di PyLint](./media/secure-web-app/before-pylint.png)

*Prima di PyLint*

Dopo aver risolto alcuni degli errori di codice rilevati dagli strumenti di linting, si ha maggiore fiducia che il codice non è soggetto a errori. La correzione degli errori riduce in modo significativo i rischi per la sicurezza che possono verificarsi quando il codice viene distribuito negli ambienti di produzione.

![Dopo Pylint](./media/secure-web-app/after-pylint.png)

*Dopo PyLint*

### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità

Lo strumento [AP](https://www.zaproxy.org/) è uno scanner di vulnerabilità delle applicazioni Web open source che è possibile utilizzare per verificare la presenza di vulnerabilità nell'app di esempio. L'esecuzione dello strumento sull'app di esempio rivela alcuni possibili errori e vettori di attacco.

![Strumento AP](./media/secure-web-app/zap-tool.png)

*Strumento AP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Individuare e correggere le vulnerabilità nelle dipendenze delle appFind and fix vulnerabilities in app dependencies

Per trovare e correggere le dipendenze dell'applicazione, è possibile utilizzare controllo delle dipendenze di [OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

La sicurezza è un'applicazione simile che controlla le dipendenze. Lo si può trovare su [GitHub](https://github.com/pyupio/safety). Analisi di sicurezza per le vulnerabilità rilevate in database di vulnerabilità ben noti.

![Sicurezza](./media/secure-web-app/pysafety.png)

*Sicurezza*

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti consentono di progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppare](secure-develop.md)
- [Distribuire](secure-deploy.md)
