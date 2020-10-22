---
title: 'Esercitazione: Configurare la scalabilità automatica per applicazioni di microservizi'
description: Questo articolo descrive come configurare le impostazioni di scalabilità automatica per le applicazioni usando il portale di Azure o l'interfaccia della riga di comando di Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4effdbf5fb924b59a540ce16e769633c484c641a
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92091067"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>Esercitazione: Configurare la scalabilità automatica per le applicazioni di microservizi

**Questo articolo si applica a:** ✔️ Java ✔️ C#

La scalabilità automatica è una funzionalità predefinita di Azure Spring Cloud che ottimizza le prestazioni delle applicazioni di microservizi quando la domanda cambia. Ciò implica la modifica del numero di CPU virtuali, della memoria e delle istanze dell'app. Questo articolo descrive come configurare le impostazioni di scalabilità automatica per le applicazioni usando il portale di Azure o l'interfaccia della riga di comando di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per seguire queste procedure occorre:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Istanza del servizio Azure Spring Cloud distribuita. Per iniziare, seguire la [guida di avvio rapido sulla distribuzione di un'app tramite l'interfaccia della riga di comando di Azure](./spring-cloud-quickstart.md).
* Almeno un'applicazione già creata nell'istanza del servizio.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Passare alla pagina Scalabilità automatica nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare alla pagina **Panoramica** di Azure Spring Cloud.
3. Selezionare il gruppo di risorse contenente il servizio.
4. Selezionare la scheda **App** in **Impostazioni** nel menu del riquadro di spostamento sinistro.
5. Selezionare l'applicazione per cui configurare la scalabilità automatica. In questo esempio selezionare l'applicazione denominata **demo**. Verrà visualizzata la pagina **Panoramica** dell'applicazione.
6. Selezionare la scheda **Scale Out** in **Impostazioni** nel menu del riquadro di spostamento sinistro.
7. Selezionare la distribuzione per cui configurare la scalabilità automatica. Verranno visualizzate le opzioni per la scalabilità automatica illustrate nella sezione seguente.


![Menu Scalabilità automatica](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Configurare le impostazioni di scalabilità automatica per l'applicazione nel portale di Azure

Sono disponibili due opzioni per la gestione delle richieste di scalabilità automatica:

* Scalabilità manuale: mantiene un numero di istanze fisse. Nel livello Standard è possibile aumentare il numero di istanze fino a un massimo di 500. Questo valore modifica il numero di istanze in esecuzione separate dell'applicazione di microservizi.
* Scalabilità automatica personalizzata: il dimensionamento avviene in base a qualsiasi pianificazione e a qualunque metrica.

Nel portale di Azure scegliere come applicare la scalabilità.  La figura seguente illustra l'opzione **Scalabilità automatica personalizzata** e le impostazioni della modalità.

![Scalabilità automatica personalizzata](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Configurare le impostazioni di scalabilità automatica per l'applicazione nell'interfaccia della riga di comando di Azure
È anche possibile impostare le modalità di scalabilità automatica usando l'interfaccia della riga di comando di Azure.  I comandi seguenti creano un'impostazione e una regola di scalabilità automatica.

* Creare l'impostazione di scalabilità automatica
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Creare la regola di scalabilità automatica
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Eseguire l'aggiornamento al livello Standard

Se il piano corrente è il livello Basic ed è vincolato da uno o più di questi limiti, è possibile eseguire l'aggiornamento al livello Standard. A questo scopo, passare al menu **Piano tariffario** selezionando la colonna *Livello Standard* e facendo clic sul pulsante **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica della scalabilità automatica in Microsoft Azure](../azure-monitor/platform/autoscale-overview.md)
* [Scalabilità automatica con il monitoraggio dell'interfaccia della riga di comando di Azure](/cli/azure/monitor/autoscale?preserve-view=true&view=azure-cli-latest)