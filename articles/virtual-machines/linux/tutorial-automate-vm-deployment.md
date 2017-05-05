---
title: Personalizzare una macchina virtuale Linux al primo avvio in Azure | Microsoft Docs
description: Informazioni su come usare cloud-init e Key Vault per personalizzare le macchine virtuali Linux al primo avvio in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 8f86f812cd708d8122ecc507d02fb2ec2c73689f
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Personalizzare una macchina virtuale al primo avvio
Per creare macchine virtuali (VM) in modo rapido e coerente, di norma è consigliabile una qualche forma di automazione. Un approccio comune per personalizzare una macchina virtuale al primo avvio consiste nell'usare [cloud-init](https://cloudinit.readthedocs.io). Questa esercitazione illustra come usare cloud-init per installare un pacchetto, configurare il server Web NGINX e distribuire un'applicazione Node.js automaticamente.

I passaggi descritti in questa esercitazione possono essere eseguiti usando la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).


## <a name="cloud-init-overview"></a>Panoramica di cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) è un approccio diffuso per personalizzare una macchina virtuale Linux al primo avvio. Cloud-init consente di installare pacchetti e scrivere file o configurare utenti e impostazioni di sicurezza. Quando cloud-init viene eseguito durante il processo di avvio iniziale non vi sono altri passaggi o agenti necessari per applicare la configurazione.

Cloud-init funziona anche fra distribuzioni. Ad esempio, non si usa `apt-get install` o `yum install` per installare un pacchetto. In alternativa, è possibile definire un elenco di pacchetti da installare. Cloud-init userà automaticamente lo strumento di gestione del pacchetto nativo per la distribuzione selezionata.

Microsoft collabora con i partner per promuovere l'inclusione e il funzionamento di cloud-init con le immagini da essi fornite per Azure. La tabella seguente illustra l'attuale disponibilità di cloud-init nelle immagini della piattaforma Azure:

| Alias | Autore | Offerta | SKU | Versione |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |più recenti |
| CoreOS |CoreOS |CoreOS |Stabile |più recenti |


## <a name="create-config-file"></a>Creare un file config
Per visualizzare cloud-init in azione, creare una macchina virtuale, installare NGINX ed eseguire una semplice app Node.js "Hello World". La configurazione cloud-init seguente installa i pacchetti necessari, crea un'applicazione Node.js, quindi inizializza e avvia l'applicazione.

Creare un file denominato `cloud-init.txt` e incollare la configurazione seguente:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Per altre informazioni sulle opzioni di configurazione di cloud-init, vedere gli [esempi di configurazione di cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)]


## <a name="create-virtual-machine"></a>Crea macchina virtuale
Per poter creare una macchina virtuale è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroupAutomate` nella località `westus`:

```azurecli
az group create --name myResourceGroupAutomate --location westus
```

Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). Usare il parametro `--custom-data` per specificare il file di configurazione di cloud-init. Se il file è stato salvato all'esterno della directory di lavoro corrente, specificare il percorso completo di configurazione `cloud-init.txt`. Nell'esempio seguente viene creata una VM chiamata `myAutomatedVM`:

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Per creare la macchina virtuale, installare i pacchetti e avviare l'applicazione sono necessari alcuni minuti. Dopo aver creato la macchina virtuale, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Questo indirizzo viene usato per accedere all'app Node.js tramite un Web browser.

Per consentire al traffico Web di raggiungere la macchina virtuale, aprire la porta 80 da Internet con il comando [az vm open_port](/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Testare l'app Web
È ora possibile aprire un Web browser e immettere `http://<publicIpAddress>` bella barra degli indirizzi. Fornire il proprio indirizzo IP pubblico dal processo di creazione della macchina virtuale. L'app Node.js viene visualizzata come illustrato nell'esempio seguente:

