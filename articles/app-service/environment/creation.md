---
title: Creare una ambiente del servizio app
description: Informazioni su come creare un ambiente del servizio app.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 12c9fd43933f9f54e013752c5fe482ce31650536
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238395"
---
# <a name="create-an-app-service-environment"></a>Creare una ambiente del servizio app

> [!NOTE]
> Questo articolo riguarda il ambiente del servizio app V3 (anteprima)
> 

Il [ambiente del servizio app (ASE)][Intro] è una distribuzione a tenant singolo del servizio app che inserisce nella rete virtuale di Azure (VNet).  ASEv3 supporta solo l'esposizione di app in un indirizzo privato in vnet. Quando viene creato un ASEv3 durante la fase di anteprima, vengono aggiunte tre risorse alla sottoscrizione.

- Ambiente del servizio app
- Zona privata di DNS di Azure
- Endpoint privato

Una distribuzione di un ambiente del servizio app richiede l'uso di due subnet.  Una subnet conterrà l'endpoint privato.  Questa subnet può essere usata per altri elementi, ad esempio le macchine virtuali.  L'altra subnet viene usata per le chiamate in uscita dall'ambiente del servizio app.  Questa subnet non può essere usata per altri scopi diversi dall'ambiente del servizio app. 

## <a name="before-you-create-your-ase"></a>Prima di creare l'ambiente del servizio app

Dopo la creazione dell'ambiente del servizio app, non è possibile modificare:

- Località
- Sottoscrizione
- Resource group
- Rete virtuale di Azure (VNet) usata
- Subnet utilizzate
- Dimensioni della subnet
- Nome dell'ambiente del servizio app

La subnet in uscita deve essere sufficientemente grande da contenere le dimensioni massime consentite per la scalabilità dell'ambiente del servizio app. Selezionare una subnet sufficientemente grande per supportare le esigenze di scalabilità massima poiché non può essere modificata dopo la creazione. La dimensione consigliata è a/24 con indirizzi 256.

Dopo aver creato l'ambiente del servizio app, è possibile aggiungervi app. Quando il ASEv3 non dispone di piani di servizio app, è previsto un addebito come se si disponesse di un'istanza di un piano di servizio app I1v2 nell'ambiente del servizio app.  

ASEv3 è disponibile solo in aree selezionate. Verranno aggiunte altre aree quando l'anteprima viene spostata verso GA. 

## <a name="creating-an-ase-in-the-portal"></a>Creazione di un ambiente del servizio app nel portale

1. Per creare un ASEv3, cercare **ambiente del servizio app (anteprima)** nel Marketplace.  
2. Nozioni di base: selezionare la sottoscrizione, selezionare o creare il gruppo di risorse e immettere il nome dell'ambiente del servizio app.  Il nome dell'ambiente del servizio app verrà usato anche per il suffisso del dominio dell'ambiente del servizio app.  Se il nome dell'ambiente del servizio app è *Contoso* , il suffisso di dominio sarà *contoso.appserviceenvironment.NET*.  Questo nome verrà impostato automaticamente nella zona privata di DNS di Azure usata dal VNET in cui è distribuito l'ambiente del servizio app. 

    ![ambiente del servizio app scheda Crea nozioni di base](./media/creation/creation-basics.png)

3. Hosting: selezionare preferenza sistema operativo, distribuzione gruppo host. La preferenza del sistema operativo indica il sistema operativo che verrà usato inizialmente per le app in questo ambiente del servizio app. Dopo la creazione dell'ambiente del servizio app, è possibile aggiungere app dell'altro sistema operativo. Per selezionare l'hardware dedicato viene utilizzata la distribuzione del gruppo host. Con ASEv3 è possibile selezionare abilitato e quindi atterrare su hardware dedicato. Viene addebitato l'intero host dedicato con la creazione dell'ambiente del servizio app e quindi un prezzo ridotto per le istanze del piano di servizio app. 

    ![Selezioni host ambiente del servizio app](./media/creation/creation-hosting.png)

4. Rete: selezionare o creare la rete virtuale, selezionare o creare la subnet in ingresso, selezionare o creare la subnet in uscita. Qualsiasi subnet utilizzata per l'uscita deve essere vuota e delegata a Microsoft. Web/hostingEnvironments. Se si crea la subnet tramite il portale, la subnet verrà automaticamente delegata.

    ![Selezioni della rete ambiente del servizio app](./media/creation/creation-networking.png)

5. Esaminare e creare: verificare che la configurazione sia corretta e selezionare Crea. La creazione dell'ambiente del servizio app richiede circa un'ora. 

    ![ambiente del servizio app rivedere e creare](./media/creation/creation-review.png)

Al termine della creazione dell'ambiente del servizio app, è possibile selezionarlo come percorso durante la creazione delle app. Per altre informazioni sulla creazione di app nel nuovo ambiente del servizio app, vedere [uso di un ambiente del servizio app][UsingASE]

## <a name="os-preference"></a>Preferenza sistema operativo
In un ambiente del servizio app è possibile avere app di Windows, app Linux o entrambe. In ASEv2, la preferenza iniziale selezionata durante la creazione aggiunge l'infrastruttura a disponibilità elevata per quel sistema operativo durante la creazione dell'ambiente del servizio app. Per aggiungere app dell'altro sistema operativo, è sufficiente impostare le app come di consueto e selezionare il sistema operativo desiderato. In ASEv3, questo non influirà sul comportamento del back-end apprezzamento.  

## <a name="dedicated-hosts"></a>Host dedicati
L'ambiente del servizio app viene in genere distribuito in macchine virtuali di cui viene eseguito il provisioning in un hypervisor multi-tenant. Se è necessario eseguire la distribuzione in sistemi dedicati, incluso l'hardware, è possibile effettuare il provisioning dell'ambiente del servizio app in host dedicati. Nell'anteprima ASEv3 iniziale gli host dedicati sono inclusi in una coppia. Ogni host dedicato si trova in una zona di disponibilità separata, se l'area lo consente. Le distribuzioni dell'ambiente del servizio app basate su host dedicate hanno un prezzo diverso rispetto al normale. Sono previsti addebiti per l'host dedicato e un altro addebito per ogni istanza del piano di servizio app.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
