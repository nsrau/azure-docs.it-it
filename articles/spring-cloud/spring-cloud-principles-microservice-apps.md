---
title: Java e sistema operativo di base per le app di microservizi cloud di Azure Spring
description: Principi per la manutenzione del sistema operativo Java e di base integro per le app di microservizi cloud di Azure Spring
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 1a8f48ff365ffae4a7013d2bfd537721ba2bc381
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172695"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java e sistema operativo di base per le app di microservizio Spring
Di seguito sono riportati i principi per gestire il sistema operativo Java e di base integro per le app di microservizi Spring.
## <a name="principles-for-healthy-java-and-base-os"></a>Principi per il sistema operativo Java e di base integro
* Deve essere lo stesso sistema operativo di base tra i livelli-Basic | Standard | Premium.
    * Attualmente, le app nel cloud Spring di Azure usano una combinazione di Debian 10 e Ubuntu 18,04.
    * Il servizio di compilazione VMware usa Ubuntu 18,04.
* Deve essere lo stesso sistema operativo di base indipendentemente dai punti di partenza della distribuzione-source | JAR
    * Attualmente, le app nel cloud Spring di Azure usano una combinazione di Debian 10 e Ubuntu 18,04.
* Il sistema operativo di base deve essere privo di vulnerabilità di sicurezza.
    * Il sistema operativo di base Debian 10 ha 147 CVEs aperti.
    * Il sistema operativo di base Ubuntu 18,04 dispone di 132 CVEs aperti.
* Deve usare JRE-Head.
    * Attualmente, le app nel cloud Spring di Azure usano JDK. JRE: è un'immagine di dimensioni ridotte.
* Utilizzerà le build più recenti di Java.
    * Attualmente, le app nel cloud Spring di Azure usano Java 8 Build 242. Si tratta di una compilazione obsoleta.
 
Azul Systems analizzerà continuamente le modifiche apportate ai sistemi operativi di base e manterrà aggiornate le ultime immagini compilate. Azure Spring cloud Cerca le modifiche alle immagini e le aggiorna continuamente tra le distribuzioni.
 
## <a name="faq-for-azure-spring-cloud"></a>Domande frequenti su Azure Spring Cloud

* Quali versioni di Java sono supportate? Versione principale e numero di Build.
    * Supportare le versioni LTS: Java 8 e 11.
    * Usa la build più recente, ad esempio adesso, Java 8 Build 252 e Java 11 Build 7.
* Chi ha creato questi runtime Java?
    * Sistemi Azul.
* Qual è il sistema operativo di base per le immagini?
    * Ubuntu 20,04 LTS (fossa focale). Le app continueranno a rimanere nella versione LTS più recente di Ubuntu.
    * Vedere [Ubuntu 20,04 LTS (fossa focale)](http://releases.ubuntu.com/focal/)
* Come è possibile scaricare un runtime Java supportato per lo sviluppo locale? 
    * Vedere [Install the JDK for Azure and Azure stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Come è possibile ottenere supporto per i problemi a livello di runtime Java?
    * Aprire un ticket di supporto con il supporto tecnico di Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Distribuzione predefinita nel cloud Spring di Azure

> ![Distribuzione predefinita](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Passaggi successivi
* [Guida introduttiva: avviare un'applicazione Azure Spring cloud esistente usando il portale di Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Supporto a lungo termine Java per Azure e Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
