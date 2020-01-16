---
author: yegu
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.openlocfilehash: ceeeb5ee155624e050f36e733a464c2cb21db88c
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750303"
---
1. Per creare un nuovo archivio di Configurazione app, accedere al [portale di Azure](https://portal.azure.com). Nell'angolo superiore sinistro della home page selezionare **Crea una risorsa**. Nella casella **Cerca nel Marketplace** immettere **Configurazione app** e quindi premere INVIO.

    ![Ricerca di Configurazione app](media/azure-app-configuration-create/azure-portal-search.png)

1. Nei risultati della ricerca selezionare **Configurazione app** e quindi selezionare **Crea**.

    ![Selezionare Crea](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Nel riquadro **Configurazione app** > **Crea** immettere le impostazioni seguenti:

    | Impostazione | Valore consigliato | Descrizione |
    |---|---|---|
    | **Nome risorsa** | Nome globalmente univoco | Immettere un nome di risorsa univoco da usare per la risorsa archivio di Configurazione app. Il nome deve essere una stringa contenente da 1 a 63 caratteri che possono includere solo numeri, lettere e il carattere `-`. Il nome non può iniziare o terminare con il carattere `-` e i caratteri `-` consecutivi non sono validi.  |
    | **Sottoscrizione** | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per testare Configurazione app. Se l'account ha una sola sottoscrizione, questa viene selezionata automaticamente e l'elenco **Sottoscrizione** non viene visualizzato. |
    | **Gruppo di risorse** | *AppConfigTestResources* | Selezionare o creare un gruppo di risorse per la risorsa archivio di Configurazione app. Questo gruppo è utile per gestire risorse multiple: ad esempio è possibile eliminare più risorse con un'unica operazione, eliminando il gruppo di risorse. Per altre informazioni, vedere [Usare i gruppi di risorse per gestire le risorse di Azure](/azure/azure-resource-manager/resource-group-overview). |
    | **Posizione** | *Stati Uniti centrali* | Usare **Posizione** per specificare la posizione geografica in cui viene ospitato l'archivio di configurazione app. Per ottenere prestazioni ottimali, creare le risorse nella stessa area degli altri componenti dell'applicazione. |

    ![Creare una risorsa archivio di Configurazione app](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Selezionare **Create** (Crea). La distribuzione può richiedere alcuni minuti.

1. Al termine, selezionare **Impostazioni** > **Chiavi di accesso**. Prendere nota della stringa di connessione della chiave primaria di sola lettura. Questa stringa di connessione verrà usata in seguito per configurare l'applicazione in modo da comunicare con l'archivio di Configurazione app creato.
