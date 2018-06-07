---
title: Tramite il portale di Azure Stack | Documenti Microsoft
description: Informazioni su come accedere e utilizzare il portale per gli utenti nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ca29ee359349f69c3d5ff21bd9db3f93358206a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724313"
---
# <a name="use-the-azure-stack-portal"></a>Usa il portale di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

È possibile usare il portale di Azure Stack per effettuare la sottoscrizione pubblico e utilizzare i servizi che forniscono tali offerte. Se è stato usato il portale di Azure globale, si ha già familiarità con il funzionamento del sito.

## <a name="access-the-portal"></a>Accedere al portale

L'operatore di Stack di Azure (un provider del servizio o un amministratore dell'organizzazione), sarà possibile conoscere l'URL corretto per accedere al portale.

- Per un sistema integrato, l'URL varia in base area e il nome di dominio esterno dell'operatore e sarà nel formato https://portal.&lt; *area*&gt;.&lt; *FQDN*&gt;.
- Se si usa il Kit di sviluppo dello Stack di Azure, l'indirizzo del portale è https://portal.local.azurestack.external.

![Cattura di schermata del portale utenti Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set predefinito di riquadri. È possibile selezionare **modificare il dashboard** per modificare il dashboard predefinito o selezionare **nuovo dashboard** per creare un dashboard personalizzato. È possibile personalizzare facilmente un dashboard aggiungendo o rimuovendo i riquadri. Ad esempio, per aggiungere un riquadro di calcolo, selezionare **New**. Fare doppio clic su **calcolo**, quindi selezionare **Aggiungi al dashboard**.

## <a name="create-subscription-and-browse-available-resources"></a>Creare sottoscrizioni ed esplorare le risorse disponibili

Se non si dispone già di una sottoscrizione, la prima cosa da eseguire è sottoscrivere un'offerta. Successivamente, è possibile esplorare le risorse disponibili. Per esplorare e creare le risorse, usare uno degli approcci seguenti:

- Selezionare il **Marketplace** riquadro nel dashboard di.
- Nel **tutte le risorse** riquadro, seleziona **creare risorse**.
- Nel riquadro di spostamento a sinistra, selezionare **New**.

## <a name="learn-how-to-use-available-services"></a>Informazioni su come utilizzare i servizi disponibili

Se sono necessarie informazioni aggiuntive sull'utilizzo di servizi disponibili, è possibile diverse opzioni disponibili.

- L'organizzazione o un provider di servizi possono fornire le proprie documentazione, che avviene in genere se gli indici ColumnStore offrono servizi personalizzati o App.
- App di terze parti dispone della propria documentazione.
- Per i servizi Azure coerenti, è consigliabile consultare la documentazione di Azure Stack. Per accedere alla documentazione utente dello Stack di Azure, selezionare l'icona della Guida, quindi **Guida e supporto**.

    ![Guida e supporto tecnico di opzione nell'interfaccia utente](media/azure-stack-use-portal/HelpAndSupport.png)

    In particolare, si consiglia di consultare gli articoli seguenti per iniziare:

    - [Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
    - Nel **usare i servizi** sezione della documentazione, è presente un articolo di considerazioni per ogni servizio. La pagina considerazioni vengono descritte le differenze tra il servizio offerto in Azure e lo stesso servizio offerto nello Stack di Azure. Per un esempio, vedere [considerazioni VM](azure-stack-vm-considerations.md). Potrebbero essere presenti altre informazioni il **usare i servizi** sezione univoco allo Stack di Azure.

      È possibile utilizzare la documentazione di Azure come riferimento generale per un servizio, ma è necessario essere a conoscenza di queste differenze. Comprendere che la documentazione di collegamenti del **esercitazioni delle Guide rapide** riquadro punto alla documentazione di Azure.

## <a name="get-support"></a>Supporto

Se è necessaria assistenza, rivolgersi al servizio dell'organizzazione o un servizio per la Guida.

Se si usa il Kit di sviluppo dello Stack di Azure, il [forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) è l'unica origine di supporto.

## <a name="next-steps"></a>Passaggi successivi

[Considerazioni sulla chiave: utilizzo di servizi o creazione di applicazioni per Azure Stack](azure-stack-considerations.md)
