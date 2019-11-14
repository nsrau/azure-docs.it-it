---
title: Distribuzione di macchine virtuali di Azure con chef
description: Imparare a utilizzare Chef per effettuare la distribuzione automatizzata della macchina virtuale e la configurazione in Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 58642cdbf164523390d5e4925290b43f6c05549b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039553"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automazione della distribuzione delle macchine virtuali di Azure con Chef

Chef rappresenta uno strumento molto utile che fornisce soluzioni automatizzate e configurazioni di stato personalizzate.

Con la versione più recente dell'API cloud, Chef offre una perfetta integrazione con Azure, consentendo di effettuare il provisioning e distribuire gli stati delle configurazioni attraverso un unico comando.

Questo articolo illustra come configurare l'ambiente Chef per il provisioning delle macchine virtuali di Azure e descrive la procedura dettagliata per creare un criterio o una guida di riferimento dettagliata e distribuirla successivamente in una macchina virtuale di Azure.

## <a name="chef-basics"></a>Nozioni di base su Chef
Prima di iniziare, [rivedere i concetti di base di Chef](https://www.chef.io/chef).

Il seguente diagramma illustra l'architettura di alto livello di Chef.

![][2]

Chef ha tre componenti principali dell'architettura: Chef Workstation, Chef Server e Chef Client (nodo).

Il server Chef rappresenta il punto di gestione per cui sono disponibili due opzioni: una soluzione in hosting e una soluzione locale.

Il client Chef (nodo) rappresenta l'agente che risiede nei server gestiti.

La workstation Chef rappresenta il nome sia della workstation di amministrazione, in cui si creano i criteri e si eseguono i comandi gestione, che del pacchetto software di strumenti Chef.

In genere, con _la workstation_ si intende la posizione in cui si eseguono le azioni e con _workstation Chef_ si intende il pacchetto software.
Il comando knife, ad esempio, verrà scaricato come parte della _workstation Chef_, ma i comandi knife vengono eseguiti dalla _workstation_ per gestire l'infrastruttura.

Chef usa anche i concetti di "guida di riferimento dettagliata" e di "recipe", che sono i criteri definiti e applicati ai server.

## <a name="preparing-your-workstation"></a>Predisposizione della workstation

In primo luogo, preparare la workstation creando una directory in cui archiviare file di configurazione e guide di riferimento dettagliate di Chef.

Creare una directory denominata C:\Chef.

Scaricare e installare la versione più recente dell'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nella workstation.

## <a name="configure-azure-service-principal"></a>Configurare un'entità servizio di Azure

In termini più semplici, l'entità servizio di Azure è un account del servizio.   Verrà usata un'entità servizio per semplificare la creazione delle risorse di Azure dalla workstation chef.  Per creare l'entità servizio pertinente con le autorizzazioni necessarie, è necessario eseguire i comandi seguenti all'interno di PowerShell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Prendere nota di SubscriptionID, TenantID, ClientID e client Secret (la password impostata in precedenza), che sarà necessaria in un secondo momento. 

## <a name="setup-chef-server"></a>Configurare il server Chef

Questa guida presuppone che si effettuerà l'iscrizione a Chef ospitato.

Se non si usa già un server Chef, è possibile:

* Iscriversi a [Chef ospitato](https://manage.chef.io/signup), che è il modo più rapido per iniziare a usare Chef.
* Installare un server Chef autonomo nel computer basato su Linux, seguendo le [istruzioni di installazione](https://docs.chef.io/install_server.html) della [documentazione di Chef](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Creazione di un account Chef ospitato

Iscriversi per ottenere un account Chef ospitato [qui](https://manage.chef.io/signup).

Durante il processo di iscrizione, verrà richiesto di creare una nuova organizzazione.

![][3]

Una volta creata l'organizzazione, scaricare lo Starter Kit.

![][4]

> [!NOTE]
> Se viene visualizzato un messaggio di avviso che indica che le chiavi verranno reimpostate, è consigliabile proseguire poiché non è ancora configurata alcuna infrastruttura.
>

Il file ZIP dello starter kit contiene i file di configurazione dell'organizzazione e la chiave utente nella directory `.chef`.

`organization-validator.pem` deve essere scaricato separatamente, perché è una chiave privata e le chiavi private non devono essere archiviate nel server Chef. Da [chef Manage](https://manage.chef.io/), passare alla sezione amministrazione e selezionare "Reimposta chiave di convalida", che fornisce un file da scaricare separatamente. Salvare il file in c:\chef.

### <a name="configuring-your-chef-workstation"></a>Configurazione della workstation Chef

Estrarre il contenuto del file chef-starter.zip in c:\chef.

Copiare tutti i file presenti nella directory chef-starter\chef-repo\.chef to your c:\chef.

Copiare il file `organization-validator.pem` in c:\chef, se è stato salvato in c:\Download

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

Aggiungere le informazioni seguenti al file knife.rb:

validation_client_name "MyOrg-validator"

validation_key "# {current_dir}/MyOrg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Queste righe garantiscono che Knife faccia riferimento alla directory Cookbooks in c:\chef\cookbooks e usa anche l'entità servizio di Azure creata durante le operazioni di Azure.

Il file knife.rb dovrebbe avere un aspetto simile all'esempio seguente:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

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

[Scaricare e installare](https://downloads.chef.io/chef-workstation/) la workstation Chef.
Installare la workstation Chef nel percorso predefinito. Questa installazione potrebbe richiedere alcuni minuti.

Nel desktop verrà visualizzato "CW PowerShell", un ambiente caricato con lo strumento, necessario per interagire con i prodotti Chef. Il PowerShell CW rende disponibili nuovi comandi ad hoc, ad esempio `chef-run`, oltre ai comandi dell'interfaccia della riga di comando chef tradizionali, ad esempio `chef`. Per conoscere la versione installata della workstation Chef e degli strumenti Chef, eseguire `chef -v`. È anche possibile controllare la versione della workstation selezionando "About Chef Workstation" (Informazioni sulla workstation Chef) nell'app della workstation Chef.

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
> L'ordine del percorso è importante. Se i percorsi opscode non sono presenti nell'ordine corretto, si verificheranno dei problemi.
>

Prima di continuare, riavviare la workstation.

### <a name="install-knife-azure"></a>Installare Knife Azure

Questa esercitazione presuppone che si usi Azure Resource Manager per interagire con la macchina virtuale.

Installare l'estensione Knife Azure. che fornisce il "plug-in Azure" a Knife.

Eseguire il comando indicato di seguito.

    chef gem install knife-azure ––pre

> [!NOTE]
> L'argomento "-pre" assicura che si riceverà la versione RC più recente del plug-in Azure di Knife, che fornisce l'accesso al set di API più recente.
>
>

È probabile che durante l'installazione verranno installate anche diverse dipendenze.

![][8]

Per assicurarsi che tutto sia configurato correttamente, eseguire il seguente comando:

    knife azurerm server list

Se tutto è stato configurato correttamente, verrà visualizzato un elenco delle immagini di Azure disponibili.

A questo punto la workstation è configurata.

## <a name="creating-a-cookbook"></a>Creazione di un cookbook

Chef usa i cookbook per definire i set di comandi che si desidera eseguire nel client gestito. La creazione di una guida di riferimento dettagliata è molto semplice. A tale scopo viene usato il comando **chef generate cookbook** per generare il modello. Questa guida di riferimento dettagliata è relativa a un server Web che distribuisce automaticamente IIS.

Nella directory C:\Chef eseguire il seguente comando.

    chef generate cookbook webserver

Questo comando genererà un set di file nella directory C:\Chef\cookbooks\webserver. Definire quindi il set di comandi che il client Chef dovrà eseguire nella macchina virtuale gestita.

I comandi vengono archiviati nel file default.rb. In questo file definire un set di comandi che consente di installare IIS, avviare IIS e copiare un file di modello nella cartella wwwroot.

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
In questo passaggio si genererà un file di modello da usare come pagina default.html.

Eseguire il comando seguente per generare il modello:

    chef generate template webserver Default.htm

Passare al file `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb`. Modificare il file aggiungendo un semplice codice HTML, ad esempio alcune parole di benvenuto, e salvare il file.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Caricare il cookbook nel server Chef
In questo passaggio si crea una copia della guida di riferimento dettagliata creata nel computer locale che verrà caricata nel server Chef ospitato. Dopo il caricamento, la guida di riferimento dettagliata viene visualizzata nella scheda **Policy** (Criteri).

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuzione di una macchina virtuale con il comando Knife Azure
Distribuire una macchina virtuale di Azure e applicare la guida di riferimento dettagliata "Webserver", che installa il servizio Web IIS e la pagina Web predefinita.

A tale scopo, usare il comando **Knife azurerm server create** .

Un esempio del comando è visualizzato qui di seguito.

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


Nell'esempio precedente viene creato un Standard_DS2_v2 macchina virtuale con Windows Server 2016 installato all'interno dell'area Stati Uniti occidentali. Sostituire le variabili desiderate ed eseguire il comando.

> [!NOTE]
> Nella riga di comando sono state automatizzate anche le regole di filtro per la rete degli endpoint mediante il parametro –tcp-endpoints. Ho aperto le porte 80 e 3389 per consentire l'accesso alla pagina Web e alla sessione RDP.
>
>

Dopo aver eseguito il comando, passare al portale di Azure, dove il computer inizia già a effettuare il provisioning.

![][15]

Di seguito viene visualizzato il prompt dei comandi.

![][16]

Al termine della distribuzione, l'indirizzo IP pubblico della nuova macchina virtuale verrà visualizzato al termine della distribuzione. è possibile copiarlo e incollarlo in un Web browser e visualizzare il sito Web distribuito. Quando la macchina virtuale è stata distribuita, è stata aperta la porta 80 in modo che sia disponibile esternamente.   

![][11]

Questo esempio usa un codice HTML creativo.

È anche possibile visualizzare lo stato di [gestione](https://manage.chef.io/)del nodo. 

![][17]

Tenere presente che è anche possibile connettersi tramite una sessione RDP dal Portale di Azure attraverso la porta 3389.

Grazie! Ora è possibile avviare l'infrastruttura come percorso di codice con Azure.

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
