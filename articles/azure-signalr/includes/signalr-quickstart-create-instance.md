---
title: File di inclusione
description: File di inclusione
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: ac77f0b6a1d90fd78db64618c1e03ade198a67c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882128"
---
## <a name="create-an-azure-signalr-service-instance"></a>Creare un'istanza del servizio Azure SignalR

L'applicazione si connette a un'istanza del servizio SignalR in Azure.

1. Selezionare il pulsante Nuovo nell'angolo superiore sinistro del portale di Azure. Nella nuova schermata digitare *Servizio SignalR* nella casella di ricerca e premere Invio.

    ![Cercare il servizio SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Selezionare **Servizio SignalR** dai risultati della ricerca, quindi selezionare **Crea**.

1. Immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | DESCRIZIONE                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome risorsa** | Nome globalmente univoco | Nome che identifica la nuova istanza del servizio SignalR. I caratteri validi sono `a-z`, `0-9` e `-`.  | 
    | **Sottoscrizione** | Sottoscrizione in uso | La sottoscrizione in cui verrà creata questa nuova istanza del servizio SignalR. | 
    | **[Gruppo di risorse](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui creare l'istanza del servizio SignalR. | 
    | **Posizione** | Stati Uniti occidentali | Scegliere [un'area](https://azure.microsoft.com/regions/) nelle vicinanze. |
    | **Piano tariffario** | Gratuito | Provare il servizio Azure SignalR gratuitamente. |
    | **Unità: conteggio** |  Non applicabile | Numero di unità specifica il numero di connessioni che può accettare l'istanza del servizio SignalR. È configurabili solo nel livello Standard. |
    | **Modalità di servizio** |  Senza server | Per l'uso con Funzioni di Azure o l'API REST. |

    ![Creare il servizio SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Selezionare **Crea** per iniziare la distribuzione dell'istanza del servizio SignalR.

1. Dopo che l'istanza è stata distribuita, aprirla nel portale e individuare la relativa pagina delle impostazioni. Modificare l'impostazione della modalità di servizio in *Serverless* solo se si usa il servizio Azure SignalR tramite l'associazione di Funzioni di Azure o l'API REST. In caso contrario, mantenere il valore *Classica* o *Predefinita*.