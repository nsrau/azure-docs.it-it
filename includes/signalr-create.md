---
title: File di inclusione
description: File di inclusione
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76021279"
---
1. Per creare una risorsa del Servizio Azure SignalR, prima di tutto accedere al [portale di Azure](https://portal.azure.com). Nell'angolo in alto a sinistra della pagina selezionare **+ Crea una risorsa**. Nella casella di testo **Cerca nel marketplace** immettere **Servizio SignalR**.

2. Nei risultati fare clic su **Servizio SignalR** e quindi su **Crea**.

3. Nella nuova pagina delle impostazioni **SignalR** aggiungere le impostazioni seguenti per la nuova risorsa SignalR:

    | Nome | Valore consigliato | Descrizione |
    | ---- | ----------------- | ----------- |
    | Nome risorsa | *testsignalr* | Immettere un nome risorsa univoco da usare per la risorsa SignalR. Il nome deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il segno meno (`-`). Il nome non può iniziare o terminare con il segno meno e non sono consentiti più segni meno consecutivi.|
    | Subscription | Scegliere la sottoscrizione |  Selezionare la sottoscrizione di Azure da usare per testare SignalR. Se l'account ha una sola sottoscrizione, questa viene selezionata automaticamente e l'elenco a discesa **Sottoscrizione** non viene visualizzato.|
    | Resource group | Creare un gruppo di risorse denominato *SignalRTestResources*| Selezionare o creare un gruppo di risorse per la risorsa SignalR. Questo gruppo è utile per gestire risorse multiple: ad esempio è possibile eliminare più risorse con un'unica operazione, eliminando il gruppo di risorse. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](../articles/azure-resource-manager/management/overview.md). |
    | Location | *Stati Uniti orientali* | Usare **Posizione** per specificare la posizione geografica in cui viene ospitata la risorsa SignalR. Per ottenere prestazioni ottimali, è consigliabile creare le risorse nella stessa area degli altri componenti dell'applicazione. |
    | Piano tariffario | *Free* | Attualmente sono disponibili due opzioni: **Gratuito** e **Standard**. |
    | Aggiungi al dashboard | ✔ | Selezionare questa casella per aggiungere la risorsa al dashboard e renderla più facile da trovare. |

4. Selezionare **Create** (Crea). Il completamento della distribuzione può richiedere alcuni minuti.

5. Dopo aver completato la distribuzione, in **IMPOSTAZIONI** selezionare **Chiavi**. Copiare la stringa di connessione per la chiave primaria. La stringa sarà utile in un secondo momento per configurare l'app per usare la risorsa del Servizio Azure SignalR.

    La stringa di connessione deve avere il formato seguente:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
