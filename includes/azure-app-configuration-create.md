---
title: File di inclusione
description: File di inclusione
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133050"
---
1. Per creare un nuovo archivio di configurazione app, accedere al [portale di Azure](https://aka.ms/azconfig/portal). Nell'angolo superiore sinistro della pagina selezionare **+ Crea una risorsa**. Nella casella **Cerca nel Marketplace** immettere **Configurazione app** e quindi premere INVIO.

    ![Ricerca di Configurazione app](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Nei risultati della ricerca selezionare **Configurazione app** e quindi selezionare **Crea**.

3. Nella pagina **Configurazione app** > **Crea** immettere le impostazioni seguenti.

    | Impostazione | Valore consigliato | DESCRIZIONE |
    |---|---|---|
    | **Nome risorsa** | Nome globalmente univoco | Immettere un nome di risorsa univoco da usare per la risorsa archivio di configurazione app. Il nome deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il carattere `-`. Il nome non può iniziare o terminare con il carattere `-` e i caratteri `-` consecutivi non sono validi.  |
    | **Sottoscrizione** | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per testare Configurazione app. Se l'account ha una sola sottoscrizione, questa viene selezionata automaticamente e l'elenco a discesa **Sottoscrizione** non viene visualizzato. |
    | **Gruppo di risorse** | *AppConfigTestResources* | Selezionare o creare un gruppo di risorse per la risorsa archivio di configurazione app. Questo gruppo è utile per gestire risorse multiple: ad esempio è possibile eliminare più risorse con un'unica operazione, eliminando il gruppo di risorse. Per altre informazioni, vedere [Usare i gruppi di risorse per gestire le risorse di Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Posizione** | *Stati Uniti centrali* | Usare **Posizione** per specificare la posizione geografica in cui viene ospitato l'archivio di configurazione app. Per ottenere prestazioni ottimali, creare le risorse nella stessa area degli altri componenti dell'applicazione. |

    ![Creare una risorsa archivio di configurazione app](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Selezionare **Create**. Il completamento della distribuzione può richiedere alcuni minuti.

5. Al termine della distribuzione, selezionare **Impostazioni** > **Chiavi di accesso**. Prendere nota della stringa di connessione della chiave primaria di sola lettura o di quella di sola scrittura. Questa stringa di connessione verrà usata in seguito per configurare l'applicazione in modo da comunicare con l'archivio di configurazione app creato.
