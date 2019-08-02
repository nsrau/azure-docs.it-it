---
title: Sviluppare un'applicazione Web sicura | Microsoft Docs
description: Questa semplice app di esempio implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e il comportamento di sicurezza dell'organizzazione quando si sviluppa in Azure.
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 89d4dc789da7fc719b61342bbf0683e99c45a72c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698255"
---
# <a name="develop-a-secure-web-app"></a>Sviluppare un'app Web sicura

Questo esempio è una semplice app Python che visualizza una pagina Web contenente i collegamenti alle risorse di sicurezza per lo sviluppo di app in Azure. L'app implementa le procedure consigliate per la sicurezza che consentono di migliorare l'applicazione e il comportamento di sicurezza dell'organizzazione quando si sviluppano app in Azure.

Attenersi alla procedura descritta in questo articolo in sequenza per assicurarsi che i componenti dell'applicazione siano configurati correttamente. Il database, il servizio app Azure, l'istanza Azure Key Vault e applicazione Azure istanza del gateway dipendono l'uno dall'altro.

Gli script di distribuzione configurano l'infrastruttura. Dopo aver eseguito gli script di distribuzione, è necessario eseguire alcune operazioni di configurazione manuale nel portale di Azure per collegare insieme i componenti e i servizi.

L'app di esempio è destinata ai principianti che sviluppano applicazioni in Azure che vogliono implementare misure di sicurezza nelle proprie applicazioni.

Durante lo sviluppo e la distribuzione di questa app, si apprenderà come:

- Creare un'istanza di Azure Key Vault, archiviare e recuperare i relativi segreti.
- Distribuire database di Azure per PostgreSQL, configurare password sicure e autorizzare l'accesso ad esso.
- Eseguire un contenitore alpino Linux in app Web di Azure per Linux e abilitare le identità gestite per le risorse di Azure.
- Creare e configurare un'istanza di applicazione Azure gateway con un firewall che usa [OWASP primi 10 RuleSet](https://coreruleset.org/).
- Abilitare la crittografia dei dati in transito e a riposo usando i servizi di Azure.

Dopo aver sviluppato e distribuito l'app, è necessario configurare l'app Web di esempio seguente insieme alle misure di configurazione e sicurezza descritte.

