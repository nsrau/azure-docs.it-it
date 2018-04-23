---
title: Creare un profilo e un endpoint della rete CDN di Azure | Microsoft Docs
description: Questa guida introduttiva illustra come abilitare la rete CDN di Azure creando un nuovo profilo e un nuovo endpoint della rete CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/13/2018
ms.author: mazha
ms.custom: mvc
ms.openlocfilehash: 6237b47be878217115849b87ebcd3d980665643a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Guida introduttiva: Creare un profilo e un endpoint della rete CDN di Azure
In questa guida introduttiva si abilita la rete per la distribuzione di contenuti (rete CDN) di Azure creando un nuovo profilo e un nuovo endpoint della rete CDN. Dopo aver creato un profilo e un endpoint, è possibile avviare la distribuzione di contenuti ai clienti.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti
Ai fini di questa guida introduttiva è necessario aver creato un account di archiviazione denominato *mystorageacct123*, che verrà usato per il nome host dell'origine. Per altre informazioni, vedere [Integrare un account di archiviazione di Azure con la rete CDN di Azure](cdn-create-a-storage-account-with-cdn.md)

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure
Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint della rete CDN

Dopo aver creato un profilo di rete CDN, è possibile usarlo per creare un endpoint.

1. Nel portale di Azure selezionare nel dashboard il profilo di rete CDN creato. Se il profilo non è immediatamente visibile, selezionare **Tutti i servizi** e quindi **Profili CDN**. Nella pagina **Profili CDN** selezionare il profilo da usare. 
   
    Verrà visualizzata la pagina del profilo di rete CDN.

2. Selezionare **Endpoint**.
   
    ![Profilo di rete CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Verrà visualizzata la pagina **Aggiungere un endpoint**.

    Usare le impostazioni specificate nella tabella disponibile sotto l'immagine.
   
    ![Riquadro Aggiungi un endpoint](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

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
    
3. Selezionare **Aggiungi** per creare il nuovo endpoint.
   
   Dopo la creazione, l'endpoint verrà visualizzato nell'elenco di endpoint per il profilo.
    
   ![Endpoint della rete CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Dato che la propagazione della registrazione richiede tempo, l'endpoint non è disponibile immediatamente per l'uso. 


## <a name="clean-up-resources"></a>Pulire le risorse
Nei passaggi precedenti sono stati creati un profilo e un endpoint della rete CDN in un gruppo di risorse. Salvare queste risorse se si vuole continuare con i [passaggi successivi](#next-steps) e ottenere informazioni su come aggiungere un dominio personalizzato all'endpoint. Se tuttavia non si prevede di usare queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse, evitando così costi aggiuntivi:

1. Dal menu a sinistra nel portale di Azure scegliere **Gruppi di risorse** e quindi selezionare **my-resource-group-123**.

2. Nella pagina **Gruppo di risorse** selezionare **Elimina gruppo di risorse**, immettere *my-resource-group-123* nella casella di testo e quindi selezionare **Elimina**.

    Con questa azione si elimineranno il gruppo di risorse, il profilo e l'endpoint creati in questa guida introduttiva.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sull'aggiunta di un dominio personalizzato all'endpoint della rete CDN, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](cdn-map-content-to-custom-domain.md)


