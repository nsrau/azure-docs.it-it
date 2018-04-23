---
title: File di inclusione
description: File di inclusione
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>Creare un nuovo profilo di rete CDN

Un profilo di rete CDN è un contenitore per endpoint della rete CDN e specifica un piano tariffario.

1. In alto a sinistra nel portale di Azure selezionare **Crea una risorsa**.
    
    Viene visualizzato il riquadro **Nuovo**.
   
2. Selezionare **Web e dispositivi mobili**, quindi **Rete CDN**.
   
    ![Selezionare una risorsa della rete CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Viene visualizzato il riquadro **Profilo rete CDN**.

    Usare le impostazioni specificate nella tabella disponibile sotto l'immagine.
   
    ![Nuovo profilo di rete CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Impostazione  | Valore |
    | -------- | ----- |
    | **Nome** | Immettere *my-cdn-profile-123* per il nome del profilo. Il nome deve essere univoco a livello globale. Se è già in uso, è possibile immettere un nome diverso. |
    | **Sottoscrizione** | Selezionare una sottoscrizione di Azure dall'elenco a discesa.|
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere *my-resource-group-123* per il nome del gruppo di risorse. Il nome deve essere univoco a livello globale. Se è già in uso, è possibile immettere un nome diverso. | 
    | **Località del gruppo di risorse** | Selezionare **Stati Uniti centrali** dall'elenco a discesa. |
    | **Piano tariffario** | Selezionare **Verizon standard** dall'elenco a discesa. |
    | **Crea un nuovo endpoint rete CDN ora** | Non selezionare alcun elemento. |  
   
3. Selezionare **Aggiungi al dashboard** per salvare il profilo nel dashboard dopo la creazione.
    
4. Selezionare **Crea** per creare il profilo. 

