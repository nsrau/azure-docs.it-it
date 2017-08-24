---
title: Distribuire la prima app a Cloud Foundry in Microsoft Azure | Microsoft Docs
description: Distribuire un'applicazione a Cloud Foundry in Azure
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: a49b76e4fa0e0d5de7c0b7b758e8103a0a79a140
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuire la prima app a Cloud Foundry in Microsoft Azure

[Cloud Foundry](http://cloudfoundry.org) è una popolare piattaforma di applicazioni open source disponibile in Microsoft Azure. In questo articolo viene illustrato come distribuire e gestire un'applicazione in Cloud Foundry in un ambiente Azure.

## <a name="create-a-cloud-foundry-environment"></a>Creare un ambiente Cloud Foundry

Esistono diverse opzioni per la creazione di un ambiente Cloud Foundry in Azure:

- Usare l'[offerta Pivotal Cloud Foundry][pcf-azuremarketplace] in Azure Marketplace per creare un ambiente standard che include PCF Operations Manager e il Service Broker di Azure. Per le [istruzioni complete][pcf-azuremarketplace-pivotaldocs] per la distribuzione dell'offerta del marketplace, vedere la documentazione di Pivotal.
- Creare un ambiente personalizzato [distribuendo manualmente Pivotal Cloud Foundry][pcf-custom].
- [Distribuire i pacchetti open source di Cloud Foundry direttamente][oss-cf-bosh] impostando un [BOSH](http://bosh.io) director, una macchina virtuale che coordina la distribuzione dell'ambiente Cloud Foundry.

> [!IMPORTANT] 
> Se si distribuisce PCF da Azure Marketplace, annotare il SYSTEMDOMAINURL e le credenziali amministratore necessarie per accedere al gestore di app di Pivotal, entrambi descritti nella Guida alla distribuzione dal marketplace. Questi elementi sono necessari per completare questa esercitazione. Per le distribuzioni dal marketplace, il SYSTEMDOMAINURL è nel formato https://system.*indirizzo-ip*.cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Connettersi al controller del cloud

Il controller del cloud è il punto di ingresso principale in un ambiente Cloud Foundry per la distribuzione e la gestione delle applicazioni. L'API del controller del cloud di base (CCAPI) è un'API REST, ma è accessibile attraverso vari strumenti. In questo caso, si interagisce con essa tramite l'[interfaccia della riga di comando di Cloud Foundry][cf-cli]. L'interfaccia della riga di comando può essere installata su Linux, MacOS o Windows, ma se si preferisce non installarla è disponibile preinstallata in [Azure Cloud Shell][cloudshell-docs].

Per eseguire l'accesso, anteporre `api` al SYSTEMDOMAINURL ottenuto dalla distribuzione dal marketplace. Poiché la distribuzione predefinita usa un certificato autofirmato, è necessario includere anche l'istruzione `skip-ssl-validation`.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Viene chiesto di accedere al controller del cloud. Usare le credenziali dell'account amministratore acquisite dalle fasi di distribuzione del marketplace.

Cloud Foundry offre *organizzazioni* e *spazi* come spazi dei nomi per isolare i team e gli ambienti all'interno di una distribuzione condivisa. La distribuzione dal marketplace PCF include l'organizzazione *sistema* predefinita e un set di spazi creato per contenere i componenti di base, come il servizio di scalabilità automatica e il Service Broker di Azure. Per il momento, scegliere lo spazio *sistema*.


## <a name="create-an-org-and-space"></a>Creare un'organizzazione e uno spazio

Se si digita `cf apps`, viene visualizzato un set di applicazioni di sistema che sono state distribuite nello spazio di sistema all'interno dell'organizzazione sistema. 

È consigliabile mantenere l'organizzazione *sistema* riservata per le applicazioni di sistema, pertanto creare un'organizzazione e uno spazio per ospitare l'applicazione di esempio.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Usare il comando di destinazione per passare alla nuova organizzazione e al nuovo spazio:

```bash
cf target -o testorg -s dev
```

A questo punto, quando si distribuisce un'applicazione essa viene automaticamente creata nella nuova organizzazione e nel nuovo spazio. Per confermare che non sono attualmente presenti app nella nuova organizzazione/spazio, digitare nuovamente `cf apps`.

> [!NOTE] 
> Per altre informazioni sulle organizzazioni e gli spazi e su come possono essere usati per il controllo degli accessi in base al ruolo, vedere la [documentazione di Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuire un'applicazione

Usiamo un'applicazione Cloud Foundry di esempio chiamata Hello Spring Cloud, che viene scritta in Java ed è basata sul [framework Spring](http://spring.io) e su [Spring Boot](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clonare il repository Hello Spring Cloud

L'applicazione di esempio Hello Spring Cloud è disponibile su GitHub. Clonarla nel proprio ambiente e modificarla nella nuova directory:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Compilare l'applicazione.

Compilare l'app mediante [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Distribuire l'applicazione con cf push

È possibile distribuire la maggior parte delle applicazioni per Cloud Foundry tramite il comando `push`:

```bash
cf push
```

Quando si *effettua il push* di un'applicazione, Cloud Foundry rileva il tipo di applicazione (in questo caso, un'app Java) e identifica le relative dipendenze (in questo caso, il framework Spring). Inserisce quindi in un pacchetto tutti gli elementi necessari per eseguire il codice in un'immagine del contenitore autonoma, denominata *droplet*. Infine, Cloud Foundry pianifica l'applicazione in uno dei computer disponibili nell'ambiente e crea un URL in cui è possibile raggiungerla, disponibile nell'output del comando.

![Output del comando push cf][cf-push-output]

Per visualizzare l'applicazione hello-spring-cloud, aprire l'URL specificato nel browser:

![Interfaccia utente predefinita per Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Per altre informazioni su cosa accade durante `cf push`, vedere [How Applications Are Staged][cf-push-docs] (Come vengono gestite temporaneamente le applicazioni) nella documentazione di Cloud Foundry.

## <a name="view-application-logs"></a>Visualizzare i registri applicazioni

Per visualizzare i registri di un'applicazione in base al nome, è possibile usare l'interfaccia della riga di comando di Cloud Foundry:

```bash
cf logs hello-spring-cloud
```

Per impostazione predefinita, il comando registri usa *tail*, che visualizza i nuovi registri man mano che vengono scritti. Per visualizzare i nuovi registri, aggiornare l'applicazione hello-spring-cloud nel browser.

Per visualizzare i log che sono già stati scritti, aggiungere l'istruzione `recent`:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Scalare l'applicazione

Per impostazione predefinita, `cf push` crea solo una singola istanza dell'applicazione. Per garantire la disponibilità elevata e consentire la scalabilità orizzontale per una velocità effettiva superiore, in genere è consigliabile eseguire più istanze delle applicazioni. È possibile scalare orizzontalmente le applicazioni già distribuite tramite il comando `scale`:

```bash
cf scale -i 2 hello-spring-cloud
```

L'esecuzione del comando `cf app` nell'applicazione indica che Cloud Foundry sta creando un'altra istanza dell'applicazione. Dopo che l'applicazione è stata avviata, Cloud Foundry avvia automaticamente il bilanciamento del carico del traffico.


## <a name="next-steps"></a>Passaggi successivi

- [Leggere la documentazione di Cloud Foundry][cloudfoundry-docs]
- [Configurare il plug-in Visual Studio Team Services per Cloud Foundry][vsts-plugin]
- [Configurare il nozzle di Microsoft Log Analytics per Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
