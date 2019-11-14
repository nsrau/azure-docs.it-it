---
title: Distribuire la prima app a Cloud Foundry in Microsoft Azure
description: Distribuire un'applicazione a Cloud Foundry in Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b1f9ab5289a41aacb5514e954f1ca01f6ad66152
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036835"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Distribuire la prima app a Cloud Foundry in Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) è una popolare piattaforma di applicazioni open source disponibile in Microsoft Azure. In questo articolo viene illustrato come distribuire e gestire un'applicazione in Cloud Foundry in un ambiente Azure.

## <a name="create-a-cloud-foundry-environment"></a>Creare un ambiente Cloud Foundry

Esistono diverse opzioni per la creazione di un ambiente Cloud Foundry in Azure:

- Usare l' [offerta pivotal Cloud Foundry][pcf-azuremarketplace] in Azure Marketplace per creare un ambiente standard che includa gestione Ops di PCF e la Service Broker di Azure. È possibile trovare [istruzioni complete][pcf-azuremarketplace-pivotaldocs] per la distribuzione dell'offerta Marketplace nella documentazione di Pivotal.
- Creare un ambiente personalizzato [distribuendo Cloud Foundry pivotal manualmente][pcf-custom].
- [Distribuire i pacchetti di Cloud Foundry open source direttamente][oss-cf-bosh] impostando un Director [Bosh](https://bosh.io) , una macchina virtuale che coordina la distribuzione dell'ambiente Cloud Foundry.

> [!IMPORTANT] 
> Se si distribuisce PCF da Azure Marketplace, annotare il SYSTEMDOMAINURL e le credenziali amministratore necessarie per accedere al gestore di app di Pivotal, entrambi descritti nella Guida alla distribuzione dal marketplace. Questi elementi sono necessari per completare questa esercitazione. Per le distribuzioni dal marketplace, il SYSTEMDOMAINURL è nel formato https://system.*ip-address*.cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Connettersi al controller del cloud

Il controller del cloud è il punto di ingresso principale in un ambiente Cloud Foundry per la distribuzione e la gestione delle applicazioni. L'API del controller del cloud di base (CCAPI) è un'API REST, ma è accessibile attraverso vari strumenti. In questo caso si interagisce con l'interfaccia della riga di comando [Cloud Foundry][cf-cli]. È possibile installare l'interfaccia della riga di comando in Linux, MacOS o Windows, ma se si preferisce non installarla, è disponibile preinstallato nel [Azure cloud Shell][cloudshell-docs].

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
> Per ulteriori informazioni su org e sugli spazi e su come utilizzarli per il controllo degli accessi in base al ruolo (RBAC), vedere la [documentazione Cloud Foundry][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Distribuire un'applicazione

Usiamo un'applicazione Cloud Foundry di esempio chiamata Hello Spring Cloud, che viene scritta in Java ed è basata sul [framework Spring](https://spring.io) e su [Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Clonare il repository Hello Spring Cloud

L'applicazione di esempio Hello Spring Cloud è disponibile su GitHub. Clonarla nel proprio ambiente e modificarla nella nuova directory:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Compilare l'applicazione.

Compilare l'app mediante [Apache Maven](https://maven.apache.org).

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
> Per ulteriori informazioni su ciò che accade durante `cf push`, vedere la pagina relativa alla gestione [temporanea delle applicazioni][cf-push-docs] nella documentazione di Cloud Foundry.

## <a name="view-application-logs"></a>Visualizzare i log applicazioni

Per visualizzare i log di un'applicazione in base al nome, è possibile usare l'interfaccia della riga di comando di Cloud Foundry:

```bash
cf logs hello-spring-cloud
```

Per impostazione predefinita, il comando logs usa *tail*, che visualizza i nuovi log man mano che vengono scritti. Per visualizzare i nuovi log, aggiornare l'applicazione hello-spring-cloud nel browser.

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

- [Leggi la documentazione di Cloud Foundry][cloudfoundry-docs]
- [Configurare il plug-in Azure DevOps Services per Cloud Foundry][vsts-plugin]
- [Configurare Microsoft Log Analytics ugello per Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
