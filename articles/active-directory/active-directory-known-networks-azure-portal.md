---
title: Reti denominate in Azure Active Directory | Documentazione Microsoft
description: "Configurando le reti denominate si evita che indirizzi IP di proprietà dell&quot;organizzazione generino la creazione di falsi positivi per gli accessi da più aree geografiche e gli accessi da indirizzi IP con eventi di rischio di attività sospetta."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Reti denominate in Azure Active Directory

> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-known-networks-azure-portal.md)
> * [portale di Azure classico](active-directory-known-networks.md)
>
>


Azure Active Directory crea un record per ogni [evento di rischio](active-directory-identity-protection-risk-events.md) rilevato. Le informazioni sugli eventi di rischio disponibili nei report di sicurezza di Azure Active Directory permettono di ottenere dati dettagliati sulla probabile presenza di account utente compromessi nell'ambiente.   

È possibile che Azure Active Directory rilevi falsi positivi dei [tipi di eventi di rischio](active-directory-reporting-risk-events.md#risk-event-types) di *trasferimento impossibile a posizioni atipiche* e di *accessi da indirizzi IP con attività sospetta* di proprietà effettiva dell'organizzazione. 

Questo può avvenire, ad esempio, nei casi seguenti:

- L'accesso di un utente dell'ufficio di Boston in modalità remota al data center di San Francisco genera un evento di rischio di *accessi da più aree geografiche*

- Un utente dell'organizzazione prova ad accedere più volte con una password non corretta, generando un evento di rischio di *accessi da indirizzi IP con attività sospetta*

Per impedire che casi come questi generino eventi di rischio fuorvianti, è necessario aggiungere gli intervalli di indirizzi IP denominati all'elenco di indirizzi IP pubblici dell'organizzazione.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Per aggiungere gli intervalli di indirizzi IP pubblici dell'organizzazione, seguire questa procedura:

1. Accedere al portale di gestione di Azure.

2. Nel riquadro di sinistra fare clic su **Active Directory**.

    ![Reti note](./media/active-directory-known-networks-azure-portal/01.png)

3. Nella sezione **Gestione** del pannello della directory fare clic su **Reti denominate**.

    ![Reti note](./media/active-directory-known-networks-azure-portal/02.png)


4. Fare clic su **Aggiungi percorso**.

    ![Reti note](./media/active-directory-known-networks-azure-portal/03.png)

5. Nel pannello **Aggiungi** seguire questa procedura:

    ![Reti note](./media/active-directory-known-networks-azure-portal/04.png)

    a. Nella casella di testo **Nome** digitare un nome.

    b. Nella casella di testo **Intervallo IP** digitare un intervallo IP. L'intervallo IP deve essere in formato CIDR. Per l'aggiornamento in blocco, è possibile caricare un file CSV con gli intervalli IP. Un caricamento aggiunge gli intervalli IP nel file all'elenco invece di sovrascrivere l'elenco.

    c. Fare clic su **Crea**.


**Risorse aggiuntive:**

* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)
* [Accessi da indirizzi IP con attività sospetta](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Accessi da più aree geografiche](active-directory-reporting-sign-ins-from-multiple-geographies.md)

