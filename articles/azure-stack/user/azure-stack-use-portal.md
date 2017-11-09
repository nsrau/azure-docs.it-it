---
title: Tramite il portale di Azure Stack | Documenti Microsoft
description: Informazioni su come accedere e utilizzare il portale per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 63c270affca31d3db7e03116f5e287d8569b0dae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-azure-stack-portal"></a>Tramite il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come un consumer di servizi di Azure Stack, è possibile utilizzare il portale di Azure Stack per sottoscrivere l'offerta pubblica e utilizzare i servizi che vengono resi disponibili tramite tali offerte. Se è stato usato il portale di Azure prima, si ha già familiarità con l'interfaccia utente.

## <a name="access-the-portal"></a>Accedere al portale

L'operatore di Stack di Azure (un provider del servizio o un amministratore dell'organizzazione), sarà possibile conoscere l'URL corretto per accedere al portale. 

- Per un sistema integrato, l'URL varia in base sulla regione dell'operatore e il nome di dominio esterno e si trova il https://portal formato. &lt; *area*&gt;.&lt; *FQDN*&gt;.
- Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

![Schermata del portale utenti Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set di riquadri predefiniti. È possibile fare clic su **modificare il dashboard** per modificare il dashboard predefinito o fare clic su **nuovo dashboard** aggiungere dashboard personalizzato. Facilmente, è possibile aggiungere riquadri al dashboard. Ad esempio, è possibile fare clic su **New**, fare doppio clic su **calcolo**, quindi fare clic su **Aggiungi al dashboard**.

## <a name="create-subscription-and-browse-available-resources"></a>Creare sottoscrizioni ed esplorare le risorse disponibili
 
Se si dispone già di una sottoscrizione, la prima cosa che è necessario eseguire è sottoscrivere un'offerta. Successivamente, è possibile esplorare le risorse disponibili. Per esplorare e creare le risorse, effettuare una delle operazioni seguenti:

- Fare clic su di **Marketplace** riquadro nel dashboard. 
- Nel **tutte le risorse** riquadro, fare clic su **creare risorse**.
- Nel riquadro di spostamento a sinistra, fare clic su **New**.

## <a name="learn-how-to-use-available-services"></a>Informazioni su come utilizzare i servizi disponibili

Se sono necessarie informazioni aggiuntive sull'utilizzo di servizi disponibili, è possibile diverse opzioni disponibili.

- Un'organizzazione o un provider di servizi possono fornire propria documentazione. Ciò vale soprattutto se vengono offerti servizi personalizzati o App.
- App di terze parti dispone della propria documentazione.
- Per i servizi Azure coerenti, è consigliabile consultare la documentazione di Azure Stack. Per accedere alla documentazione utente dello Stack di Azure, fare clic sull'icona della Guida e quindi fare clic su **Guida e supporto**.
 
    ![Schermata dell'opzione di Guida e supporto tecnico nell'interfaccia utente](media/azure-stack-use-portal/HelpAndSupport.png)

    In particolare, si consiglia di consultare gli articoli seguenti per iniziare:

    - [Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
    - Nella sezione "Servizi" della documentazione, si noterà ogni servizio di Azure-coerente elencato. È un argomento "considerazioni" per ogni servizio, che descrive i delta tra il servizio offerto in Azure e lo stesso servizio offerto nello Stack di Azure. Per un esempio, vedere [considerazioni VM](azure-stack-vm-considerations.md). Potrebbe esserci altre informazioni nella sezione "Servizi" univoco allo Stack di Azure. 
     
      È possibile utilizzare la documentazione di Azure come riferimento generale per un servizio, ma è necessario essere a conoscenza di queste differenze. Comprendere che la documentazione di collegamenti del **esercitazioni delle Guide rapide** riquadro punto alla documentazione di Azure.

## <a name="get-support"></a>Supporto

Se è necessario ulteriore assistenza, contattare il provider dell'organizzazione o un servizio di assistenza. 

Se si utilizza il Kit di sviluppo dello Stack di Azure, il [forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) è l'unico metodo di supporto.

## <a name="next-steps"></a>Passaggi successivi

[Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
