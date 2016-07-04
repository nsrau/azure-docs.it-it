<properties
   pageTitle="Creare host Docker in Azure con Docker Machine | Microsoft Azure"
   description="Descrive l'uso di Docker Machine per creare host Docker in Azure."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark;stevelas" />

# Creare host Docker in Azure con Docker Machine

L'esecuzione dei contenitori [Docker](https://www.docker.com/) richiede una VM host che esegue il daemon Docker. In questo argomento viene descritto come usare il comando [docker-machine](https://docs.docker.com/machine/) per creare nuove macchine virtuali Linux, configurate con il daemon Docker, in esecuzione in Azure.

**Nota:**
- *questo articolo si basa sulla versione di Docker Machine 0.7.0 o versione successiva*
- *I contenitori Windows saranno supportati tramite Docker Machine nel prossimo futuro*

## Creare VM con Docker Machine

Creare macchine virtuali host di Docker in Azure con il comando `docker-machine create` usando il driver `azure`.

Il driver Azure necessita dell'ID sottoscrizione. È possibile utilizzare l’[interfaccia della riga di comando di Azure](xplat-cli-install.md) o il [portale di Azure](https://portal.azure.com) per recuperare la sottoscrizione di Azure.

**Uso del portale di Azure**
- Selezionare le sottoscrizioni nella pagina di navigazione a sinistra e copiare l'ID sottoscrizione.

**Uso dell'interfaccia della riga di comando di Azure**
- Digitare ```azure account list``` e copiare l'ID sottoscrizione.

Digitare `docker-machine create --driver azure` per visualizzare le opzioni e i relativi valori predefiniti. È anche possibile visualizzare la [documentazione del driver di Azure per Docker](https://docs.docker.com/machine/drivers/azure/) per altre informazioni.

L'esempio seguente si basa sui valori predefiniti, ma è possibile anche aprire la porta 80 della VM per accedere a Internet.

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-open-port 80 mydockerhost
```

## Scegliere un host di Docker con Docker Machine
Dopo aver creato un ingresso nella Docker Machine per l'host, è possibile impostare l'host predefinito quando si eseguono comandi di Docker.
##Tramite PowerShell

```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

##Tramite Bash

```bash
eval $(docker-machine env MyDockerHost)
```

È ora possibile eseguire i comandi di Docker con l'host specificato

```
docker ps
docker info
```

## Eseguire un contenitore

Dopo aver configurato l'host, è possibile eseguire un semplice server Web per verificare che la configurazione dell'host sia corretta. In questo esempio viene usata un'immagine nginx standard. Specificare che deve essere in ascolto sulla porta 80 e che, se la VM dell'host viene riavviata, anche il contenitore deve essere riavviato (`--restart=always`).

```bash
docker run -d -p 80:80 --restart=always nginx
```

L'output dovrebbe essere simile a quanto segue.

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## Eseguire il test del contenitore

Esaminare i contenitori in esecuzione usando `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Quindi verificare il contenitore in esecuzione digitando `docker-machine ip <VM name>` per trovare l'indirizzo IP da immettere nel browser:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Esecuzione di un contenitore ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

##Riepilogo
Docker Machine consente di eseguire facilmente il provisioning di host Docker in Azure per le convalide di host Docker singoli. Per l'hosting di produzione di contenitori, vedere l'articolo sul [servizio contenitore di Azure](http://aka.ms/AzureContainerService)

Per sviluppare applicazioni .NET Core con Visual Studio, vedere gli [Strumenti Docker per Visual Studio](http://aka.ms/DockerToolsForVS)

<!---HONumber=AcomDC_0622_2016-->