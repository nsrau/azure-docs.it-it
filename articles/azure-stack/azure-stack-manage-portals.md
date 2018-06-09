---
title: Tramite il portale dell'amministratore nello Stack di Azure | Documenti Microsoft
description: Come operatore Azure Stack, informazioni su come utilizzare il portale dell'amministratore.
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
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248521"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Tramite il portale dell'amministratore in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Sono disponibili due portali nello Stack di Azure; il portale dell'amministratore e il portale per gli utenti (talvolta detto il *tenant* portal.) Come operatore di Stack di Azure, è possibile utilizzare il portale dell'amministratore per la gestione quotidiana e operazioni dello Stack di Azure.

## <a name="access-the-administrator-portal"></a>Accedere al portale di amministrazione

Per un ambiente di kit di sviluppo, è necessario assicurarsi innanzitutto che è possibile [connettersi all'host del kit di sviluppo](azure-stack-connect-azure-stack.md) tramite connessione Desktop remoto o una rete privata virtuale (VPN).

Per accedere al portale di amministrazione, passare al portale di accesso con le credenziali di un operatore di Stack di Azure e URL. Per un sistema integrato, il portale che varia URL basato sul nome di area ed esterno nome di dominio completo (FQDN) della distribuzione di Azure Stack.

| Environment | URL del portale di amministrazione |   
| -- | -- | 
| Kit di sviluppo| https://adminportal.local.azurestack.external  |
| Sistemi integrati | https://adminportal.&lt; *regione*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Il portale dell'amministratore](media/azure-stack-manage-portals/image1.png)

Nel portale di amministrazione, è possibile eseguire operazioni, ad esempio:

* gestire l'infrastruttura (compresi integrità del sistema, gli aggiornamenti, capacità e così via).
* Popolare Marketplace
* creare sottoscrizioni per gli utenti
* creare i piani e le offerte

Il **esercitazione di avvio rapido** riquadro vengono forniti collegamenti alla documentazione online per le attività più comuni.

Anche se dispone di un operatore può creare risorse, ad esempio macchine virtuali, reti virtuali e gli account di archiviazione nel portale di amministrazione, è necessario [accedere al portale per gli utenti](user/azure-stack-use-portal.md) per creare e testare le risorse.

>[!NOTE]
>Il **creare una macchina virtuale** collegamento nel riquadro esercitazioni delle Guide rapide è creare una macchina virtuale nel portale di amministrazione, ma ciò è destinato solo a convalidare Azure Stack dopo la prima distribuzione.

## <a name="understand-subscription-behavior"></a>Comprendere il comportamento di sottoscrizione

È disponibile una sola sottoscrizione disponibile per l'utilizzo nel portale dell'amministratore. Questa sottoscrizione è il *predefinito sottoscrizione Provider*. Impossibile aggiungere altre sottoscrizioni e usarle nel portale di amministrazione.

Come operatore di Stack di Azure, è possibile aggiungere sottoscrizioni per gli utenti (incluso l'autore) il portale dell'amministratore. Utente () possono accedere e utilizzare queste sottoscrizioni dal **utente** portale. Tuttavia, il portale per gli utenti non fornisce accesso a qualsiasi delle funzionalità amministrative o operative del portale dell'amministratore.

I portali di amministratore e utente sono supportati da istanze separate di gestione risorse di Azure. A causa di questa separazione di gestione risorse, sottoscrizioni superino i portali. Ad esempio, se è, come un operatore di Stack di Azure, accedere al portale per gli utenti, sarà possibile accedere il *sottoscrizione Provider predefinito*. Anche se non si ha accesso alle funzioni amministrative, è possibile creare sottoscrizioni per se stessi dal offerte pubbliche disponibili. Come è effettuato l'accesso al portale per gli utenti si è considerati un utente di tenant.

  >[!NOTE]
  >Nell'ambiente di kit di sviluppo, se un utente appartiene nella stessa directory tenant come l'operatore di Stack di Azure, non sono bloccati dall'accesso al portale di amministrazione. Tuttavia, non possono accedere a tutte le funzioni amministrative. Inoltre, dal portale dell'amministratore non possono aggiungere sottoscrizioni o l'accesso sono disponibili che sono disponibili nel portale per gli utenti.

## <a name="administrator-portal-tips"></a>Suggerimenti di amministratore del portale

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set di riquadri predefiniti. È possibile selezionare **modificare il dashboard** per modificare il dashboard predefinito o selezionare **nuovo dashboard** per aggiungere un dashboard personalizzato. È possibile aggiungere facilmente i riquadri a un dashboard. Ad esempio, è possibile selezionare **New**, fare doppio clic su **offre + piani**, quindi selezionare **Aggiungi al dashboard**.

### <a name="quick-access-to-online-documentation"></a>Accedere rapidamente alla documentazione in linea

Per accedere alla documentazione di operatore dello Stack di Azure, utilizzare la Guida e supporto tecnico di icona (punto interrogativo) nell'angolo superiore sinistro del portale dell'amministratore. Spostare il cursore sull'icona e quindi selezionare **Guida e supporto**.

### <a name="quick-access-to-help-and-support"></a>Accesso rapido a Guida e supporto tecnico

Se si seleziona l'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale dell'amministratore e quindi selezionare **nuova richiesta di assistenza**, che si verifichi una dei risultati seguenti:

- Se si utilizza un sistema integrato, questa azione apre un sito in cui è possibile aprire direttamente un ticket di supporto con Microsoft cliente il supporto tecnico clienti Microsoft. Fare riferimento a [come ottenere supporto](azure-stack-manage-basics.md#where-to-get-support) comprendere quando è necessario accedere tramite il supporto tecnico Microsoft o il supporto dei fornitori di hardware original equipment manufacturer (OEM).
- Se si utilizza il kit di sviluppo, questa azione apre il sito dei forum di Azure Stack direttamente. Questi forum regolarmente vengono monitorati. Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale di Microsoft CSS.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione area nello Stack di Azure](azure-stack-region-management.md)
