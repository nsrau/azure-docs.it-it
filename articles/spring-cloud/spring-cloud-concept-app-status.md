---
title: Informazioni sullo stato delle app in Azure Spring Cloud
description: Informazioni sulle categorie di stato delle app nel cloud Spring di Azure
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ec87c5523d6f608363ca615037f20365705609df
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892857"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Informazioni sullo stato delle app in Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C #

L'interfaccia utente di Azure Spring cloud fornisce informazioni sullo stato delle applicazioni in esecuzione.  È disponibile un'opzione **app** per ogni gruppo di risorse in una sottoscrizione che visualizza lo stato generale dei tipi di applicazione.  Per ogni tipo di applicazione, vengono visualizzate le **istanze dell'applicazione**.

## <a name="apps-status"></a>Stato delle app
Per visualizzare lo stato generale di un tipo di applicazione, selezionare **app** nel riquadro di spostamento a sinistra di un gruppo di risorse. Il risultato Visualizza lo stato dell'app distribuita:

* **Stato del provisioning** Mostra lo stato di provisioning della distribuzione
* L' **istanza in esecuzione** Mostra il numero di istanze dell'app in esecuzione/il numero di istanze dell'app desiderate. Se l'app deve essere arrestata, in questa colonna viene visualizzato *arrestato*.
* L' **istanza registrata** Mostra il numero di istanze dell'app registrate in Eureka/il numero di istanze dell'app desiderate. Se l'app deve essere arrestata, in questa colonna viene visualizzato *arrestato*.


 ![Stato delle app](media/spring-cloud-concept-app-status/apps-ui-status.png)

**Lo stato della distribuzione viene segnalato come uno dei valori seguenti:**

| Enumerazione | Definizione |
|:--:|:----------------:|
| In esecuzione | La distribuzione dovrebbe essere in esecuzione. |
| Arrestato | La distribuzione deve essere arrestata. |

**Lo stato di provisioning è accessibile solo dall'interfaccia della riga di comando.  Viene segnalato come uno dei valori seguenti:**

| Enumerazione | Definizione |
|:--:|:----------------:|
| Creating | La risorsa sta creando. |
| Aggiornamento | Aggiornamento della risorsa in corso. |
| Operazione riuscita | Le risorse sono state fornite e la distribuzione del file binario è stata completata. |
| Operazione non riuscita | Non è stato possibile raggiungere l'obiettivo *riuscito* . |
| Deleting | È in corso l'eliminazione della risorsa. Questa operazione impedisce l'operazione e la risorsa non è disponibile in questo stato. |

## <a name="app-instances-status"></a>Stato delle istanze dell'app

Per visualizzare lo stato di un'istanza specifica di un'app distribuita, fare clic sul **nome** dell'app nell' **interfaccia utente di app.** I risultati vengono visualizzati:
* **Stato**: indica se l'istanza è in esecuzione o il suo stato
* **DiscoveryStatus**: stato registrato dell'istanza dell'app nel server Eureka

 ![Stato delle istanze dell'app](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**Lo stato dell'istanza viene segnalato come uno dei valori seguenti:**

| Enumerazione | Definizione |
|:--:|:----------------:|
| Avvio in corso | Il file binario è stato distribuito correttamente nell'istanza specificata. L'avvio dell'istanza del file jar potrebbe non riuscire perché non è possibile eseguire correttamente il file jar. |
| In esecuzione | L'istanza funziona. |
| Operazione non riuscita | L'istanza dell'app non è riuscita a avviare il file binario dell'utente dopo diversi tentativi. |
| Terminazione | L'istanza dell'app verrà arrestata. |

**Lo stato di individuazione dell'istanza viene segnalato come uno dei valori seguenti:**

| Enumerazione | Definizione |
|:--:|:----------------:|
| UP | L'istanza dell'app è registrata in Eureka ed è pronta per ricevere traffico |
| OUT_OF_SERVICE | L'istanza dell'app è registrata in Eureka e in grado di ricevere traffico. ma viene arrestato intenzionalmente per il traffico. |
| DOWN | L'istanza dell'app non è registrata in Eureka o è registrata ma non può ricevere traffico. |


## <a name="see-also"></a>Vedere anche
* [Preparare un'applicazione Spring o Steeltoe per la distribuzione nel cloud Spring di Azure](spring-cloud-tutorial-prepare-app-deployment.md)