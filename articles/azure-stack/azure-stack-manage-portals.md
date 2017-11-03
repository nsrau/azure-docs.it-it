---
title: Tramite il portale dell'amministratore nello Stack di Azure | Documenti Microsoft
description: Come operatore Azure Stack, informazioni su come utilizzare il portale dell'amministratore.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>Tramite il portale dell'amministratore in Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Sono disponibili due portali nello Stack di Azure; il portale dell'amministratore e il portale utente (talvolta detto di *tenant* portale). Come operatore di Stack di Azure, è possibile utilizzare il portale dell'amministratore per la gestione quotidiana e operazioni dello Stack di Azure. 

## <a name="access-the-administrator-portal"></a>Accedere al portale di amministrazione

Per un ambiente di kit di sviluppo, è necessario assicurarsi innanzitutto che è possibile [connettersi all'host del kit di sviluppo](azure-stack-connect-azure-stack.md) tramite connessione Desktop remoto o una rete privata virtuale (VPN).

Per accedere al portale di amministrazione, passare al portale di accesso con le credenziali di un operatore di Stack di Azure e URL. Per un sistema integrato, il portale che varia URL basato sul nome di area ed esterno nome di dominio completo (FQDN) della distribuzione di Azure Stack.

| Environment | URL del portale di amministrazione |   
| -- | -- | 
| Kit di sviluppo| https://adminportal.Local.azurestack.External  |
| Sistemi integrati | https://adminportal. &lt; *area*&gt;.&lt; *FQDN*&gt; | 
| | |

 ![Il portale dell'amministratore](media/azure-stack-manage-portals/image1.png)

Nel portale di amministrazione, è possibile eseguire operazioni, ad esempio:

* gestire l'infrastruttura (compresi integrità del sistema, gli aggiornamenti, capacità e così via).
* Popolare il marketplace
* creare i piani e le offerte
* creare sottoscrizioni per gli utenti

Nel **esercitazione rapida** riquadro, sono disponibili collegamenti a documentazione in linea per attività più comuni.
 
Anche se non esiste la possibilità per un operatore creare risorse, ad esempio macchine virtuali, reti virtuali e gli account di archiviazione nel portale di amministrazione, è necessario [accedere al portale per gli utenti](user/azure-stack-use-portal.md) per creare e testare le risorse. (Il **creare una macchina virtuale** collegamento nel riquadro esercitazioni delle Guide rapide è creare una macchina virtuale nel portale di amministrazione, ma questa procedura è solo per convalidare Azure Stack dopo la distribuzione iniziale.)

## <a name="subscription-behavior"></a>Comportamento della sottoscrizione
 
È disponibile solo una sottoscrizione che è disponibile nel portale di amministrazione. Questa sottoscrizione è il *predefinito sottoscrizione Provider*. È possibile aggiungere sottoscrizioni per l'utilizzo nel portale di amministrazione.

Come operatore di Stack di Azure, è possibile aggiungere sottoscrizioni per gli utenti (incluso l'autore) il portale dell'amministratore. Utente () possono accedere e utilizzare le sottoscrizioni dal portale per gli utenti. Portale per gli utenti non fornisce accesso a qualsiasi funzionalità amministrative o operative del portale di amministrazione.

I portali di amministratore e utente sono supportati da istanze separate di gestione risorse di Azure. Separazione di gestione delle risorse, sottoscrizioni superino i portali. Ad esempio, se è un operatore di Stack Azure accede al portale per gli utenti, sarà possibile accedere alla sottoscrizione di Provider predefinito. Di conseguenza, non è necessario l'accesso alle funzioni amministrative. È possibile creare sottoscrizioni per se stessi dall'offerta pubblica, ma si è considerati un utente di tenant.

  >[!NOTE]
  Nell'ambiente di kit di sviluppo, se un utente appartiene nella stessa directory tenant come l'operatore di Stack di Azure, non sono bloccati dall'accesso al portale di amministrazione. Tuttavia, non possono accedere a tutte le funzioni amministrative. Inoltre, il portale dell'amministratore non possono aggiungere sottoscrizioni o accesso offre che vengono rese disponibili nel portale per gli utenti.

## <a name="administrator-portal-tips"></a>Suggerimenti di amministratore del portale

### <a name="customize-the-dashboard"></a>Personalizzare il dashboard

Il dashboard contiene un set di riquadri predefiniti. È possibile fare clic su **modificare il dashboard** per modificare il dashboard predefinito o fare clic su **nuovo dashboard** aggiungere dashboard personalizzato. Facilmente, è possibile aggiungere riquadri al dashboard. Ad esempio, è possibile fare clic su **nuovo**, fare doppio clic su **offre + piani**, quindi fare clic su **Aggiungi al dashboard**.

### <a name="quick-access-to-online-documentation"></a>Accedere rapidamente alla documentazione in linea

Per accedere alla documentazione di Azure Stack operatore, fare clic sull'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale di amministrazione e quindi fare clic su **Guida e supporto**.

### <a name="quick-access-to-help-and-support"></a>Accesso rapido a Guida e supporto tecnico

Se fai clic sull'icona della Guida e supporto (punto interrogativo) nell'angolo superiore sinistro del portale di amministrazione e quindi fare clic su **nuova richiesta di assistenza**, si verificheranno i seguenti:

- Se si utilizza un sistema integrato, questa azione apre un sito in cui è possibile aprire direttamente un ticket di supporto con Microsoft cliente il supporto tecnico clienti Microsoft. Vedere la sezione "Come ottenere supporto" di [nozioni fondamentali di amministrazione di Azure Stack](azure-stack-manage-basics.md) comprendere quando è necessario accedere tramite il supporto tecnico Microsoft o il supporto dei fornitori di hardware (OEM) original equipment manufacturer.
- Se si utilizza il kit di sviluppo, questa azione apre il sito dei forum di Azure Stack direttamente. Questi forum regolarmente vengono monitorati. Poiché il kit di sviluppo è un ambiente di valutazione, non è previsto alcun supporto ufficiale di Microsoft CSS.

## <a name="next-steps"></a>Passaggi successivi

- [Gestione area nello Stack di Azure](azure-stack-region-management.md)
