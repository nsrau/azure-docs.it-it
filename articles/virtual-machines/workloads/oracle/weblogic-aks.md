---
title: Che cosa sono le soluzioni per l'esecuzione di Oracle WebLogic Server nel servizio Azure Kubernetes
description: Informazioni su come eseguire Oracle WebLogic Server nel servizio Azure Kubernetes.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: a57d5a00fad9a17e01b96ebdf395fb7a4b857935
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968690"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Che cosa sono le soluzioni per l'esecuzione di Oracle WebLogic Server nel servizio Azure Kubernetes?

Questa pagina descrive le soluzioni per l'esecuzione di Oracle WebLogic Server (WLS) nel servizio Azure Kubernetes (AKS). Queste soluzioni sono sviluppate e supportate congiuntamente da Oracle e Microsoft.

È anche possibile eseguire WebLogic Server in macchine virtuali di Azure. Le soluzioni da eseguire sono descritte in [questo articolo Microsoft](/azure/virtual-machines/workloads/oracle/oracle-weblogic).

WebLogic Server è un server applicazioni Java leader che esegue alcune delle applicazioni Java aziendali più cruciali in tutto il mondo. WebLogic Server costituisce la base middleware per Oracle software suite. Oracle e Microsoft si impegnano a offrire ai clienti di WebLogic Server la scelta e la flessibilità per l'esecuzione di carichi di lavoro in Azure come piattaforma cloud leader.

## <a name="wls-on-aks-certified-and-supported"></a>WLS su AKS Certified e supported
WebLogic Server è certificata da Oracle e Microsoft per essere eseguita correttamente su AKS. Il server WebLogic sulle soluzioni AKS mira a semplificare il più possibile l'esecuzione delle applicazioni Java EE in contenitori e orchestrate in Docker e nell'infrastruttura Kubernetes. Le soluzioni sono incentrate sull'affidabilità, sulla scalabilità, sulla gestibilità e sul supporto aziendale.

I cluster WebLogic Server sono completamente abilitati per l'esecuzione in Kubernetes tramite l'operatore WebLogic Kubernetes (denominato semplicemente "operator" da qui in poi). L'operatore segue il modello di operatore Kubernetes standard. Semplifica la gestione e il funzionamento dei domini WebLogic e delle distribuzioni su Kubernetes automatizzando le attività manuali altrimenti e aggiungendo altre funzionalità di affidabilità operativa. L'operatore supporta Oracle WebLogic Server 12c, Oracle Fusion Middleware Infrastructure 12C e versioni successive. Sono state testate le immagini Docker ufficiali per WebLogic Server 12.2.1.3 e 12.2.1.4 con l'operatore. Per informazioni dettagliate sull'operatore, vedere la [documentazione ufficiale di Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Linee guida, script ed esempi per WLS su AKS
Oltre a certificare WebLogic Server su AKS, Oracle e Microsoft forniscono congiuntamente istruzioni dettagliate, script ed esempi per l'esecuzione di WebLogic Server su AKS. Le linee guida sono incluse nella sezione relativa all'esempio di servizio Azure Kubernetes della [documentazione dell'operatore](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Il materiale sussidiario mira a rendere più semplice possibile la produzione WebLogic Server nelle distribuzioni AKS. Il materiale sussidiario usa le immagini Docker ufficiali WebLogic Server fornite da Oracle. Il failover viene raggiunto tramite File di Azure a cui si accede tramite attestazioni del volume permanenti Kubernetes. I servizi di bilanciamento del carico di Azure sono supportati quando ne viene eseguito il provisioning tramite un servizio Kubernetes di tipo "LoadBalancer". Le linee guida consentono un elevato livello di configurazione e personalizzazione.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="È possibile usare gli script di esempio per distribuire WebLogic Server in AKS":::

Il materiale sussidiario attualmente presuppone la distribuzione del dominio all'esterno dell'immagine Docker e l'uso delle immagini Docker standard da Oracle. Verranno aggiunte linee guida per l'abilitazione di immagini personalizzate con il dominio all'interno di un'immagine docker. Altre soluzioni di facile utilizzo e integrazione dei servizi di Azure sono possibili in futuro tramite le offerte di Marketplace mirroring Oracle WebLogic Server in macchine virtuali di Azure.

_Queste soluzioni sono Bring your own License_. Si presuppone che l'utente abbia già ottenuto le licenze appropriate con Oracle e che disponga di una licenza corretta per l'esecuzione di offerte in Azure.

_Se si è interessati a lavorare a stretto contatto con gli scenari di migrazione con il team di progettazione che sviluppa queste soluzioni, compilare [questo breve sondaggio](https://aka.ms/wls-on-azure-survey) e includere le informazioni di contatto_. I responsabili del programma, gli architetti e i tecnici ti contatteranno a breve e inizieranno a collaborare in stretta collaborazione. La possibilità di collaborare in uno scenario di migrazione è gratuita mentre le soluzioni sono in fase di sviluppo iniziale attivo.

## <a name="deployment-architectures"></a>Architetture di distribuzione

Le soluzioni per l'esecuzione di Oracle WebLogic Server nel servizio Azure Kubernetes consentiranno un'ampia gamma di architetture di distribuzione pronte per la produzione con la relativa facilità.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Le distribuzioni di WebLogic Server complesse sono abilitate su AKS":::

Oltre alle soluzioni che offrono ai clienti una flessibilità completa per personalizzare ulteriormente le distribuzioni. È probabile che, in base alla distribuzione di applicazioni, i clienti integrino altre risorse di Azure con le relative distribuzioni. I clienti sono invitati a fornire commenti e suggerimenti nel sondaggio per migliorare ulteriormente le soluzioni.

## <a name="next-steps"></a>Passaggi successivi

Esplorare l'esecuzione di Oracle WebLogic Server nel servizio Azure Kubernetes.

> [!div class="nextstepaction"]
> [Linee guida, script ed esempi per l'esecuzione di WLS su AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [Operatore WebLogic Kubernetes](https://oracle.github.io/weblogic-kubernetes-operator/)
