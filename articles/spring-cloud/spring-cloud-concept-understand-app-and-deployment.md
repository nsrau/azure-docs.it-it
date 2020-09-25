---
title: Informazioni sull'app e la distribuzione nel cloud Spring di Azure
description: Distinzione tra l'applicazione e la distribuzione nel cloud Spring di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: c053a7830e02eb7c460bd030ca3c6a10c00ea78a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323638"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Informazioni sull'app e la distribuzione nel cloud Spring di Azure

**Questo articolo si applica a:** ✔️ Java ✔️ C#

**App** e **distribuzione** sono i due concetti chiave del modello di risorse di Azure Spring cloud. Nel cloud Spring di Azure un' *app* è un'astrazione di un'app aziendale o di un microservizio.  Una versione di codice o binario distribuita quando l' *app* viene eseguita in una *distribuzione*.  Le app vengono eseguite in un' *istanza del servizio cloud di Azure Spring*o semplicemente in un' *istanza del servizio*, come illustrato di seguito.

 ![App e distribuzioni](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

È possibile avere più istanze del servizio all'interno di una singola sottoscrizione di Azure, ma il servizio cloud Spring è più semplice da usare quando tutte le app che costituiscono un'app aziendale o un microservizio risiedono all'interno di una singola istanza del servizio.

Il livello standard di Azure Spring cloud consente a un'app di disporre di una distribuzione di produzione e di una distribuzione di gestione temporanea, in modo che sia possibile eseguire facilmente la distribuzione Blue/Green.

## <a name="app"></a>App
Le seguenti funzionalità/proprietà sono definite a livello di app.

| Funzionalità | Descrizione |
|:--|:----------------|
| Pubblico</br>Endpoint | URL per accedere all'app |
| Personalizzato</br>Dominio | Record CNAME che protegge il dominio personalizzato |
| Servizio</br>Binding | Connessione predefinita con altri servizi di Azure |
| Gestiti</br>Identità | L'identità gestita da Azure Active Directory consente all'app di accedere facilmente ad altre risorse protette da Azure AD, ad esempio Azure Key Vault |
| Persistente</br>Archiviazione | Impostazione che consente la permanenza dei dati oltre il riavvio dell'app |

## <a name="deployment"></a>Distribuzione

Le seguenti funzionalità/proprietà sono definite a livello di distribuzione e verranno scambiate durante lo scambio della distribuzione di produzione/gestione temporanea.

| Funzionalità | Descrizione |
|:--|:----------------|
| CPU | Numero di Vcore per ogni istanza dell'app |
| Memoria | GB di memoria per ogni istanza dell'app|
| Istanza</br>Conteggio | Il numero di istanze dell'app, impostate manualmente o automaticamente |
| Ridimensionamento automatico | Ridimensionare automaticamente il numero di istanze in base a regole e pianificazioni predefinite |
| JVM</br>Opzioni | Impostazione delle opzioni JVM  |
| Environment</br>Variabili | Impostazioni delle variabili di ambiente |
| Runtime</br>Versione | Java 8/Java 11|

## <a name="restrictions"></a>Restrizioni

* **Un'app deve avere una distribuzione di produzione**: l'eliminazione di una distribuzione di produzione è bloccata dall'API. Deve essere scambiata in staging prima dell'eliminazione.
* **Un'app può avere al massimo due distribuzioni**: la creazione di più di due distribuzioni è bloccata dall'API. Distribuire il nuovo file binario in una distribuzione di produzione o di gestione temporanea esistente.
* **La gestione della distribuzione non è disponibile nel piano Basic**: usare il livello standard per la funzionalità di distribuzione blu-verde.

## <a name="see-also"></a>Vedere anche
* [Configurare un ambiente di staging nel cloud Spring di Azure](spring-cloud-howto-staging-environment.md)
