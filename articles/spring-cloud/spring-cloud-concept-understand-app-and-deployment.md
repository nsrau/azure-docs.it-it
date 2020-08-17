---
title: Informazioni sull'app e la distribuzione nel cloud Spring di Azure
description: Distinzione tra l'applicazione e la distribuzione nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263974"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Informazioni sull'app e la distribuzione nel cloud Spring di Azure

**App** e **distribuzione** sono i due concetti chiave del modello di risorse di Azure Spring cloud. Nel cloud Spring di Azure un' *app* è un'astrazione di un'app aziendale o di un microservizio.  Una versione di codice o binario distribuita quando l' *app* viene eseguita in una *distribuzione*.

 ![App e distribuzioni](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

Il livello standard di Azure Spring cloud consente a un'app di disporre di una distribuzione di produzione e di una distribuzione di gestione temporanea, in modo che sia possibile eseguire facilmente la distribuzione Blue/Green.

## <a name="app"></a>App
Le seguenti funzionalità/proprietà sono definite a livello di app.

| Enum | Definizione |
|:--|:----------------|
| Pubblico</br>Endpoint | URL per accedere all'app |
| Personalizzato</br>Dominio | Record CNAME che protegge il dominio personalizzato |
| Servizio</br>Binding | Binding delle proprietà di configurazione impostate nell'function.jssu file e l'attributo *ServiceBusTrigger* |
| Gestiti</br>Identità | L'identità gestita da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure AD, ad esempio Azure Key Vault |
| Persistente</br>Archiviazione: | Impostazione che consente la permanenza dei dati oltre il riavvio dell'app |

## <a name="deployment"></a>Distribuzione

Le seguenti funzionalità/proprietà sono definite a livello di distribuzione e verranno scambiate durante lo scambio della distribuzione di produzione/gestione temporanea.

| Enum | Definizione |
|:--|:----------------|
| CPU | Numero di Vcore per ogni istanza dell'app |
| Memoria | Impostazione che alloca memoria per la scalabilità verticale o orizzontale di distribuzioni |
| Istanza</br>Conteggio | Il numero di istanze dell'app, impostate manualmente o automaticamente |
| Ridimensionamento automatico | Ridimensionare automaticamente il numero di istanze in base a regole e pianificazioni predefinite |
| JVM</br>Opzioni | impostazione: JAVA_OPTS |
| Ambiente</br>variables | Impostazioni applicabili all'intero ambiente cloud Spring di Azure |
| Runtime</br>Versione | Java 8/Java 11|

## <a name="restrictions"></a>Restrizioni

* **Un'app deve avere una distribuzione di produzione**: l'eliminazione di una distribuzione di produzione è bloccata dall'API. Deve essere scambiata in staging prima dell'eliminazione.
* **Un'app può avere al massimo due distribuzioni**: la creazione di più di due distribuzioni è bloccata dall'API. Distribuire il nuovo file binario in una distribuzione di produzione o di gestione temporanea esistente.
* **La gestione della distribuzione non è disponibile nel piano Basic**: usare il livello standard per la funzionalità di distribuzione blu-verde.

## <a name="see-also"></a>Vedere anche
* [Configurare un ambiente di staging nel cloud Spring di Azure](spring-cloud-howto-staging-environment.md)