---
title: Tramite il portale di amministrazione in Azure Stack | Microsoft Docs
description: Un operatore di Azure Stack, informazioni su come usare il portale di amministrazione.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817933"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Guida introduttiva: usare il portale di amministrazione di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Sono disponibili due portali in Azure Stack. nel portale di amministrazione e il portale utenti (talvolta detto il *tenant* portale.) Come operatore, Stack di Azure è usare il portale di amministrazione per la gestione quotidiana e operazioni di Azure Stack.

## <a name="access-the-administrator-portal"></a>Accedere al portale di amministrazione

Per accedere al portale di amministratore, passare al portale di accesso usando le credenziali di un operatore di Azure Stack e URL. Per un sistema integrato, il portale che varia URL basato sul nome di area e nome di dominio completo esterno (FQDN) della distribuzione di Azure Stack. Nel portale di amministrazione URL è sempre lo stesso per le distribuzioni di Azure Stack Development Kit (ASDK). 

| Environment | URL portale amministratore |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Sistemi integrati | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> Per un ambiente ASDK, è necessario verificare innanzitutto che sia possibile [connettersi all'host del kit di sviluppo](azure-stack-connect-azure-stack.md) tramite connessione Desktop remoto o tramite una rete privata virtuale (VPN).

 ![Nel portale di amministrazione](media/azure-stack-manage-portals/admin-portal.png)

Il fuso orario predefinito per tutte le distribuzioni di Azure Stack è impostato a Coordinated Universal Time (UTC). 

Nel portale di amministrazione, è possibile eseguire operazioni come:

* [Registrare Azure Stack con Azure](azure-stack-registration.md)
* [Popolare marketplace](azure-stack-download-azure-marketplace-item.md)
* [Creare piani, offerte e sottoscrizioni per gli utenti](azure-stack-plan-offer-quota-overview.md)
* [Monitorare l'integrità e gli avvisi](azure-stack-monitor-health.md)
* [Gestire gli aggiornamenti di Azure Stack](azure-stack-updates.md)

Il **esercitazione introduttiva** riquadro vengono forniti collegamenti alla documentazione online per le attività più comuni.

Anche se un operatore può creare risorse quali macchine virtuali, reti virtuali e gli account di archiviazione nel portale di amministrazione, dovrebbe [accedere al portale per gli utenti](user/azure-stack-use-portal.md) per creare e testare le risorse.

>[!NOTE]
>Il **creare una macchina virtuale** collegamento nella sezione dell'esercitazione Guida introduttiva contiene è creare una macchina virtuale nel portale di amministrazione, ma solo allo scopo di verificare che Azure Stack è stato distribuito correttamente.

## <a name="understand-subscription-behavior"></a>Comprendere il comportamento di sottoscrizione

Esistono tre sottoscrizioni create per impostazione predefinita nel portale di amministrazione; consumo di provider predefinito e di controllo. Un operatore, si userà principalmente il *sottoscrizione del Provider predefinito*. È possibile aggiungere tutte le altre sottoscrizioni e usarle nel portale di amministrazione. 

Altre sottoscrizioni create dagli utenti nel portale per gli utenti in base i piani e offerte create per loro. Tuttavia, il portale per gli utenti non fornisce accesso a qualsiasi funzionalità amministrative o operative del portale di amministrazione.

I portali di amministrazione e l'utente sono supportati da istanze separate di Azure Resource Manager. A causa di questa separazione di Azure Resource Manager, le sottoscrizioni non sono valide tra i portali. Ad esempio, se, come un operatore di Azure Stack, accedi al portale utenti, sarà possibile accedere la *sottoscrizione del Provider predefinito*. Anche se non si ha accesso a tutte le funzioni amministrative, è possibile creare sottoscrizioni per se stessi dal offerte disponibili a pubbliche. Fino a quando si è connessi al portale utenti si è considerati un utente del tenant.

  >[!NOTE]
  >In un ambiente ASDK, se un utente appartiene alla stessa directory tenant come l'operatore di Stack di Azure, non sono bloccati dall'accesso al portale di amministrazione. Tuttavia, questi non possono accedere tutte le funzioni amministrative o aggiungere sottoscrizioni per accedere a offerte che sono disponibili nel portale per gli utenti.

## <a name="administration-portal-tips"></a>Suggerimenti del portale di amministrazione

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set di riquadri predefiniti. È possibile selezionare **modifica dashboard** per modificare il dashboard predefinito o selezionare **nuovo dashboard** per aggiungere un dashboard personalizzato. È facilmente possibile aggiungere riquadri a un dashboard. Ad esempio, è possibile selezionare **+ crea una risorsa**, fare doppio clic su **offre + piani**, quindi selezionare **Aggiungi al dashboard**.

In alcuni casi, è possibile visualizzare un dashboard vuoto nel portale. Per ripristinare il dashboard, fare clic su **modifica Dashboard**, quindi fare doppio clic e selezionare **predefinite reimpostati**.

### <a name="quick-access-to-online-documentation"></a>Accesso rapido alla documentazione online

Per accedere alla documentazione di Azure Stack operatore, utilizzare la Guida e supporto icona (punto interrogativo) nell'angolo superiore destro del portale di amministratore. Spostare il cursore sull'icona e quindi selezionare **Guida e supporto**.

### <a name="quick-access-to-help-and-support"></a>Accesso rapido alla assistenza e supporto

Se si seleziona l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore destro del portale di amministratore e quindi selezionare **nuova richiesta di supporto**, si verifica una delle seguenti risultati:

- Se si usa un sistema integrato, questa azione apre un sito in cui è possibile aprire direttamente un ticket di supporto con Microsoft dei clienti supporto tecnico clienti Microsoft. Fare riferimento a [come ottenere supporto](azure-stack-manage-basics.md#where-to-get-support) comprendere quando deve passare attraverso il supporto tecnico Microsoft o attraverso il supporto dei fornitori di hardware (OEM) original equipment manufacturer.
- Se si usa il ASDK, questa azione apre la [sito dei forum di Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) direttamente. Questi forum vengono regolarmente monitorati. Poiché il ASDK è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft CSS.

### <a name="quick-access-to-the-azure-roadmap"></a>Accesso rapido per la roadmap per Azure

Se si seleziona **Guida in linea e supporto** (il punto interrogativo) nell'angolo superiore destro dell'amministrazione del portale e quindi seleziona **roadmap per Azure**, una nuova scheda del browser si apre e consente di visualizzare la roadmap per Azure. Digitando **Azure Stack** nel **prodotti** casella di ricerca, è possibile visualizzare tutti gli aggiornamenti della roadmap per Azure Stack.

## <a name="next-steps"></a>Passaggi successivi

[Registrazione in Azure Stack Azure](azure-stack-registration.md) e popolare il [marketplace Azure Stack](azure-stack-marketplace.md) con elementi da offrire agli utenti. 
