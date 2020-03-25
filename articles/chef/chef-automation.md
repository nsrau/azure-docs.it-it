---
title: 'Avvio rapido: Configurare una macchina virtuale Windows in Azure con Chef'
description: Questa guida di avvio rapido illustra come usare Chef per distribuire e configurare una macchina virtuale Windows in Azure
keywords: chef, azure, devops, macchina virtuale
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77589495"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Avvio rapido: Configurare una macchina virtuale Windows in Azure con Chef

Chef consente di recapitare configurazioni dello stato desiderato e di automazione.

Con la versione più recente dell'API cloud, Chef offre una perfetta integrazione con Azure, consentendo di effettuare il provisioning e distribuire gli stati delle configurazioni attraverso un unico comando.

Questo articolo illustra come configurare l'ambiente Chef per il provisioning delle macchine virtuali di Azure e descrive la procedura dettagliata per creare un criterio o cookbook e distribuirlo successivamente in una macchina virtuale di Azure.

## <a name="chef-basics"></a>Nozioni di base su Chef

Prima di iniziare con questo articolo, [rivedere i concetti di base di Chef](https://www.chef.io/chef).

Il diagramma seguente illustra l'architettura generale di Chef.

![Architettura di Chef](media/chef-automation/chef-architecure.png)

Chef ha tre componenti principali dell'architettura: 
- Il server Chef rappresenta il punto di gestione per cui sono disponibili due opzioni: una soluzione in hosting e una soluzione locale.
- Il client Chef (nodo) rappresenta l'agente che risiede nei server gestiti.
- La workstation Chef rappresenta il nome sia della workstation di amministrazione, in cui si creano i criteri e si eseguono i comandi gestione, che del pacchetto software di strumenti Chef.

In genere, con **workstation** si intende la posizione in cui si eseguono i comandi e con **workstation Chef** si intende il pacchetto software.

Il comando knife, ad esempio, verrà scaricato come parte della **workstation Chef**, ma i comandi knife vengono eseguiti dalla **workstation** per gestire l'infrastruttura.

Chef usa inoltre i concetti di *cookbook* e *recipe*. Questi termini sono i criteri che vengono definiti e applicati rispettivamente ai server.

## <a name="preparing-your-workstation"></a>Predisposizione della workstation

In primo luogo, preparare la workstation creando una directory in cui archiviare file di configurazione e guide di riferimento dettagliate di Chef.

Crea una directory denominata `C:\Chef`.

Scaricare e installare la versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nella workstation.

## <a name="configure-azure-service-principal"></a>Configurare un'entità servizio di Azure

Verrà usata un'entità servizio per semplificare la creazione delle risorse di Azure dalla workstation Chef.  Per creare l'entità servizio pertinente con le autorizzazioni necessarie, eseguire i comandi seguenti in PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Prendere nota dei valori di SubscriptionID, TenantID, ClientID e Client Secret (la password impostata in precedenza in questa esercitazione), perché questi valori saranno necessari in seguito. 

## <a name="setup-chef-server"></a>Configurare il server Chef

Questa guida presuppone che si effettuerà l'iscrizione per la soluzione Chef ospitata.

Se non si usa già un server Chef, è possibile:

* Iscriversi a [Chef ospitato](https://manage.chef.io/signup), che è il modo più rapido per iniziare a usare Chef.
* Installare un server Chef autonomo nel computer basato su Linux, seguendo le [istruzioni di installazione](https://docs.chef.io/install_server.html) della [documentazione di Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Creazione di un account Chef ospitato

Iscriversi per ottenere un account Chef ospitato [qui](https://manage.chef.io/signup).

Durante il processo di iscrizione, verrà richiesto di creare una nuova organizzazione.

![Finestra di creazione dell'organizzazione](media/chef-automation/create-organization.png)

Una volta creata l'organizzazione, scaricare lo Starter Kit.

![Configurazione di Chef](media/chef-automation/configure-chef.png)

> [!NOTE]
> Se viene visualizzato un messaggio di avviso che indica che le chiavi verranno reimpostate, è consigliabile proseguire poiché non è ancora configurata alcuna infrastruttura.
>

Il file ZIP dello starter kit contiene i file di configurazione dell'organizzazione e la chiave utente nella directory `.chef`.

Il file `organization-validator.pem` deve essere scaricato separatamente, perché è una chiave privata e le chiavi private non devono essere archiviate nel server Chef. Da [Chef Manage](https://manage.chef.io/) (Gestione Chef) passare alla sezione Administration (Amministrazione) e selezionare "Reset Validation Key" (Reimposta chiave di convalida), per ottenere un file da scaricare separatamente. Salvare il file in c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurazione della workstation Chef

Estrarre il contenuto del file `chef-starter.zip` in `c:\chef`.

Copiare tutti i file in `chef-starter\chef-repo\.chef` nella directory `c:\chef`.

Copiare il file `organization-validator.pem` in `c:\chef`, se è salvato in `c:\Downloads`.

La directory avrà ora un aspetto analogo al seguente esempio:

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

Si avranno ora cinque file e quattro directory (inclusa la directory vuota chef-repo) nella radice di c:\chef.

### <a name="edit-kniferb"></a>Modificare knife.rb

I file PEM contengono le chiavi private dell'organizzazione e amministrative per la comunicazione e il file knife.rb contiene la configurazione Knife. È necessario modificare il file knife.rb.

Aprire il file knife.rb nell'editor preferito. Il file non modificato sarà simile al seguente:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Aggiungere le informazioni seguenti a knife.rb, sostituendo i segnaposto con le proprie informazioni:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Queste righe consentono di garantire che Knife faccia riferimento alla directory Cookbooks in `c:\chef\cookbooks`.

Il file `knife.rb` dovrebbe avere un aspetto simile all'esempio seguente:

![Esempio di file Knife](./media/chef-automation/knife-file-example.png)

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Installare la workstation Chef

A questo punto, [scaricare e installare la workstation Chef](https://downloads.chef.io/chef-workstation/).

Installare la workstation Chef nel percorso predefinito.

Sul desktop verrà visualizzato uno strumento CW PowerShell. Questo strumento viene usato per interagire con i prodotti Chef. Lo strumento CW PowerShell rende disponibili nuovi comandi, ad esempio `chef-run`, e i comandi dell'interfaccia della riga di comando di Chef, ad esempio `chef`. Per conoscere la versione installata della workstation Chef e degli strumenti Chef, eseguire `chef -v`. È anche possibile controllare la versione della workstation selezionando **About Chef Workstation** (Informazioni sulla workstation Chef) nell'app Workstation Chef.

`chef --version` restituirà un output simile al seguente:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> L'ordine del percorso è importante. Se i percorsi di opscode non sono elencati nell'ordine corretto, si verificheranno dei problemi.
>

Prima di continuare, riavviare la workstation.

### <a name="install-knife-azure"></a>Installare Knife Azure

Questa esercitazione presuppone che si usi Azure Resource Manager per interagire con la macchina virtuale.

Installare l'estensione Knife Azure, che include il plug-in Azure.

Eseguire il comando seguente.

    chef gem install knife-azure ––pre

> [!NOTE]
> L'argomento `–-pre` assicura che si riceverà la versione RC più recente del plug-in Knife Azure, che fornisce l'accesso al set di API più recente.
>
>

È probabile che durante l'installazione verranno installate anche diverse dipendenze.

![Output dell'installazione di knife-azure](./media/chef-automation/install-knife-azure.png)

Per assicurarsi che tutto sia configurato correttamente, eseguire il seguente comando:

    knife azurerm server list

Se tutto è stato configurato correttamente, verrà visualizzato un elenco delle immagini di Azure disponibili.

A questo punto la workstation è configurata.

## <a name="creating-a-cookbook"></a>Creazione di un cookbook

Chef usa i cookbook per definire i set di comandi che si desidera eseguire nel client gestito. La creazione di un cookbook è molto semplice. A tale scopo viene usato il comando `chef generate cookbook` per generare il modello di cookbook. Questa guida di riferimento dettagliata è relativa a un server Web che distribuisce automaticamente IIS.

In `C:\Chef directory` eseguire il comando seguente.

    chef generate cookbook webserver

Questo comando genererà un set di file nella directory C:\Chef\cookbooks\webserver. Definire quindi il set di comandi che il client Chef dovrà eseguire nella macchina virtuale gestita.

I comandi vengono archiviati nel file default.rb. In questo file definire un set di comandi che consente di installare IIS, avviare IIS e copiare un file modello nella cartella `wwwroot`.

Modificare il file C:\chef\cookbooks\webserver\recipes\default.rb e aggiungere le seguenti righe:

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Al termine dell'operazione, salvare il file.

## <a name="creating-a-template"></a>Creazione di un modello

In questo passaggio verrà generato un file modello da usare come pagina `default.html`.

Eseguire il comando seguente per generare il modello:

    chef generate template webserver Default.htm

Passare al file `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Modificare il file aggiungendo un semplice codice HTML *Hello World* e salvare il file.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Caricare il cookbook nel server Chef

In questo passaggio si crea una copia del cookbook creato nel computer locale e la si caricherà nel server Chef ospitato. Una volta caricato, il cookbook verrà visualizzato nella scheda **Policy** (Criteri).

    knife cookbook upload webserver

![Risultati dell'installazione del cookbook nel server Chef](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuzione di una macchina virtuale con il comando Knife Azure

Distribuire una macchina virtuale di Azure e applicare il cookbook `Webserver` usando il comando `knife`.

Il comando `knife` installerà anche il servizio Web IIS e la pagina Web predefinita.

```bash
    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"
```

L'esempio di comando `knife` crea una macchina virtuale *Standard_DS2_v2* in cui è installato Windows Server 2016 nell'area Stati Uniti occidentali. Modificare questi valori in base alle esigenze dell'app.

Dopo aver eseguito il comando, passare al portale di Azure e osservare che viene avviato il provisioning della macchina virtuale.

![Macchina virtuale di cui è in corso il provisioning](./media/chef-automation/virtual-machine-being-provisioned.png)

Di seguito viene visualizzato il prompt dei comandi.

![Output di Knife durante la creazione della macchina virtuale](./media/chef-automation/knife-output-when-creating-vm.png)

Al termine della distribuzione, verrà visualizzato l'indirizzo IP pubblico della nuova macchina virtuale. Incollare questo valore in un Web browser per visualizzare il nuovo sito Web. Quando la macchina virtuale è stata distribuita, è stata aperta la porta 80 per renderla disponibile esternamente.   

![Test della macchina virtuale](./media/chef-automation/testing-the-virtual-machine.png)

Questo esempio usa un codice HTML creativo.

È anche possibile visualizzare lo stato del nodo [Chef Manage](https://manage.chef.io/). 

![Visualizzazione dello stato del nodo](./media/chef-automation/viewing-node-status.png)

Tenere presente che è anche possibile connettersi tramite una sessione RDP dal Portale di Azure attraverso la porta 3389.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Chef in Azure](/azure/chef/)