![App Web di esempio](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architettura
L'app è una tipica applicazione a più livelli con tre livelli. Il front-end, il back-end e il livello di database con componenti di monitoraggio e di gestione segreta integrati sono illustrati di seguito:

![Architettura dell'app](./media/secure-web-app/architecture.png)

L'architettura è costituita da questi componenti:

- [Gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/). Fornisce il gateway e il firewall per l'architettura dell'applicazione.
- [App Web di Azure in Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro). Fornisce il runtime del contenitore per eseguire l'app Python in un ambiente Linux.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Archivia e crittografa i segreti dell'app e gestisce la creazione di criteri di accesso.
- [Database di Azure per PostgreSQL](https://azure.microsoft.com/services/postgresql/). Archivia in modo sicuro i dati dell'app.
- [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/) e [informazioni dettagliate applicazione Azure](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fornisce il monitoraggio e gli avvisi per il funzionamento dell'app.

## <a name="threat-model"></a>Modello di minaccia
La modellazione delle minacce è il processo di identificazione delle potenziali minacce per la sicurezza per l'azienda e l'applicazione e quindi per assicurarsi che sia presente un piano di mitigazione appropriato.

Questo esempio ha usato le [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) per implementare la modellazione delle minacce per l'app di esempio protetta. Grazie alla creazione di diagrammi dei componenti e dei flussi di dati, è possibile identificare i problemi e le minacce nelle fasi iniziali del processo di sviluppo. Questo consente di risparmiare tempo e denaro in seguito.

Questo è il modello di rischio per l'app di esempio:

![Modello di minaccia](./media/secure-web-app/threat-model.png)

Nella schermata seguente vengono illustrate alcune minacce di esempio e potenziali vulnerabilità generate dallo strumento di modellazione delle minacce. Il modello di minaccia fornisce una panoramica della superficie di attacco esposta e chiede agli sviluppatori di considerare come mitigare i problemi.

![Output del modello di minaccia](./media/secure-web-app/threat-model-output.png)

Ad esempio, l'attacco SQL injection nell'output del modello di minaccia precedente è mitigato dalla purificazione degli input utente e dall'uso delle funzioni archiviate nel database di Azure per PostgreSQL. Questa attenuazione impedisce l'esecuzione arbitraria delle query durante le operazioni di lettura e scrittura dei dati.

Gli sviluppatori migliorano la sicurezza complessiva del sistema mitigando ciascuna minaccia nell'output del modello di minaccia.

## <a name="deployment"></a>Distribuzione
Le opzioni seguenti consentono di eseguire Linux in app Azure servizio:

- Scegliere un contenitore dall'elenco dei contenitori Microsoft predefiniti in Azure che sono stati creati con le tecnologie di supporto (Python, Ruby, PHP, Java, node. js, .NET Core).
- Usare un contenitore personalizzato. Selezionare i registri dei contenitori come origine dell'immagine e basarsi sulle numerose tecnologie disponibili che supportano HTTP.

In questo esempio verrà eseguito lo script di distribuzione che distribuirà il webapp nel servizio app e creerà le risorse.

L'app può usare i diversi modelli di distribuzione illustrati di seguito:

![Diagramma del flusso di dati della distribuzione](./media/secure-web-app/deployment.png)

Sono disponibili diversi modi per distribuire le app in Azure, tra cui:

- Modelli di Gestione risorse di Azure
- PowerShell
- Interfaccia della riga di comando di Azure
- Portale di Azure
- Azure DevOps

Questa applicazione USA:

- [Docker](https://docs.docker.com/) per creare e compilare le immagini del contenitore.
- [Interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) della riga di comando di Azure per la distribuzione.
- [Hub Docker](https://hub.docker.com/) come registro contenitori.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

### <a name="network"></a>Rete
L'app di esempio usa la crittografia SSL end-to-end per il flusso di dati in transito all'interno e all'esterno della rete. Il gateway è configurato con un certificato autofirmato.
> [!IMPORTANT]
> In questa dimostrazione viene usato un certificato autofirmato. In un ambiente di produzione è necessario ottenere i certificati da un'autorità di certificazione (CA) verificata.

Il firewall dell'applicazione controlla anche il traffico in ingresso e gli avvisi gli amministratori quando viene rilevato traffico dannoso nel traffico di rete.
Il gateway applicazione mitiga la possibilità per le minacce DDoS e SQL injection individuate nel modello di minaccia.

### <a name="identity"></a>identità
Per accedere al portale, l'app di esempio usa l'autenticazione a più fattori per gli amministratori Azure Active Directory (Azure AD) a cui viene assegnato l'accesso alle risorse.
L'app di esempio Usa identità gestite per ottenere le autorizzazioni per la lettura e il recupero dei segreti da Azure Key Vault, garantendo che l'app non debba necessariamente codificare le credenziali e i token per leggere i segreti. Azure AD crea automaticamente le entità servizio necessarie all'app per leggere e modificare i segreti quando vengono usate le identità gestite.

Le identità gestite per le risorse di Azure e l'autenticazione a più fattori rendono più difficile per gli avversari ottenere privilegi ed escalare i propri privilegi nel sistema. Questa minaccia è stata indicata nel modello di minaccia.
L'app usa OAuth, che consente agli utenti registrati nell'applicazione OAuth di accedere all'app.

### <a name="storage"></a>Archiviazione
I dati nel database PostgreSQL vengono crittografati automaticamente a riposo da database di Azure per PostgreSQL. Il database autorizza gli indirizzi IP del servizio app in modo che solo l'app Web del servizio app distribuita possa accedere alle risorse di database con le credenziali di autenticazione corrette.

### <a name="logging-and-auditing"></a>Registrazione e controllo
L'app implementa la registrazione usando Application Insights per tenere traccia delle metriche, dei registri e delle eccezioni che si verificano. Questa registrazione fornisce un numero sufficiente di metadati dell'app per informare gli sviluppatori e i membri del team operativo sullo stato dell'app. Fornisce inoltre dati sufficienti per eseguire il backtracking in caso di eventi imprevisti della sicurezza.

## <a name="cost-considerations"></a>Considerazioni sul costo
Se non si ha già un account Azure, è possibile crearne uno gratuito. Vai alla [pagina account gratuito](https://azure.microsoft.com/free/) per iniziare, Scopri cosa puoi fare con un account Azure gratuito e Scopri quali prodotti sono gratuiti per 12 mesi.

Per distribuire le risorse nell'app di esempio con le funzionalità di sicurezza, è necessario pagare per alcune funzionalità Premium. Quando l'app viene ridimensionata e i livelli gratuiti e le versioni di valutazione offerte da Azure devono essere aggiornati per soddisfare i requisiti dell'applicazione, i costi potrebbero aumentare. Usa il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) di Azure per stimare i costi.

## <a name="deploy-the-solution"></a>Distribuire la soluzione
### <a name="prerequisites"></a>Prerequisiti
Per fare in modo che l'applicazione sia in esecuzione, è necessario installare gli strumenti seguenti:

- Un editor di codice per modificare e visualizzare il codice dell'applicazione. [Visual Studio Code](https://code.visualstudio.com/) è un'opzione open source.
- [Interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) della riga di comando di Azure nel computer di sviluppo.
- [Git](https://git-scm.com/) nel sistema. Git viene usato per clonare il codice sorgente localmente.
- [JQ](https://stedolan.github.io/jq/), uno strumento UNIX per l'esecuzione di query su JSON in modo semplice e intuitivo.

Per distribuire le risorse dell'app di esempio, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/) per testare l'app di esempio.

Dopo l'installazione di questi strumenti, si è pronti per distribuire l'app in Azure.

### <a name="environment-setup"></a>Configurazione dell'ambiente
Eseguire gli script di distribuzione per configurare l'ambiente e la sottoscrizione:

1. Per clonare il repository del codice sorgente, usare questo comando git:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Per spostarsi nella directory, usare il comando seguente:

   ```
   cd tutorial-project/scripts
   ```

3. Nella cartella scripts sono presenti file specifici della piattaforma in uso (Windows o Linux). Poiché l'interfaccia della riga di comando di Azure è già stata installata, accedere all'account Azure dal prompt dei comandi eseguendo questo comando CLI:

   ``` azurecli
   az login
   ```

Si aprirà il browser e si accederà con le proprie credenziali. Dopo aver eseguito l'accesso, è possibile iniziare a distribuire le risorse dal prompt dei comandi.

Gli script `deploy-powershell.ps1` di distribuzione `deploy-bash.sh` e contengono codice che distribuisce l'intera applicazione.
Per distribuire la soluzione:

1. Se si è in PowerShell, eseguire `deploy-powershell.ps1` il file digitando `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` sostituendo l'area e il nome del gruppo di risorse con le aree di Azure appropriate e un nome per il gruppo di risorse
2. Se si è in Linux eseguire il `deploy-bash.sh` file digitando `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`, potrebbe essere necessario creare il file eseguibile digitando`chmod +x deploy-bash.sh`

Gli esempi seguenti illustrano i frammenti dei componenti principali. È possibile distribuire gli esempi singolarmente o con il resto dei componenti eseguendo i file di distribuzione.

### <a name="implementation-guidance"></a>Indicazioni relative all'implementazione
Lo script di distribuzione è uno script che può essere suddiviso in quattro fasi. Ogni fase distribuisce e configura una risorsa di Azure che si trova nel [diagramma dell'architettura](#architecture).

Le quattro fasi sono:

- Distribuire Azure Key Vault.
- Distribuire database di Azure per PostgreSQL.
- Distribuire app Web di Azure in Linux.
- Distribuire il gateway applicazione con web application firewall.

Ogni fase si basa su quella precedente usando la configurazione dalle risorse distribuite in precedenza.

Per completare i passaggi di implementazione, verificare di aver installato gli strumenti elencati in [prerequisiti](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Distribuisci Azure Key Vault
In questa sezione viene creata e distribuita un'istanza di Azure Key Vault utilizzata per archiviare segreti e certificati.

Dopo aver completato la distribuzione, è presente un'istanza di Azure Key Vault distribuita in Azure.

Per distribuire Azure Key Vault usando l'interfaccia della riga di comando di Azure:

1. Dichiarare le variabili per Azure Key Vault.
2. Registrare il provider di Azure Key Vault.
3. Creare il gruppo di risorse per l'istanza.
4. Creare l'istanza Azure Key Vault nel gruppo di risorse creato nel passaggio 3.

   ``` azurecli

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
È consigliabile usare identità gestite per le risorse di Azure nelle app che usano Key Vault per accedere alle risorse. Il comportamento di sicurezza aumenta quando le chiavi di accesso Key Vault non vengono archiviate nel codice o nella configurazione.

#### <a name="deploy-azure-database-for-postgresql"></a>Distribuire database di Azure per PostgreSQL
Il database di Azure per PostgreSQL funziona nel modo seguente, innanzitutto creare il server di database e quindi creare il database in cui archiviare lo schema e i dati.

Al termine della distribuzione, si dispone di un server PostgreSQL e di un database in esecuzione in Azure.

Per distribuire database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure:

1. Aprire un terminale con l'interfaccia della riga di comando di Azure e la configurazione della sottoscrizione Azure.
2. Generare una combinazione di nome utente e password sicura utilizzata per accedere al database. Questi elementi devono essere archiviati in Azure Key Vault per le app che li usano.
3. Creare l'istanza del server PostgreSQL.
4. Creare un database nell'istanza del server creata nel passaggio 3.
5. Eseguire gli script PostgreSQL nell'istanza di PostgreSQL.

Il codice riportato di seguito si basa sui segreti PGUSERNAME e PGPASSWORD archiviati nell'insieme di credenziali delle chiavi di Azure dal passaggio di distribuzione dell'insieme di credenziali delle chiavi riportato sopra.

   ``` azurecli
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

Dopo aver distribuito il database, è necessario archiviarne le credenziali e la stringa di connessione in Azure Key Vault.
Nella cartella Scripts è presente un `functions.sql` file che contiene il codice PL/pgSQL che consente di creare funzioni archiviate durante l'esecuzione. L'esecuzione di questo file parametrizza gli input per limitare SQL injection.

PostgreSQL è integrato con uno strumento denominato `psql` usato per connettersi al database. Per eseguire `functions.sql`, è necessario connettersi all'istanza del database di Azure per PostgreSQL dal computer locale ed eseguirla da questa posizione. L'installazione dello strumento PSQL è inclusa nell'installazione predefinita per PostgreSQL in ogni sistema operativo.
Per ulteriori informazioni, vedere la [documentazione di PSQL](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure cloud Shell include anche lo `psql` strumento. È possibile utilizzare Cloud Shell direttamente dall'portale di Azure selezionando l'icona Cloud Shell.

Per abilitare l'accesso remoto all'istanza di PostgreSQL, è necessario autorizzare l'indirizzo IP in PostgreSQL.
Per abilitare l'accesso, passare alla scheda **sicurezza connessione** , selezionare **Aggiungi IP client**e salvare le nuove impostazioni.

![Autorizza IP client](./media/secure-web-app/add-client-ip-postgres.png)

Se si usa Cloud Shell invece dello strumento PSQL locale, selezionare **Consenti l'accesso ai servizi di Azure** e modificare il relativo valore **su on** per consentire l'accesso al cloud Shell.

Connettersi quindi all'istanza eseguendo il comando psql seguente con i parametri della stringa di connessione dalla scheda **stringhe di connessione** dell'istanza di PostgreSQL nella portale di Azure.
Sostituire le parentesi graffe vuote con i parametri del pannello della stringa di connessione del database e la password con la password da Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Eseguire lo script PL/pgSQL seguente dopo avere verificato di essere connessi al database. Lo script crea le funzioni archiviate utilizzate per inserire i dati nel database.

```sql
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


Per altre informazioni su come configurare la verifica di SSL e dell'autorità di certificazione (CA) per PostgreSQL, vedere [configurare la connettività SSL nel database di Azure per PostgreSQL](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security).

Un certificato radice è incluso nel contenitore. I passaggi necessari per ottenere il certificato sono:

1. Scaricare il file del certificato dall' [autorità di certificazione](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Scaricare e installare OpenSSL nel computer](https://docs.microsoft.com/en-us/azure/postgresql/concepts-ssl-connection-security).
3. Decodificare il file di certificato:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Per altre informazioni su come configurare la sicurezza SSL per PostgreSQL, vedere [configurare la sicurezza della connessione SSL](https://docs.microsoft.com/en-gb/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Distribuire app Web di Azure in Linux
Puoi creare con facilità servizi Linux in app Azure servizio come Azure fornisce un set di contenitori e immagini predefiniti per linguaggi ampiamente usati come Python, Ruby, C#e Java. Azure supporta anche i contenitori personalizzati, che possono consentire l'esecuzione praticamente di tutti i linguaggi di programmazione sulla piattaforma del servizio app Azure.

L'app distribuita è una semplice app Python che viene eseguita nella distribuzione Ubuntu Linux più recente. Si connette alle istanze di Azure Key Vault e PostgreSQL create nelle sezioni precedenti per la gestione delle credenziali e l'archiviazione dei dati, rispettivamente.

Il file Docker seguente viene fornito nella cartella radice dell'app:

``` docker
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

Il Dockerfile sopra riportato viene usato per compilare il contenitore ospitato nel Container Registry di Azure in `mcr.microsoft.com/samples/basic-linux-app`.

Il codice seguente:

1. Dichiara le variabili e i nomi per l'istanza del servizio app.
2. Crea il gruppo di risorse per il piano di servizio app.
3. Effettua il provisioning di un'istanza di app Web di Azure in contenitori Linux.
4. Abilita la registrazione per il contenitore dell'app Web.
5. Imposta alcune configurazioni dell'app nelle impostazioni dell'app del contenitore.

   ```
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

Questo script crea un'identità assegnata per l'istanza del servizio app che può essere usata con MSI per interagire con Azure Key Vault senza segreti di codifica rigidi nel codice o nella configurazione.

Passare all'istanza di Azure Key Vault nel portale per autorizzare l'identità assegnata nella scheda criteri di accesso. Selezionare **Aggiungi nuovi criteri di accesso**. In **Seleziona entità**cercare il nome dell'applicazione simile al nome dell'istanza del servizio app creata.
Un'entità servizio associata all'applicazione deve essere visibile. Selezionare la pagina e salvare i criteri di accesso, come illustrato nello screenshot seguente.

Poiché l'applicazione deve solo recuperare le chiavi, selezionare l'autorizzazione **Get** nelle opzioni Secrets, consentendo l'accesso, riducendo al contempo i privilegi concessi.

![Criteri di accesso insieme di credenziali delle chiavi](./media/secure-web-app/kv-access-policy.png)

*Creare un criterio di accesso Key Vault*

Salvare i criteri di accesso, quindi salvare la nuova modifica nella scheda **criteri di accesso** per aggiornare i criteri.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Distribuire il gateway applicazione con web application firewall abilitato
Nelle app Web non è consigliabile esporre i servizi direttamente al mondo esterno su Internet.
Il bilanciamento del carico e le regole del firewall forniscono maggiore sicurezza e controllo sul traffico in ingresso e consentono di gestirlo.

Per distribuire un'istanza del gateway applicazione:

1. Creare il gruppo di risorse per ospitare il gateway applicazione.
2. Eseguire il provisioning di una rete virtuale per connettersi al gateway.
3. Creare una subnet per il gateway nella rete virtuale.
4. Effettuare il provisioning di un indirizzo IP pubblico.
5. Eseguire il provisioning del gateway applicazione.
6. Abilitare web application firewall sul gateway.

   ``` azurecli
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

1. Consente di creare un nuovo certificato autofirmato in Azure.
2. Scarica il certificato autofirmato come file con codifica Base64.
3. Genera una password per il certificato autofirmato.
4. Esporta il certificato come file PFX firmato con la password.
5. Archivia la password del certificato in Azure Key Vault.

Questa sezione distribuisce il gateway applicazione:

```powershell
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

Dopo aver completato la distribuzione, è disponibile un gateway applicazione con web application firewall abilitata.

L'istanza del gateway espone la porta 443 per HTTPS. Questa configurazione garantisce che l'app sia accessibile solo sulla porta 443 tramite HTTPS.

Bloccare le porte inutilizzate e limitare l'esposizione alla superficie di attacco è una procedura di sicurezza consigliata.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Aggiungere gruppi di sicurezza di rete all'istanza del servizio app

Le istanze del servizio app possono essere integrate con le reti virtuali. Questa integrazione consente di configurare i criteri del gruppo di sicurezza di rete che gestiscono il traffico in ingresso e in uscita dell'app.

1. Per abilitare questa funzionalità, nel pannello dell'istanza del servizio app Azure, in **Impostazioni**, selezionare **rete**. Nel riquadro di destra, in **integrazione VNet**, selezionare **fare clic qui per configurare**.

   ![Nuova integrazione della rete virtuale](./media/secure-web-app/app-vnet-menu.png)

    *Nuova integrazione della rete virtuale per il servizio app*
1. Nella pagina successiva selezionare **Aggiungi VNET (anteprima)** .

1. Nel menu successivo selezionare la rete virtuale creata nella distribuzione che inizia con `hello-vnet`. È possibile creare una nuova subnet o selezionarne una esistente.
   In tal caso, creare una nuova subnet. Impostare l' **intervallo di indirizzi** su **10.0.3.0/24** e assegnare un nome alla subnet **app-subnet**.

   ![Configurazione della rete virtuale del servizio app](./media/secure-web-app/app-vnet-config.png)

    *Configurazione della rete virtuale per il servizio app*

Ora che è stata abilitata l'integrazione della rete virtuale, è possibile aggiungere gruppi di sicurezza di rete all'app.

1. Utilizzare la casella di ricerca per cercare **gruppi di sicurezza di rete**. Nei risultati selezionare **gruppi di sicurezza di rete** .

    ![Cercare gruppi di sicurezza di rete](./media/secure-web-app/nsg-search-menu.png)

    *Cercare gruppi di sicurezza di rete*

2. Nel menu successivo selezionare **Aggiungi**. Immettere il **nome** del NSG e il **gruppo di risorse** in cui deve essere individuato. Questo NSG verrà applicato alla subnet del gateway applicazione.

    ![Creare un NSG](./media/secure-web-app/nsg-create-new.png)

    *Creare un NSG*

3. Dopo aver creato il NSG, selezionarlo. Nel pannello in **Impostazioni**selezionare **regole di sicurezza in ingresso**. Configurare queste impostazioni per consentire le connessioni in ingresso nel gateway applicazione sulla porta 443.

   ![Configurare NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Configurare NSG*

4. Nelle regole in uscita per il gateway NSG aggiungere una regola che consenta le connessioni in uscita all'istanza del servizio app creando una regola destinata al tag `AppService`del servizio:

   ![Aggiungere regole in uscita per NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Aggiungere regole in uscita per NSG*

    Aggiungere un'altra regola in uscita per consentire al gateway di inviare regole in uscita a una rete virtuale.

   ![Aggiungere un'altra regola in uscita](./media/secure-web-app/nsg-outbound-vnet.png)

    *Aggiungere un'altra regola in uscita*

5. Nel pannello subnet del NSG selezionare **associa**, selezionare la rete virtuale creata nella distribuzione e selezionare la subnet del gateway denominata **GW-Subnet**. NSG viene applicato alla subnet.

6. Creare un'altra NSG come nel passaggio precedente, questa volta per l'istanza del servizio app. Assegnargli un nome. Aggiungere la regola in ingresso per la porta 443 come per il gateway applicazione NSG.

   Se è stata distribuita un'istanza del servizio app in un'istanza di ambiente del servizio app, che non è il caso di questa app, è possibile aggiungere regole in ingresso per consentire i probe di integrità dei servizi di Azure aprendo le porte 454-455 nei gruppi di sicurezza in ingresso del servizio app NSG. Ecco la configurazione:

   ![Aggiungere regole per i probe di integrità dei servizi di Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Aggiungere regole per i probe di integrità dei servizi di Azure (solo ambiente del servizio app)*

7. Nelle regole di sicurezza in uscita creare una nuova regola di sicurezza in uscita che consenta all'istanza del servizio app di comunicare con il database PostgreSQL. Configurarlo come segue:

   ![Regola per consentire le connessioni PostgreSQL in uscita](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Aggiungere una regola per consentire le connessioni PostgreSQL in uscita*

Per limitare la superficie di attacco, modificare le impostazioni di rete del servizio app per consentire solo al gateway applicazione di accedere all'applicazione.
A tale scopo, passare alla scheda rete del servizio app, selezionare la scheda **restrizioni IP** e creare una regola di autorizzazione che consenta solo all'indirizzo IP del gateway applicazione di accedere direttamente al servizio.

È possibile recuperare l'indirizzo IP del gateway dalla relativa pagina di panoramica. Nella scheda **CIDR indirizzo IP** immettere l'indirizzo IP nel formato seguente: `<GATEWAY_IP_ADDRESS>/32`.

![Consenti solo il gateway](./media/secure-web-app/app-allow-gw-only.png)

*Consenti solo all'indirizzo IP del gateway di accedere al servizio app*


#### <a name="implement-azure-active-directory-oauth"></a>Implementare Azure Active Directory OAuth

I documenti di Azure distribuiti nella pagina dell'app Web di esempio sono risorse nell'app che potrebbero richiedere la protezione. È possibile usare Azure Active Directory (Azure AD) per implementare l'autenticazione per app Web, desktop e per dispositivi mobili usando flussi di autenticazione diversi.
L'app usa **login con Microsoft**, che consente all'app di leggere i profili degli utenti che sono stati aggiunti all'elenco di Azure ad utente a tenant singolo.

Nella portale di Azure configurare l'app per l'uso delle credenziali necessarie:

1. Selezionare **Azure Active Directory**o cercarla usando la casella di ricerca.

2. Selezionare **nuova registrazione**:

   ![Creare una registrazione](./media/secure-web-app/ad-auth-create.png)

   *Creare una registrazione dell'app Azure AD*

3. Nella pagina successiva immettere il nome dell'app. In **tipi di account supportati**selezionare **account solo in questa directory aziendale**.
    In **URI di reindirizzamento**immettere il dominio di base in cui verrà eseguita l'app, più uno con l'endpoint del token. Ad esempio:  *GATEWAY_HASH*. cloudapp.NET/token.

   ![Configurare la registrazione dell'app Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Configurare la registrazione dell'app Azure AD*

4. Viene visualizzata una schermata che mostra l'app registrata e le relative informazioni. È necessario aggiungere queste informazioni nell'istanza di Azure Key Vault.
   1. Copiare l'ID dell'applicazione (client) e salvarlo in Key Vault `CLIENTID`come.
   2. Copiare l'URI di reindirizzamento immesso nel passaggio precedente e salvarlo come `REDIRECTURI`.
   3. Copiare il Azure AD nome di directory predefinito, che ha il formato *nome*. microsoftonline.com, e salvarlo in Key Vault `TENANT`come.
   4. Passare alla scheda **certificati & segreti** dell'app Azure ad creata in precedenza e selezionare **nuovo segreto client**, come illustrato nello screenshot seguente. Impostare una data di scadenza, quindi copiare il valore generato e salvarlo in Key Vault come `CLIENTSECRET`.

      ![Segreto di autorizzazione Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Segreto di autorizzazione Azure AD*

   5. Generare una chiave privata casuale sicura usando uno strumento da riga di comando/strumento online. Salvarlo in Key Vault come `FLASKSECRETKEY`. Il Framework applicazione usa questa chiave per creare sessioni.
        Per informazioni su come generare una chiave privata, vedere [sessioni Flask](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Dopo aver configurato l'accesso, è necessario aggiungere gli utenti al collegamento Azure AD per consentire l'accesso alla risorsa. Per aggiungerli, passare alla scheda **utenti** in Azure ad, selezionare **tutti gli utenti**e quindi selezionare **nuovo utente** o **nuovo utente Guest**. Per il test, è possibile aggiungere un utente Guest e invitare l'utente nella directory. In alternativa, è possibile aggiungere un nuovo utente se il dominio in cui è in esecuzione l'app è stato convalidato. In questo esempio, solo gli utenti registrati nel tenant di Azure AD possono essere registrati per l'accesso. Per informazioni sull'accesso multi-tenant, vedere la documentazione di.

   ![Aggiungere utenti al dominio predefinito](./media/secure-web-app/ad-auth-add-user.png)

   *Aggiungere utenti al dominio Azure Active Directory predefinito*

Dopo aver aggiunto la configurazione di Azure AD e i segreti ai Key Vault, gli utenti possono essere autenticati nell'app usando l'autenticazione OAuth di Azure.
Nel codice dell'app questo viene gestito dalla libreria di autenticazione Azure Active Directory (ADAL).

Quando i segreti si trovano in Key Vault e l'applicazione ha accesso ai segreti e al database, il servizio dell'applicazione può essere raggiunto tramite l'URL dell'applicazione del https://GATEWAY_HASH.cloudapp.net) Gateway (, che è possibile ottenere dal relativo pannello.

Se, quando si accede a Azure AD, viene visualizzato un messaggio di errore che indica che l'utente non è registrato nella directory a cui si sta tentando di accedere, ovvero è necessario aggiungere l'utente. Per aggiungere l'utente, passare alla scheda **utenti** di Azure ad e aggiungere manualmente l'utente immettendo i relativi dettagli o invitare l'utente immettendo il proprio indirizzo di posta elettronica come utente guest per Azure ad nel pannello **invita Guest** .

#### <a name="deploy-application-insights"></a>Distribuisci Application Insights
Ora che l'app è stata distribuita e funzionante, è necessario gestire gli errori che si verificano all'interno dell'app insieme alla raccolta dei dati di registrazione e di traccia.
La raccolta dei dati di registrazione e di traccia fornisce una visualizzazione degli eventi di controllo che si verificano nell'app.

Application Insights è un servizio che raccoglie log che possono essere generati dagli utenti o dal sistema.

Per creare un'istanza di Application Insights:

1. Cercare **Application Insights** usando la casella di ricerca nella portale di Azure.
2. Selezionare **Application Insights**. Fornire i dettagli riportati di seguito per creare un'istanza.

   ![Creare un'istanza di Application Insights](./media/secure-web-app/app-insights-data.png)

Al termine della distribuzione, si disporrà di un'istanza di Application Insights.

Dopo aver creato l'istanza di Application Insights, è necessario fare in modo che l'app riconosci la chiave di strumentazione che consente di inviare i log al cloud. A tale scopo, recuperare la chiave di Application Insights e usarla all'interno delle librerie di applicazioni fornite da Azure per Application Insights. La procedura consigliata consiste nell'archiviare le chiavi e i segreti in Azure Key Vault per mantenerli protetti.

Per l'app di esempio di base, dopo aver creato l'istanza di Application Insights, è necessario fare in modo che l'app riconosci la chiave di strumentazione che consente di inviare i log al cloud.
In Key Vault impostare un `APPINSIGHTSKEY` segreto e impostarne il valore come chiave di strumentazione. Questa operazione consente all'app di inviare log e metriche a Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementare la funzionalità di autenticazione a più fattori per Azure Active Directory
Gli amministratori devono assicurarsi che gli account di sottoscrizione nel portale siano protetti. La sottoscrizione è vulnerabile agli attacchi perché gestisce le risorse create. Per proteggere la sottoscrizione, abilitare la funzionalità di autenticazione a più fattori nella scheda **Azure Active Directory** della sottoscrizione.

Azure AD funziona in base ai criteri applicati a un utente o a gruppi di utenti che soddisfano determinati criteri.
Azure crea un criterio predefinito che specifica che gli amministratori devono eseguire l'autenticazione a due fattori per accedere al portale.
Dopo aver abilitato questo criterio, potrebbe essere richiesto di disconnettersi e accedere nuovamente al portale di Azure.

Per abilitare l'autenticazione a più fattori per gli accessi amministratore:

1. Passare alla scheda **Azure Active Directory** nella portale di Azure
2. Nella categoria sicurezza selezionare accesso condizionale. Viene visualizzata la schermata seguente:

   ![Accesso condizionale-criteri](./media/secure-web-app/ad-mfa-conditional-add.png)

Se non è possibile creare un nuovo criterio:

1. Passare **alla scheda autenticazione** a più fattori.
2. Selezionare il collegamento Azure AD Premium **versione di valutazione gratuita** per sottoscrivere la versione di valutazione gratuita.

   ![Azure AD Premium versione di valutazione gratuita](./media/secure-web-app/ad-trial-premium.png)

Tornare alla schermata di accesso condizionale.

1. Selezionare la scheda nuovo criterio.
2. Immettere il nome del criterio.
3. Selezionare gli utenti o i gruppi per i quali si desidera abilitare l'autenticazione a più fattori.
4. In **controlli di accesso**selezionare la scheda **Concedi** , quindi selezionare **Richiedi autenticazione** a più fattori e altre impostazioni, se lo si desidera.

   ![Richiedere l'autenticazione MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

È possibile abilitare il criterio selezionando la casella di controllo nella parte superiore della schermata oppure facendo clic sulla scheda **accesso condizionale** . Quando il criterio è abilitato, gli utenti devono avere l'autenticazione a più fattori per accedere al portale.

Sono disponibili criteri di base che richiedono l'autenticazione a più fattori per tutti gli amministratori di Azure. È possibile abilitarlo immediatamente nel portale. L'abilitazione di questo criterio potrebbe invalidare la sessione corrente e forzare l'accesso.

Se il criterio di base non è abilitato:
1.  Selezionare **Richiedi autenticazione a**più fattori per gli amministratori.
2.  Selezionare **usa immediatamente i criteri**.

   ![Selezionare Usa immediatamente il criterio](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Usare Sentinel di Azure per monitorare le app e le risorse

Man mano che un'applicazione cresce, diventa difficile aggregare tutti i segnali di sicurezza e le metriche ricevute dalle risorse e renderle utili in modo orientato alle azioni.

Azure Sentinel è progettato per raccogliere dati, rilevare i tipi di minacce possibili e fornire visibilità agli eventi imprevisti della sicurezza.
Sebbene attenda l'intervento manuale, Azure Sentinel può basarsi su PlayBook pre-scritti per avviare gli avvisi e i processi di gestione degli eventi imprevisti.

L'app di esempio è costituita da diverse risorse che possono essere monitorate da Azure Sentinel.
Per configurare Sentinel di Azure, è prima di tutto necessario creare un'area di lavoro di Log Analytics che archivia tutti i dati raccolti dalle varie risorse.

Per creare questa area di lavoro:

1. Nella casella di ricerca del portale di Azure cercare **log Analytics**. Selezionare **Aree di lavoro di Log Analytics**.

   ![Cerca aree di lavoro Log Analytics](./media/secure-web-app/sentinel-log-analytics.png)

    *Cerca aree di lavoro Log Analytics*

2. Nella pagina successiva selezionare **Aggiungi** e quindi specificare un nome, un gruppo di risorse e un percorso per l'area di lavoro.
   ![Creare un'area di lavoro Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Creare un'area di lavoro Log Analytics*

3. Usare la casella di ricerca per cercare **Azure Sentinel**.

   ![Cercare Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Cercare Azure Sentinel*

4. Selezionare **Aggiungi** e quindi selezionare l'area di lavoro log Analytics creata in precedenza.

   ![Aggiungere un'area di lavoro Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Aggiungere un'area di lavoro Log Analytics*

5. Nella pagina **Sentinel di Azure-connettori dati** , in **configurazione**, selezionare **connettori dati**. Viene visualizzata una matrice di servizi di Azure che è possibile collegare all'istanza di archiviazione Log Analytics per l'analisi in Sentinel di Azure.

   ![Connettori di dati Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Aggiungere un connettore dati ad Azure Sentinel*

   Ad esempio, per connettere il gateway applicazione, seguire questa procedura:

   1. Aprire il pannello dell'istanza di applicazione Azure gateway.
   2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
   3. Selezionare **Aggiungi impostazioni di diagnostica**.

      ![Aggiungere la diagnostica del gateway applicazione](./media/secure-web-app/sentinel-gateway-connector.png)

      *Aggiungere la diagnostica del gateway applicazione*

   4. Nella pagina **impostazioni di diagnostica** selezionare l'area di lavoro log Analytics creata e quindi selezionare tutte le metriche che si desidera raccogliere e inviare a Sentinel di Azure. Selezionare **Salva**.

        ![Impostazioni del connettore Sentinel di Azure](./media/secure-web-app/sentinel-connector-settings.png)

        *Impostazioni del connettore Sentinel di Azure*

  Le metriche della risorsa si trovano in Sentinel di Azure, in cui è possibile eseguire query ed esaminarle.

   Aggiungere le stesse metriche nelle impostazioni di diagnostica per Azure Key Vault, l'indirizzo IP pubblico, il database di Azure per PostgreSQL e tutti i servizi che supportano i log di diagnostica nell'account.

Dopo aver configurato la metrica, Azure Sentinel dispone di dati da analizzare.

## <a name="evaluate-and-verify"></a>Valutare e verificare
Dopo aver sviluppato e distribuito l'architettura, è necessario assicurarsi che il codice e i servizi distribuiti soddisfino gli standard di sicurezza. Ecco alcuni passaggi che è possibile eseguire per verificare il software:

- Analisi statica del codice
- Analisi delle vulnerabilità
- Individuazione e correzione delle vulnerabilità nelle dipendenze dell'applicazione

Questi sono i blocchi predefiniti di base per le procedure consigliate per lo sviluppo sicuro.

### <a name="static-code-analysis"></a>Analisi statica del codice
Per l'app di esempio, la verifica con gli strumenti di analisi statica comporta la ricerca di vulnerabilità nel codice dell'app usando tecniche come il controllo di Taint e l'analisi del flusso di dati. Gli strumenti di analisi statica di Python ti offrono maggiore sicurezza per la sicurezza dell'app.

**Lint**

PyFlakes, una libreria Python che consente di rimuovere il codice inattivo e le funzioni non usate dalle app, come illustrato di seguito:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Il problema fornisce suggerimenti e possibili modifiche che possono rendere il codice più pulito e meno soggetto a errori durante il Runtime.

**PyLint**

PyLint ha fornito il valore massimo per questo progetto. Esegue i controlli standard del codice, il controllo degli errori e i suggerimenti di refactoring per assicurarsi che il codice in esecuzione sul server sia sicuro. Usando PyLint per aggiornare il codice, è possibile eliminare i bug e migliorare la classificazione PyLint, come illustrato nelle immagini seguenti.

![Prima di PyLint](./media/secure-web-app/before-pylint.png)

*Prima di PyLint*

Dopo aver risolto alcuni degli errori del codice rilevati dagli strumenti di pelucchi, è più sicuro che il codice non sia soggetto a errori. La correzione degli errori comporta una riduzione significativa dei rischi di sicurezza che possono verificarsi quando il codice viene distribuito negli ambienti di produzione.

![Dopo Pylint](./media/secure-web-app/after-pylint.png)

*Dopo PyLint*

### <a name="vulnerability-scanning"></a>Analisi delle vulnerabilità
Lo strumento [zap di OWASP](https://www.zaproxy.org/) è uno scanner di vulnerabilità di applicazioni Web open source che è possibile usare per controllare le vulnerabilità nell'app di esempio. L'esecuzione dello strumento nell'app di esempio rivela alcuni possibili errori e vettori di attacco.

![Strumento ZAP](./media/secure-web-app/zap-tool.png)

*Strumento ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Trovare e correggere le vulnerabilità nelle dipendenze delle app
Per trovare e correggere le dipendenze dell'applicazione, è possibile usare [il controllo delle dipendenze di OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

La sicurezza è un'applicazione simile che controlla le dipendenze. È possibile trovarlo in [GitHub](https://github.com/pyupio/safety). Analisi della sicurezza per le vulnerabilità rilevate nei database di vulnerabilità noti.

![Sicurezza](./media/secure-web-app/pysafety.png)

*Sicurezza*

## <a name="next-steps"></a>Passaggi successivi
Gli articoli seguenti possono essere utili per progettare, sviluppare e distribuire applicazioni protette.

- [Progettazione](secure-design.md)
- [Sviluppo](secure-develop.md)
- [Distribuire](secure-deploy.md)
