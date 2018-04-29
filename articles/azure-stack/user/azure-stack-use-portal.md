---
title: Tramite il portale di Azure Stack | Documenti Microsoft
description: Informazioni su come accedere e utilizzare il portale per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mabrigg
ms.openlocfilehash: 279722cc53889cb0a261fcffde0c7e0f86be6dc5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="using-the-azure-stack-portal"></a>Tramite il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come un consumer di servizi di Azure Stack, è possibile usare il portale di Azure Stack per effettuare la sottoscrizione pubblico e utilizzare i servizi che forniscono tali offerte. Se è stato usato il portale di Azure prima, si ha già familiarità con l'interfaccia utente.

## <a name="access-the-portal"></a>Accedere al portale

L'operatore di Stack di Azure (un provider del servizio o un amministratore dell'organizzazione), sarà possibile conoscere l'URL corretto per accedere al portale.

- Per un sistema integrato, l'URL varia in base area e il nome di dominio esterno dell'operatore e sarà nel formato https://portal.&lt; *regione*&gt;.&lt; *FQDN*&gt;.
- Se si utilizza il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

![Schermata del portale utenti Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set predefinito di riquadri. È possibile fare clic **modificare il dashboard** per modificare il dashboard predefinito o fare clic su **nuovo dashboard** per creare un dashboard personalizzato. È possibile personalizzare facilmente un dashboard aggiungendo o rimuovendo i riquadri. Ad esempio, per aggiungere un riquadro di calcolo, fare clic su **New**. Fare doppio clic su **calcolo**, quindi fare clic su **Aggiungi al dashboard**.

## <a name="create-subscription-and-browse-available-resources"></a>Creare sottoscrizioni ed esplorare le risorse disponibili
 
Se si dispone già di una sottoscrizione, la prima cosa che è necessario eseguire è sottoscrivere un'offerta. Successivamente, è possibile esplorare le risorse disponibili. Per esplorare e creare le risorse, usare uno degli approcci seguenti:

- Fare clic su di **Marketplace** riquadro nel dashboard.
- Nel **tutte le risorse** riquadro, fare clic su **creare risorse**.
- Nel riquadro di spostamento a sinistra, fare clic su **New**.

## <a name="learn-how-to-use-available-services"></a>Informazioni su come utilizzare i servizi disponibili

Se sono necessarie informazioni aggiuntive sull'utilizzo di servizi disponibili, è possibile diverse opzioni disponibili.

- L'organizzazione o un provider di servizi possono fornire le proprie documentazione, che avviene in genere se gli indici ColumnStore offrono servizi personalizzati o App.
- App di terze parti dispone della propria documentazione.
- Per i servizi Azure coerenti, è consigliabile consultare la documentazione di Azure Stack. Per accedere alla documentazione utente dello Stack di Azure, fare clic sull'icona della Guida e quindi fare clic su **Guida e supporto**.
 
    ![Schermata dell'opzione di Guida e supporto tecnico nell'interfaccia utente](media/azure-stack-use-portal/HelpAndSupport.png)

    In particolare, si consiglia di consultare gli articoli seguenti per iniziare:

    - [Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
    - Nella sezione "Servizi" della documentazione, è presente un articolo "considerazioni" per ogni servizio. La pagina "considerazioni" descrive le differenze tra il servizio offerto in Azure e lo stesso servizio offerto nello Stack di Azure. Per un esempio, vedere [considerazioni VM](azure-stack-vm-considerations.md). Potrebbe esserci altre informazioni nella sezione "Servizi" univoco allo Stack di Azure.
     
      È possibile utilizzare la documentazione di Azure come riferimento generale per un servizio, ma è necessario essere a conoscenza di queste differenze. Comprendere che la documentazione di collegamenti del **esercitazioni delle Guide rapide** riquadro punto alla documentazione di Azure.

## <a name="get-support"></a>Supporto

Se è necessario ulteriore supporto, rivolgersi al servizio dell'organizzazione o un servizio di assistenza.

Se si usa il Kit di sviluppo dello Stack di Azure, il [forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) è l'unica origine di supporto.

## <a name="next-steps"></a>Passaggi successivi

[Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