![Visualizzare il sito NGINX in esecuzione](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Inserire certificati da Key Vault
Questa sezione facoltativa illustra come archiviare in modo protetto i certificati in Azure Key Vault e inserirli durante la distribuzione della macchina virtuale. Anziché usare un'immagine personalizzata che includa i certificati integrati, questo processo assicura che i certificati aggiornati vengano inseriti in una macchina virtuale al primo avvio. Durante il processo, il certificato non lascia mai la piattaforma Azure e non è esposto in script, cronologie delle righe di comando o modelli.

Azure Key Vault consente di proteggere chiavi di crittografia, chiavi private, certificati e password. Key Vault semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi che accedono ai dati e li crittografano. Questo scenario introduce alcuni concetti chiave di Key Vault per creare e usare un certificato, sebbene non rappresenti una panoramica esaustiva sull'uso di Key Vault.

I passaggi seguenti mostrano come sia possibile:

- Creare un Azure Key Vault
- Generare o caricare un certificato in Key Vault
- Creare una chiave privata dal certificato da inserire in una macchina virtuale
- Creare una macchina virtuale e inserire il certificato

### <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault
Innanzitutto, creare un Key Vault con il comando [az keyvault create](/cli/azure/keyvault#create) e abilitarlo all'uso quando si distribuisce una macchina virtuale. Ogni Key Vault deve avere un nome univoco in lettere minuscole. Nell'esempio seguente, sostituire `<mykeyvault>` con il nome univoco del proprio Key Vault:

```azurecli
keyvault_name=<mykeyvault>
az keyvault create --resource-group myResourceGroupAutomate --name $keyvault_name --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generare certificati e archiviarli in Key Vault
Per la produzione è necessario importare un certificato valido firmato da un provider attendibile con il comando [az keyvault certificate import](/cli/azure/certificate#import). Per questa esercitazione, l'esempio seguente illustra come sia possibile generare un certificato autofirmato con il comando [az keyvault certificate create](/cli/azure/certificate#create) che usi i criteri dei certificati predefiniti:

```azurecli
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Preparare i certificati per l'uso con macchine virtuali
Per usare il certificato durante il processo di creazione della macchina virtuale, ottenere l'ID del certificato con il comando [az keyvault secret list-versions](/cli/azure/keyvault/secret#list-versions). Convertire il certificato con il comando [az vm format-secret](/cli/azure/vm#format-secret). L'esempio seguente assegna l'output di questi comandi a delle variabili per semplificarne l'uso nei passaggi successivi:

```azurecli
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Creare una configurazione cloud-init per proteggere NGINX
Quando si crea una macchina virtuale, certificati e chiavi vengono archiviati nella directory `/var/lib/waagent/` protetta. Per automatizzare l'aggiunta del certificato alla macchina virtuale e la configurazione di NGINX, è possibile espandere la configurazione di cloud-init dell'esempio precedente.

Creare un file denominato `cloud-init-secured.txt` e incollare la configurazione seguente:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Creare una macchina virtuale protetta
Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm#create). I dati del certificato sono inseriti da Key Vault con il parametro `--secrets`. Come nell'esempio precedente, si seleziona anche la configurazione cloud-init con il parametro `--custom-data`:

```azurecli
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image Canonical:UbuntuServer:14.04.4-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Per creare la macchina virtuale, installare i pacchetti e avviare l'applicazione sono necessari alcuni minuti. Dopo aver creato la macchina virtuale, prendere nota del `publicIpAddress` visualizzato dall'interfaccia della riga di comando di Azure. Questo indirizzo viene usato per accedere all'app Node.js tramite un Web browser.

Per consentire al traffico Web protetto di raggiungere la macchina virtuale, aprire la porta 443 da Internet con il comando [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port --port 443 --resource-group myResourceGroupAutomate --name myVMSecured
```

### <a name="test-secure-web-app"></a>Testare l'applicazione Web protetta
È ora possibile aprire un Web browser e immettere `https://<publicIpAddress>` nella barra degli indirizzi. Fornire il proprio indirizzo IP pubblico dal processo di creazione della macchina virtuale. Se è stato usato un certificato autofirmato, accettare l'avviso di sicurezza:

![Accettare l'avviso di sicurezza del Web browser](./media/tutorial-automate-vm-deployment/browser-warning.png)

Il sito protetto NGINX e la app Node.js sono visualizzati come illustrato nell'esempio seguente:

![Visualizzare il sito protetto NGINX in esecuzione](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come personalizzare una macchina virtuale al primo avvio. Passare all'esercitazione successiva per imparare a creare immagini di macchine virtuali personalizzate.

[Creare un'immagine di VM personalizzata](./tutorial-custom-images.md)

