---
title: Introduzione all'interfaccia della riga di comando XPlat per Azure Service Fabric
description: Introduzione all'interfaccia della riga di comando XPlat per Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9190b01b6ce42ea67ec7806c55a0013834d24211
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017

---

# <a name="using-the-xplat-cli-to-interact-with-a-service-fabric-cluster"></a>Uso dell'interfaccia della riga di comando XPlat per interagire con un cluster di Service Fabric

È possibile interagire con un cluster di Service Fabric da computer Linux usando l'interfaccia della riga di comando XPlat in Linux.

Come primo passaggio è necessario ottenere la versione più recente dell'interfaccia della riga di comando dal repository Git e configurarla nel proprio percorso usando i comandi seguenti:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

È possibile digitare il nome di ogni comando supportato per ottenere la relativa Guida.
Per i comandi è supportato il completamento automatico. Il comando seguente, ad esempio, visualizza la Guida per tutti i comandi dell'applicazione. 

```sh
 azure servicefabric application 
```

È anche possibile visualizzare la Guida per un comando specifico, come illustra l'esempio seguente:

```sh
 azure servicefabric application  create
```

Per abilitare il completamento automatico nell'interfaccia della riga di comando, eseguire questi comandi:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

I comandi seguenti connettono al cluster e visualizzano i nodi nel cluster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Per usare parametri denominati e trovare quali sono, è possibile digitare --help dopo un comando, Ad esempio:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Quando ci si connette a un cluster costituito da più macchine virtuali da un computer **che non fa parte del cluster**, usare il comando seguente:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Sostituire il tag PublicIPorFQDN con l'IP o il nome FQDN effettivo, in base alle proprie esigenze. Quando ci si connette a un cluster costituito da più macchine virtuali da un computer **che fa parte del cluster**, usare il comando seguente:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

È possibile usare PowerShell o l'interfaccia della riga di comando per interagire con il cluster di Service Fabric per Linux creato con il portale di Azure.

> [!WARNING]
> Questi cluster non sono protetti, quindi c'è la possibilità di aprire l'unica finestra di dialogo aggiungendo l'indirizzo IP pubblico nel manifesto del cluster.

## <a name="using-the-xplat-cli-to-connect-to-a-service-fabric-cluster"></a>Uso dell'interfaccia della riga di comando XPlat per la connessione a un cluster di Service Fabric

I comandi dell'interfaccia della riga di comando di Azure seguenti illustrano come connettersi a un cluster sicuro. I dettagli del certificato devono corrispondere a un certificato sui nodi del cluster.

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

Se il certificato ha autorità di certificazione (CA), è necessario aggiungere il parametro --ca-cert-path come illustrato nell'esempio seguente: 

```sh
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```

Se sono presenti più CA, usare una virgola come delimitatore.

Se il nome comune nel certificato non corrisponde all'endpoint di connessione, è possibile usare il parametro `--strict-ssl-false` per ignorare la verifica, come illustrato nel comando seguente:

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

Per ignorare la verifica della CA, è possibile aggiungere il parametro --reject-unauthorized, come illustrato nel comando seguente: 

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Dopo la connessione, sarà possibile eseguire altri comandi dell'interfaccia della riga di comando per interagire con il cluster.

## <a name="deploying-your-service-fabric-application"></a>Distribuzione dell'applicazione di Service Fabric

Eseguire questi comandi per copiare, registrare e avviare l'applicazione di Service Fabric:

```sh
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```

## <a name="upgrading-your-application"></a>Aggiornamento dell'applicazione

Il processo è simile al [processo in Windows](service-fabric-application-upgrade-tutorial-powershell.md).

Compilare, copiare, registrare e creare l'applicazione dalla directory radice del progetto. Se il nome dell'istanza dell'applicazione è `fabric:/MySFApp` e il tipo è MySFApp, i comandi saranno i seguenti:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Apportare la modifica all'applicazione e ricompilare il servizio modificato.  Aggiornare il file manifesto del servizio modificato (ServiceManifest.xml) con le versioni aggiornate del servizio (e del codice, della configurazione o dei dati, in base alle proprie esigenze). Modificare anche il manifesto dell'applicazione (ApplicationManifest.xml) con il numero di versione aggiornato per l'applicazione e il servizio modificato.  Ora copiare e registrare l'applicazione aggiornata usando i comandi seguenti:

```sh
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Ora è possibile avviare l'aggiornamento dell'applicazione con il comando seguente:

```sh
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0 --rolling-upgrade-mode UnmonitoredAuto
```

Ora è possibile monitorare l'aggiornamento dell'applicazione usando SFX. In pochi minuti, l'applicazione risulterà aggiornata. È anche possibile provare un'app aggiornata con un errore e controllare la funzionalità di ripristino dello stato precedente automatico in Service Fabric.

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>Conversione da PFX PEM e viceversa

Potrebbe essere necessario installare un certificato nel computer locale (con Windows o Linux) per accedere a cluster protetti che possono trovarsi in un ambiente diverso. Durante l'accesso a un cluster Linux protetto da un computer Windows e viceversa, ad esempio, può essere necessario convertire il certificato PFX in PEM e viceversa. 

Per eseguire la conversione da un file PEM a un file PFX, usare il comando seguente:

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

Per eseguire la conversione da un file PFX a un file PEM, usare il comando seguente:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Per informazioni dettagliate, vedere la [documentazione di OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html).

<a id="troubleshooting"></a>

## <a name="troubleshooting"></a>Risoluzione dei problemi


### <a name="copying-of-the-application-package-does-not-succeed"></a>Non è possibile copiare il pacchetto dell'applicazione

Controllare se `openssh` è installato. Per impostazione predefinita, in Ubuntu Desktop non è installato. Installarlo usando il comando seguente:

```sh
sudo apt-get install openssh-server openssh-client**
```

Se il problema persiste, provare a disabilitare PAM per SSH modificando il file `sshd_config` con i comandi seguenti:

```sh
sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
sudo service sshd reload
```

Se il problema persiste ancora, provare ad aumentare il numero di sessioni SSH eseguendo questi comandi:

```sh
sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
sudo service sshd reload
```

Poiché l'uso di chiavi per l'autenticazione SSH (invece che di password) non è ancora supportato (perché la piattaforma usa SSH per copiare i pacchetti), usare l'autenticazione della password.

## <a name="next-steps"></a>Passaggi successivi

[Configurare l'ambiente di sviluppo e distribuire un'applicazione di Service Fabric in un cluster Linux.](service-fabric-get-started-linux.md)

## <a name="related-articles"></a>Articoli correlati

* [Introduzione a Service Fabric e all'interfaccia della riga di comando di Azure 2.0](service-fabric-azure-cli-2-0.md)

