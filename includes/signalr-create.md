---
title: file di inclusione
description: file di inclusione
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: c26e2de2c492c51c35527979ef0450ed7c2de499
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100828"
---
1. Per creare una nuova risorsa di servizio Azure SignalR, accedere prima di tutto al [portale di Azure](https://portal.azure.com). Nell'angolo in alto a sinistra della pagina, fare clic su **Crea una risorsa**. Nella casella di testo per la **ricerca nel Marketplace**, digitare **Servizio SignalR**, quindi premere **Invio**.

2. Nei risultati, fare clic su **Servizio SignalR**, quindi su **Crea**.

3. Nella nuova pagina di impostazioni **SignalR** pagina, aggiungere le impostazioni seguenti per la nuova risorsa SignalR:

    | Nome | Valore consigliato | Descrizione |
    | ---- | ----------------- | ----------- |
    | Nome risorsa | *testsignalr* | Immettere un nome risorsa univoco da usare per la risorsa SignalR. Il nome deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il carattere `-`. Il nome non può iniziare o terminare con il carattere `-` e i caratteri `-` consecutivi non sono validi.|
    | Sottoscrizione | Scegliere la sottoscrizione |  Selezionare la sottoscrizione di Azure da usare per testare SignalR. Se l'account ha una sola sottoscrizione, questa viene selezionata automaticamente e l'elenco a discesa **Sottoscrizione** non viene visualizzato.|
    | Gruppo di risorse | Creare un nuovo gruppo di risorse denominato *SignalRTestResources*| Selezionare o creare un gruppo di risorse per la risorsa SignalR. Questo gruppo è utile per gestire risorse multiple: ad esempio è possibile eliminare più risorse con un'unica operazione, eliminando il gruppo di risorse. Per altre informazioni, vedere [Uso di gruppi di risorse per gestire le risorse di Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Località | *Stati Uniti orientali* | Usare **Posizione** per specificare la posizione geografica in cui viene ospitata la risorsa SignalR. Per ottenere prestazioni ottimali, è consigliabile creare le risorse nella stessa area degli altri componenti dell'applicazione. |
    | Piano tariffario | *Gratuito* | Attualmente sono disponibili due opzioni: **Gratuito** e **Standard**. |
    | Aggiungi al dashboard | ✔ | Selezionare questa casella per aggiungere la risorsa al dashboard e renderla più facile da trovare. |

4. Fare clic su **Crea**. Per il completamento della distribuzione possono essere necessari alcuni minuti.

5. Dopo aver completato la distribuzione, in **IMPOSTAZIONI** fare clic su **Chiavi**. Copiare la stringa di connessione chiave primaria. Sarà utile in un secondo momento per configurare l'app per utilizzare la risorsa del servizio Azure SignalR.

    La stringa di connessione deve avere il formato seguente:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
