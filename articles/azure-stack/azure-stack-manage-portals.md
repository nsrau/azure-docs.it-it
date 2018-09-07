---
title: Tramite il portale dell'amministratore in Azure Stack | Microsoft Docs
description: Un operatore di Azure Stack, informazioni su come usare il portale dell'amministratore.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: cea59d061dadfa3e10330cbce7b6005a286524cc
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024167"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Tramite il portale dell'amministratore in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Sono disponibili due portali in Azure Stack. il portale dell'amministratore e il portale utenti (talvolta detto il *tenant* portale.) Un operatore di Azure Stack, è possibile usare il portale dell'amministratore per la gestione quotidiana e operazioni di Azure Stack.

## <a name="access-the-administrator-portal"></a>Accedere al portale di amministrazione

Un ambiente di sviluppo kit, è necessario verificare innanzitutto che sia possibile [connettersi all'host del kit di sviluppo](azure-stack-connect-azure-stack.md) tramite connessione Desktop remoto o tramite una rete privata virtuale (VPN).

Per accedere al portale di amministratore, passare al portale di accesso usando le credenziali di un operatore di Azure Stack e URL. Per un sistema integrato, il portale che varia URL basato sul nome di area e nome di dominio completo esterno (FQDN) della distribuzione di Azure Stack.

| Environment | URL portale amministratore |   
| -- | -- | 
| Kit di sviluppo| https://adminportal.local.azurestack.external  |
| Sistemi integrati | https://adminportal.&lt; *regione*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Il portale dell'amministratore](media/azure-stack-manage-portals/admin-portal.png)

Nel portale di amministrazione, è possibile eseguire operazioni, ad esempio:

* Gestire l'infrastruttura (inclusi integrità del sistema, gli aggiornamenti, capacità e così via).
* Popolare Marketplace
* Creare sottoscrizioni per gli utenti
* Creare offerte e piani

Il **esercitazione introduttiva** riquadro vengono forniti collegamenti alla documentazione online per le attività più comuni.

Anche se ha un operatore può creare risorse quali macchine virtuali, reti virtuali e gli account di archiviazione nel portale di amministrazione, dovrebbe [accedere al portale per gli utenti](user/azure-stack-use-portal.md) per creare e testare le risorse.

>[!NOTE]
>Il **creare una macchina virtuale** collegamento nella sezione dell'esercitazione Guida introduttiva contiene è creare una macchina virtuale nel portale di amministrazione, ma solo allo scopo di convalidare Azure Stack dopo la prima distribuzione.

## <a name="understand-subscription-behavior"></a>Comprendere il comportamento di sottoscrizione

È presente una sola sottoscrizione disponibile per l'uso nel portale dell'amministratore. Questa sottoscrizione è il *sottoscrizione del Provider predefinito*. È possibile aggiungere tutte le altre sottoscrizioni e usarle nel portale di amministrazione.

Un operatore di Azure Stack, è possibile aggiungere sottoscrizioni per gli utenti (inclusi manualmente) dal portale di amministratore. Gli utenti (compreso te stesso) possono accedere e utilizzare queste sottoscrizioni dal **utente** portale. Tuttavia, il portale per gli utenti non fornisce accesso a qualsiasi funzionalità amministrative o operative del portale di amministratore.

I portali di amministratore e utente sono supportati da istanze separate di Azure Resource Manager. A causa di questa separazione di Resource Manager, le sottoscrizioni non sono valide tra i portali. Ad esempio, se, come un operatore di Azure Stack, accedi al portale utenti, sarà possibile accedere la *sottoscrizione del Provider predefinito*. Anche se non si ha accesso a tutte le funzioni amministrative, è possibile creare sottoscrizioni per se stessi dal offerte disponibili a pubbliche. Fino a quando si è connessi al portale utenti si è considerati un utente del tenant.

  >[!NOTE]
  >Nell'ambiente di kit di sviluppo, se un utente appartiene alla stessa directory tenant come l'operatore di Azure Stack, essi non sono impediti l'accesso al portale di amministrazione. Tuttavia, non possono accedere tutte le funzioni amministrative. Inoltre, dal portale di amministratore, non possono aggiungere accesso o le sottoscrizioni offerte che sono disponibili nel portale per gli utenti.

## <a name="administrator-portal-tips"></a>Suggerimenti per gli amministratori del portale

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set di riquadri predefiniti. È possibile selezionare **modifica dashboard** per modificare il dashboard predefinito o selezionare **nuovo dashboard** per aggiungere un dashboard personalizzato. È facilmente possibile aggiungere riquadri a un dashboard. Ad esempio, è possibile selezionare **New**, fare doppio clic su **offre + piani**, quindi selezionare **Aggiungi al dashboard**.

### <a name="quick-access-to-online-documentation"></a>Accesso rapido alla documentazione online

Per accedere alla documentazione di operatore di Azure Stack, usare la Guida e supporto di icona (punto interrogativo) nell'angolo superiore destro del portale di amministratore. Spostare il cursore sull'icona e quindi selezionare **Guida e supporto**.

### <a name="quick-access-to-help-and-support"></a>Accesso rapido alla assistenza e supporto

Se si seleziona l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore destro del portale di amministratore e quindi selezionare **nuova richiesta di supporto**, che si verifichi una dei risultati seguenti:

- Se si usa un sistema integrato, questa azione apre un sito in cui è possibile aprire direttamente un ticket di supporto con Microsoft dei clienti supporto tecnico clienti Microsoft. Fare riferimento a [come ottenere supporto](azure-stack-manage-basics.md#where-to-get-support) comprendere quando deve passare attraverso il supporto tecnico Microsoft o attraverso il supporto dei fornitori di hardware (OEM) original equipment manufacturer.
- Se si utilizza il kit di sviluppo, questa azione apre direttamente sul sito dei forum Azure Stack. Questi forum vengono regolarmente monitorati. Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale offerto tramite Microsoft CSS.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione area nello Stack di Azure](azure-stack-region-management.md)
