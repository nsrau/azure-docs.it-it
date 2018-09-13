---
title: Tramite il portale di Azure Stack | Microsoft Docs
description: Informazioni su come accedere e usare il portale utenti in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: mabrigg
ms.reviewer: efemmano
ms.openlocfilehash: 7ccf746cfd9645b63c2810bff83cfb0c4fa55d65
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713740"
---
# <a name="use-the-azure-stack-portal"></a>Usa il portale di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

È possibile usare il portale di Azure Stack per sottoscrivere le offerte pubbliche e usare i servizi che forniscono queste offerte. Se è stato usato il portale di Azure globale, ha già familiarità con il funzionamento del sito.

## <a name="access-the-portal"></a>Accedere al portale

L'operatore di Azure Stack (un provider del servizio o un amministratore dell'organizzazione), sarà possibile conoscere l'URL corretto per accedere al portale.

- Per un sistema integrato, varia a seconda dell'operatore area e nome di dominio esterno, l'URL e sarà nel formato https://portal.&lt; *area geografica*&gt;.&lt; *FQDN*&gt;.
- Se si usa Azure Stack Development Kit, l'indirizzo del portale è https://portal.local.azurestack.external.

![Cattura di schermata del portale utenti Azure Stack](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set predefinito di riquadri. È possibile selezionare **modifica dashboard** per modificare il dashboard predefinito o selezionare **nuovo dashboard** per creare un dashboard personalizzato. È possibile personalizzare con facilità un dashboard aggiungendo o rimuovendo i riquadri. Per aggiungere un riquadro del calcolo, ad esempio, selezionare **+ crea una risorsa**. Fare doppio clic su **Compute**, quindi selezionare **Aggiungi al dashboard**.

## <a name="create-subscription-and-browse-available-resources"></a>Creare sottoscrizioni ed esplorare le risorse disponibili

Se non si ha già una sottoscrizione, la prima cosa che è necessario eseguire è sottoscrivere un'offerta. Successivamente, è possibile esplorare le risorse disponibili. Per esplorare e creare le risorse, usare uno degli approcci seguenti:

- Selezionare il **Marketplace** riquadro nel dashboard.
- Nel **tutte le risorse** riquadro, seleziona **creano risorse**.
- Nel riquadro di spostamento a sinistra, selezionare **+ crea una risorsa**.

## <a name="learn-how-to-use-available-services"></a>Informazioni su come usare servizi disponibili

Se è necessario indicazioni su come usare i servizi disponibili, potrebbero esserci diverse opzioni disponibili.

- Un'organizzazione o un provider di servizi può fornire la propria documentazione, che in genere è il caso se offrono servizi personalizzati o App.
- Le app di terze parti hanno la propria documentazione.
- Per i servizi coerenti con Azure, è consigliabile rivedere innanzitutto la documentazione di Azure Stack. Per accedere alla documentazione di utente di Azure Stack, selezionare l'icona della Guida e quindi selezionare **Guida e supporto**.

    ![Guida e supporto tecnico di opzione nell'interfaccia utente](media/azure-stack-use-portal/HelpAndSupport.png)

    In particolare, è consigliabile rivedere gli articoli seguenti per iniziare:

    - [Considerazioni sulla chiave: uso dei servizi o creazione di App per Azure Stack](azure-stack-considerations.md)
    - Nel **usare i servizi** sezione della documentazione, è presente un articolo di considerazioni per ogni servizio. La pagina considerazioni descrive le differenze tra il servizio disponibile in Azure e lo stesso servizio disponibile in Azure Stack. Per un esempio, vedere [considerazioni sulle VM](azure-stack-vm-considerations.md). Potrebbero esserci altre informazioni nel **usare i servizi** sezione univoco in Azure Stack.

      È possibile usare la documentazione di Azure come riferimento generale per un servizio, ma occorre tenere presenti queste differenze. Comprendere che collega la documentazione sul **esercitazioni introduttive** riquadro punto alla documentazione di Azure.

## <a name="get-support"></a>Supporto

Se ti serve supporto, contattare il provider di servizio o dell'organizzazione per assistenza.

Se si usa Azure Stack Development Kit, il [forum di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) è l'unica fonte di supporto.

## <a name="next-steps"></a>Passaggi successivi

[Considerazioni sulla chiave: uso dei servizi o creazione di App per Azure Stack](azure-stack-considerations.md)
