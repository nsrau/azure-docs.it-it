---
title: Guida introduttiva - Creare un profilo e un endpoint della rete CDN di Azure | Microsoft Docs
description: Questa guida introduttiva illustra come abilitare la rete CDN di Azure creando un nuovo profilo e un nuovo endpoint della rete CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/24/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b1e287ee949aedf084d4390fe672f0fe41899c34
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091766"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Guida introduttiva: Creare un profilo e un endpoint della rete CDN di Azure
In questa guida introduttiva si abilita la rete per la distribuzione di contenuti (rete CDN) di Azure creando un nuovo profilo e un nuovo endpoint della rete CDN. Dopo aver creato un profilo e un endpoint, è possibile avviare la distribuzione di contenuti ai clienti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti
Ai fini di questa guida introduttiva è necessario aver creato un account di archiviazione denominato *mystorageacct123*, che verrà usato per il nome host dell'origine. Per altre informazioni, vedere [Integrare un account di archiviazione di Azure con la rete CDN di Azure](cdn-create-a-storage-account-with-cdn.md).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint della rete CDN

Dopo aver creato un profilo di rete CDN, è possibile usarlo per creare un endpoint.

1. Nel portale di Azure selezionare nel dashboard il profilo di rete CDN creato. Se il profilo non è immediatamente visibile, selezionare **Tutti i servizi** e quindi **Profili CDN**. Nella pagina **Profili CDN** selezionare il profilo da usare. 
   
    Verrà visualizzata la pagina del profilo di rete CDN.

2. Selezionare **Endpoint**.
   
    ![Profilo di rete CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Verrà visualizzato il riquadro **Aggiungi un endpoint**.

3. Per le impostazioni dell'endpoint, usare i valori specificati nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Nome** | Immettere *my-endpoint-123* come nome host dell'endpoint. Il nome deve essere univoco a livello globale. Se è già in uso, è possibile immettere un nome diverso. Questo nome viene usato per accedere alle risorse memorizzate nella cache nel dominio _&lt;nome endpoint&gt;_.azureedge.net.|
    | **Tipo di origine** | Selezionare **Archiviazione**. | 
    | **Nome host dell'origine** | Immettere *mystorageacct123.blob.core.windows.net* per il nome host. Il nome deve essere univoco a livello globale. Se è già in uso, è possibile immettere un nome diverso. |
    | **Percorso dell'origine** | Lasciare vuoto. |
    | **Intestazione host dell'origine** | Lasciare il valore predefinito generato. |  
    | **Protocollo** | Lasciare selezionate le opzioni **HTTP** e **HTTPS** predefinite. |
    | **Porta dell'origine** | Lasciare i valori di porta predefiniti. | 
    | **Ottimizzato per** | Lasciare la selezione predefinita, ovvero **Distribuzione Web generale**. |

    ![Riquadro Aggiungi un endpoint](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Selezionare **Aggiungi** per creare il nuovo endpoint.
   
   Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint per il profilo.
    
   ![Endpoint della rete CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Dato che la propagazione della registrazione richiede tempo, l'endpoint non è immediatamente disponibile per l'uso: 
   - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
   - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
   - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata entro 90 minuti. 

## <a name="clean-up-resources"></a>Pulire le risorse
Nei passaggi precedenti sono stati creati un profilo e un endpoint della rete CDN in un gruppo di risorse. Salvare queste risorse se si vuole continuare con i [passaggi successivi](#next-steps) e ottenere informazioni su come aggiungere un dominio personalizzato all'endpoint. Se tuttavia non si prevede di usare queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse, evitando così costi aggiuntivi:

1. Dal menu a sinistra nel portale di Azure scegliere **Gruppi di risorse** e quindi selezionare **my-resource-group-123**.

2. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**, immettere *my-resource-group-123* nella casella di testo e quindi selezionare **Elimina**.

    Con questa azione si elimineranno il gruppo di risorse, il profilo e l'endpoint creati in questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'aggiunta di un dominio personalizzato all'endpoint della rete CDN, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md)


